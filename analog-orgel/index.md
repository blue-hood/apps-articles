---
title: マイコンを使わないアナログ式電子オルゴールを作った
description: デジタル全盛のいま、あえてマイコンを使わずアナログな電子オルゴールを作りました。
thumbnail: /storage/articles/images/e7c0bb07.jpg
---

<picture>
  <source type="image/webp" srcset="/storage/articles/images/e7c0bb07.webp">
  <img src="/storage/articles/images/e7c0bb07.jpg">
</picture>

皆さんこんにちは。
今回は、私が 1 年がかりで作った「アナログ式電子オルゴール」の紹介をしたいと思います。

<ol class="table-of-contents"></ol>

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- ディスプレイ広告 -->
<!-- textlint-disable -->

<ins class="adsbygoogle"
    style="display:block"
    data-ad-client="ca-pub-7008780049786244"
    data-ad-slot="5063315418"
    data-ad-format="auto"
    data-full-width-responsive="true"></ins>

<!-- textlint-enable -->
<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>

## 動作風景

※音量小さいです。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">回路のまとめ作業が終わったので、次は3Dモデル編になります。 <a href="https://t.co/cWoV34qFTe">pic.twitter.com/cWoV34qFTe</a></p>&mdash; hata (@bluehood_admin) <a href="https://twitter.com/bluehood_admin/status/1139909993702948864?ref_src=twsrc%5Etfw">June 15, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## 元ネタはピアノロール

