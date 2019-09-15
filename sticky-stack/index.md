---
title: 大規模な作業のときは「付箋スタック」を積む
description: 仕事上での作業漏れをなくす「付箋スタック」を提案します。
thumbnail: /storage/articles/images/770a5abf.jpg
---

<picture>
  <source type="image/webp" srcset="/storage/articles/images/770a5abf.webp 1x,/storage/articles/images/4ed63954.webp 2x">
  <img src="/storage/articles/images/770a5abf.jpg" srcset="/storage/articles/images/4ed63954.jpg 2x">
</picture>

皆さんこんにちは。
今回は番外的な記事として、私個人で編み出した仕事術**ふせんスタック**について紹介したいと思います（笑）
おもしろ半分で読んでいただけると幸いです。

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

## 人間には把握の限界がある

まず最初に、人間には**把握の限界**があることを前提とします。
というのは、人間のワーキングメモリはコンピュータのように数 GB あるわけではなく、たいてい数単語分しかないということです。
[参考文献](https://ja.wikipedia.org/wiki/%E3%83%AF%E3%83%BC%E3%82%AD%E3%83%B3%E3%82%B0%E3%83%A1%E3%83%A2%E3%83%AA#%E3%83%AF%E3%83%BC%E3%82%AD%E3%83%B3%E3%82%B0%E3%83%A1%E3%83%A2%E3%83%AA%E3%81%AE%E5%AE%B9%E9%87%8F)
によると、人間のワーキングメモリは 7±2 チャンクの範囲だと言われています。
仕事などで大規模な作業を行うのに対して、短期記憶の使用は避けるべきだと言えます。

## 作業の影響は連鎖していく

当然のことではありますが、作業を行うとその影響を対応するために新しい作業が生まれていくことがあります。
たとえば、新しい元号へ対応するために`A`というシステムを改修した場合、`A`を利用しているシステム`B`、`C`、`D`も改修が必要になり、さらに……という連鎖が発生します。
このとき、見通しが浅く「平成の 2 文字を令和に置き換えるだけ」程度に考えてしまうと、その作業量の大きさに気付くことができません。

## 作業漏れを起こさないコンピュータの構造

これも当然ですが、コンピュータは指示された命令通りに漏れを起こすことなく作業を遂行しますね。
コンピュータが処理を漏れなく行う記憶構造として、**[スタック](https://ja.wikipedia.org/wiki/%E3%82%B9%E3%82%BF%E3%83%83%E3%82%AF)**があります。
これはコンピュータが現在何の処理をしているか、この処理が終わったら次は何をするか、といったプログラムの実行ステータスを記憶するためにも使われています。
これに似たような方法を取り入れれば、機械的ながらも作業漏れを減らすことができるのではないでしょうか？

## ふせんスタック

それではふせんスタックの方法を例をまじえながら紹介します。
まず、作業ステータスを記録するために**ふせん**を使います。
ふせんに今回のタスク例として「平成 → 令和移行」と書きます。
そして、これから行うタスク「平成 → 令和移行」に「←」マークを付けます。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/046f6b7f.webp">
  <img src="/storage/articles/images/046f6b7f.jpg">
</picture>

次に、「平成 → 令和移行」のタスクを完了するにはどんな作業が必要かを書き出します。
ここでは、「文字列置換」と「画像に埋め込まれた文字の置換」をする必要があるため、
この 2 つをふせんに書き出して**「平成 → 令和移行」の隣**に貼ります。
このようにして作業の階層構造を表現します。
また、「文字列置換」のタスクに「←」マークを付けます。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/9ffe2969.webp">
  <img src="/storage/articles/images/9ffe2969.jpg">
</picture>

「文字列置換」のタスクを完了するには、app/ ディレクトリ配下のファイルと
resource/ ディレクトリ配下のファイルを編集する必要があります。
この 2 つのディレクトリをふせんに書き出して「文字列置換」のタスクの隣に貼ります。
そして、resource/ 配下から着手するため「resource/ 配下」のタスクに「←」を書きます。
さらに、「平成 → 令和移行」のタスクを完了するには、西暦と和暦を変換するプログラムを改修する必要が出てきました。
そこで「西暦 → 令和変換実装」のふせんを追加します。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/196a5bc7.webp">
  <img src="/storage/articles/images/196a5bc7.jpg">
</picture>

resource/ 配下の文字列置換が完了したため、「resource/ 配下」のふせんを破棄します。
そして、次のタスク「app/ 配下」に「←」を付けます。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/d2368862.webp">
  <img src="/storage/articles/images/d2368862.jpg">
</picture>

app/ 配下の作業も終わったため、「app/ 配下」のふせんを破棄します。
これによって文字列置換のタスクもすべて完了したため、「文字列置換」のふせんも破棄します。
次に行うタスクを選び、ここでは「西暦 → 令和変換実装」を行うことにするため「←」マークを付けます。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/23603ab7.webp">
  <img src="/storage/articles/images/23603ab7.jpg">
</picture>

このようにタスクを消化していくことで、大きなタスクも詳細かつ全体を把握しながら遂行できます。

## 全体を把握しながら再帰的に

このような仕事術はいわゆる**[深さ優先](https://ja.wikipedia.org/wiki/%E6%B7%B1%E3%81%95%E5%84%AA%E5%85%88%E6%8E%A2%E7%B4%A2)**
といえます。しかし、仕事上ではタスクの工数（全体の作業量）を把握する必要が出てくるため、なるべく細かいタスクをふせんに貼り出して
全体を把握できるように心がけたいです。
ふせんスタックは個人の裁量内で使えるものですが、大きなタスクは [Backlog](https://backlog.com/ja/) などのツールを使って共有することもお勧めです。
