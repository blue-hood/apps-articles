---
title: 面倒なファイル名は付けずに8文字IDで管理しよう
description: 特にファイル名をつける必要がないときは、自動で8文字IDに置き換えて管理しやすくする方法を紹介します。
thumbnail: /storage/articles/images/7c38fe6d.jpg
---

<picture>
  <source type="image/webp" srcset="/storage/articles/images/7c38fe6d.webp 1x,/storage/articles/images/45e49d86.webp 2x">
  <img src="/storage/articles/images/7c38fe6d.jpg" srcset="/storage/articles/images/45e49d86.jpg 2x">
</picture>

みなさんはこんな感じのファイル管理を見たことはないでしょうか？

- `レイアウト_トップ画面.xlsx`
- `レイアウト_登録画面.xlsx`
- `レイアウト_トップ画面_20190101 提出版.xlsx`
- `レイアウト_登録画面_20190101 提出版.xlsx`

バージョン管理システムが導入されていないオフィスにいたとき、作業用 PC の作業ディレクトリ内がこんな感じでした（笑）。
ちなみにいまはバージョン管理システムが導入されており、少し作業効率が改善されたのを覚えています。
とはいっても、バージョン管理システムもその人の使い方次第で ↑ のような状況になってしまうのですが……

Web サイトやプレゼンテーション資料を作るときなど、画像素材をいろんなところから収集してくるときがあります。
すると、ファイル名は「猫の画像 1.jpg」「ScreenShot20190101-1.png」「12de944eff….jpg」など、ファイル名の規則性がバラバラで意味を成さなくなります。
そんなときに、ファイル名を**8 文字 ID**に一括で置き換えて統一する方法を紹介します。

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

## `mvhash` コマンドを作ってみた

Linux や Mac をお使いであれば、以下のシェルスクリプトを導入することで 8 文字 ID へ変換できます。

<script src="https://gist.github.com/Hata6502/9ebb9a6ce2863e9498a5ebea3a3f5c6c.js"></script>

上記シェルスクリプトを `mvhash` というファイル名で /usr/localbin などのパスが通っているところに配置します。
そして、以下のようにすれば 8 文字 ID に変換されます。

<pre class="prettyprint">
$ ls
  82f231898694e893389f7fc7f0d4b2ae1ddfb69ed2d59295603593c8529db673.jpg
  ScreenShot20190101-1.png
  猫の画像1.jpg

$ mvhash *.jpg *.png
  82f231898694e893389f7fc7f0d4b2ae1ddfb69ed2d59295603593c8529db673.jpg -> fff32890.jpg
  猫の画像1.jpg -> 9018ac4f.jpg
  ScreenShot20190101-1.png -> 9ee84598.png

$ ls
  9018ac4f.jpg  9ee84598.png  fff32890.jpg
</pre>

せっかくですので、`mvhash` コマンドの解説をしていきたいと思います！💪

### コマンド引数で `while` ループ

<pre class="prettyprint linenums">
while [ $# -ne 0 ]
do
  # $1 を使った処理
  shift
done
</pre>

`$ mvhash *.jpg *.png` のようにワイルドカードを使ったコマンドを実行するとき、bash がワイルドカードの展開をします。
よって、`$ mvhash 猫の画像1.jpg ScreenShot20190101-1.png 82f23189….jpg` というコマンドを入力したとみなされます。
`mvhash` シェルスクリプトでは、与えられたコマンドライン引数ごとにループ処理をさせます。
`while` 文のループ条件は「引数が 0 でないとき」とし、`shift` コマンドによって**引数のシフト**というものをします。
これによって第一引数 `$1` が削除され、そのあとに続く引数が `$1`、`$2`、…となります。

<pre class="prettyprint linenums">
$ mvhash 猫の画像1.jpg ScreenShot20190101-1.png 82f23189….jpg  (引数3つ)
↓shift
$ mvhash ScreenShot20190101-1.png 82f23189….jpg  (引数2つ)
↓shift
$ mvhash 82f23189….jpg  (引数1つ)
↓shift
$ mvhash  (引数0つのためループ終了)
</pre>

よって、`while` ループの中で `$1` を使った処理を書けばよいということになります。

### すでに 8 文字 ID 化されている場合はスキップ

<pre class="prettyprint linenums">
if [[ $1 =~ ^[0-9a-f]{8}\. ]];  then
  shift
  continue
fi
</pre>

ファイル名を 8 文字 ID 化するために [crc32](https://ja.wikipedia.org/wiki/%E5%B7%A1%E5%9B%9E%E5%86%97%E9%95%B7%E6%A4%9C%E6%9F%BB)
というハッシュアルゴリズムを使用するため（後述）、ID は 0〜9 と a〜f の文字を使った 8 文字となります。
そして、8 文字 ID をさらに 8 文字 ID 化できてしまうため、これを防ぐためにスキップ処理を入れています。
`if [[ $1 =~ ^[0-9a-f]{8}\. ]]` によって、与えられたファイル名がすでに 8 文字 ID であるかを
[正規表現マッチ](https://ja.wikipedia.org/wiki/%E6%AD%A3%E8%A6%8F%E8%A1%A8%E7%8F%BE)して条件分岐します。

### `crc32` で 8 文字 ID を生成

<pre class="prettyprint">
hashed=`crc32 <(echo $1)`.${1##*.}
echo $1' -> '${hashed}
mv -i "$1" "${hashed}"
shift
</pre>

`crc32` コマンドは、指定したファイルの**内容**をもとに crc32 ハッシュを出力するコマンドです。
しかし、今回は指定したファイルの**名前**をもとに crc32 ハッシュを取得したいと思います。
そこで、bash の [`<()`](http://fj.hatenablog.jp/entry/2016/03/06/170907) という構文を使います。
これは、() 内に書いたコマンドによる標準出力を一時的なファイルとして展開する……というものです。

なかなか説明が難しいのでデバッグ出力をしてみました。
下記の `/dev/fd/63` に注目してください。
`/dev/fd/63` という仮想的なファイルには、`echo test.png` による標準出力、つまり「test.png」という文字列が書き込まれています。
これを `crc32` コマンドに送ることで、test.png の中身ではなくファイル名をもとに crc32 ハッシュを生成しています。

<pre class="prettyprint">
$ echo crc32 <(echo test.png)
  crc32 /dev/fd/63
</pre>

## （余談）「8 文字」ID にした理由

今回はファイル名を 8 文字の ID に置き換える方法を紹介しました。
実は私には**ファイル名は 8 文字！**という謎のこだわりを持っています 😤 　
というのは、だいぶ前にファイル名が 8 文字までしか使えない OS があったからです。
そのなごりからか、Linux のコマンド名も伝統的なものは 8 文字以内に収まっているような気がします。
とはいえさすがにもう 2019 年、`docker-compose` のように長いコマンド名も普通ですね。
無理やり 8 文字に収めようとすると、直感的でなくなってしまうような気もします。