私が中学生のころ、クラシックなミッキーマウスのアニメーションを見るのが好きだったのですが、そこに[ピアノロール](https://ja.wikipedia.org/wiki/%E3%83%94%E3%82%A2%E3%83%8E%E3%83%AD%E3%83%BC%E3%83%AB)
が出ていました。これは、ピアノの演奏情報を時系列的に記録した紙媒体であり、穴が空いているところに対応する鍵盤が自動的に押されるしくみのようです。

これをもとに、幅 40 mm の紙テープで演奏データを記録することにしました。
本家のピアノロールは、鍵盤 1 つずつに 1 ライン使うため非常に幅の広い紙テープが必要になります。
そこで、音階（ドレミファソラシドと#）を 4 ビット、オクターブを 3 ビット、ノートオン／オフを 1 ビットの合計 8 ビットで表現しています。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/62859724.webp">
  <img src="/storage/articles/images/62859724.jpg">
</picture>

## 回路図

画像形式と .CE3 形式で配布します。
.CE3 を [BSch3V](https://www.suigyodo.com/online/schsoft.htm) で開けば高解像で確認できるためお勧めします。
なお、パスコンなどのノイズ対策や電源の振動対策を行っておらず、自己責任にてお願いします。

### 電源

15V の AC アダプタからレールスプリッタで ±7.5V 電源を生成、プッシュプルで大電流対応。
そのほか、NJM4580DD オペアンプの動作電圧を作ったり、デジタル用+5V を三端子レギュレータで作っています。

- [CE3 ファイル](/storage/articles/AnalogOrgel/PowerSupply.CE3)

<img src="/storage/articles/images/df10230b.png">

### 紙テープ巻取回路

紙テープを一定の速度で巻き取ります。
モータの速度をフィードバックで制御していますが、なんと速度センサもまたモータ。
紙テープにローラーを当ててモータの軸を回し、微弱な起電圧を 300 倍に増幅します。
そして、半固定抵抗によって定めた基準電圧とコンパレータによって比較し、
紙テープを巻き取るモータをオン／オフします。
モータは激しいノイズ源になりそうですので、3V 電池を使って電源を隔離しています。

- [CE3 ファイル](/storage/articles/AnalogOrgel/TapeRoller.CE3)

<img src="/storage/articles/images/4d87a9a1.png">

### 紙テープ読取回路

黒を 1、白を 0 のデジタル信号として、8 ビット幅の紙テープをフォトリフレクタで読み取ります。
1 ビットはノートオン信号として、これが Hi のときに残りの 7 ビットを読み取るようにします。

- [CE3 ファイル](/storage/articles/AnalogOrgel/TapeReader.CE3)

<img src="/storage/articles/images/69b2f114.png">

### 音階デコーダ

音階信号（ドレミファソラシドと半音）からオクターブ 4 の周波数信号を生成します。
音階に対応する周波数は半固定抵抗で調整する必要があります。
また、周波数出力 `Vfreq4` はインピーダンスありの負電圧となります（オクターブデコーダとの兼ね合いのため）。

- [CE3 ファイル](/storage/articles/AnalogOrgel/ScaleDecoder.CE3)

<img src="/storage/articles/images/7742b6a2.png">

### オクターブデコーダ

オクターブ 4 の周波数信号をもとに、オクターブ 0〜7 に変調します。
オクターブ 0 は実質 0V を出力することになります。
こちらも、各オクターブごとに半固定抵抗で周波数の調整が必要です。

- [CE3 ファイル](/storage/articles/AnalogOrgel/OctaveDecoder.CE3)

<img src="/storage/articles/images/63245516.png">

### エンベロープ回路

音量を変化させて減衰を表現します。
単純なコンデンサの充電放電と、分圧回路ですね。
なお、コンデンサの急放電によるストレスは無視。

- [CE3 ファイル](/storage/articles/AnalogOrgel/Envelope.CE3)

<img src="/storage/articles/images/b4215aeb.png">

### ノコギリ波 VCO&VCA

周波数と音量を電圧として入力するとノコギリ波が生成されます。
周波数については入力電圧との線形性が成り立ちますが、振幅（音量）については J-FET の特性の影響を受けます。

- [CE3 ファイル](/storage/articles/AnalogOrgel/SawWave.CE3)

<img src="/storage/articles/images/fcf3dc8e.png">

## 3D モデル

紙テープ読取装置の 3D モデルです。
3D プリンタで印刷し、接着剤で組み立てればできあがります。
なお、.stl 形式と .rsdoc 形式で配布しますが、[DesignSpark Mechanical](https://www.rs-online.com/designspark/mechanical-software-jp)
で .rsdoc を開けば印刷用に部品が分割されていますのでお勧めします。

- [紙テープ読取装置の .rsdoc ファイル](/storage/articles/AnalogOrgel/AnalogOrgel.rsdoc)

### モータ用ロール

幅 40mm の紙テープを 130 モータとギアボックスで巻き取ります。

- [STL ファイル](/storage/articles/AnalogOrgel/Roll.stl)
  <img src="/storage/articles/images/176f7440.png">

### 手巻き用ロール

こちらは手巻き用のノブがついています。

- [STL ファイル](/storage/articles/AnalogOrgel/Roll2.stl)
  <img src="/storage/articles/images/77907bad.png">

### ベース

手前から順に、ギアボックス、速度センサ、紙テープの土台です。

- [STL ファイル](/storage/articles/AnalogOrgel/Base.stl)
  <img src="/storage/articles/images/4c4a9198.png">

### ヘッド

格子 1 つにフォトリフレクタ LBR-127HLD が 2 つ入ります。
格子を 4 つ用意したので、8 ビット分の並列データ読み込みができます。

- [STL ファイル](/storage/articles/AnalogOrgel/Head.stl)
  <img src="/storage/articles/images/4d909bc4.png">

### モータホルダー

FA-130RA モータを固定します。

- [STL ファイル](/storage/articles/AnalogOrgel/MotorHolder.stl)
  <img src="/storage/articles/images/d299a05c.png">

## その他

市販のパーツの紹介です。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">アナログ式電子オルゴールのまとめ作業、既存品の紹介です。紙テープの巻き取りにはタミヤのシングルギヤボックス(<a href="https://t.co/w51rd8hAV2">https://t.co/w51rd8hAV2</a>)を使用しています。ギア比は一番低速の344.2:1です。その下には単3x2の電池ボックス(<a href="https://t.co/jKcs8ePmz0">https://t.co/jKcs8ePmz0</a>)を接着して高さを稼いでいます。 <a href="https://t.co/MswvqI2T0q">pic.twitter.com/MswvqI2T0q</a></p>&mdash; hata (@bluehood_admin) <a href="https://twitter.com/bluehood_admin/status/1140254877680984065?ref_src=twsrc%5Etfw">June 16, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">アナログ式電子オルゴールのまとめ作業、既存品の紹介２です。FA-130RAモーターにタイヤを付けてまさかの速度センサーにしています。タイヤはミニ四駆の小径ホイール(<a href="https://t.co/jbvedz7p5R">https://t.co/jbvedz7p5R</a>)に、ICの静電気保護用のスポンジを巻いて調整しています。 <a href="https://t.co/rxAOAp6Re0">pic.twitter.com/rxAOAp6Re0</a></p>&mdash; hata (@bluehood_admin) <a href="https://twitter.com/bluehood_admin/status/1140256469624819718?ref_src=twsrc%5Etfw">June 16, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">アナログ式電子オルゴールのまとめ作業、既存品の紹介３です。せっかくなのでイヤホン出力ではなく小型スピーカー(<a href="https://t.co/7R0spPkxYa">https://t.co/7R0spPkxYa</a>)にしました。アンプは簡単なキット(<a href="https://t.co/IoDVF4JGTF">https://t.co/IoDVF4JGTF</a>)、単3x2の電池ボックス(<a href="https://t.co/jKcs8ePmz0">https://t.co/jKcs8ePmz0</a>)。10kΩの抵抗を挟んで音量調整しています。 <a href="https://t.co/7lohz8iypP">pic.twitter.com/7lohz8iypP</a></p>&mdash; hata (@bluehood_admin) <a href="https://twitter.com/bluehood_admin/status/1140257922309447680?ref_src=twsrc%5Etfw">June 16, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
