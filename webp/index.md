---
title: webp 画像形式でファイルサイズが 92% 削減された
description: webp 画像形式をサイトに導入し、画像の転送負荷を大幅に削減する方法を紹介します。
thumbnail: /storage/articles/images/e18bc26b.jpg
---

<picture>
  <source type="image/webp" srcset="/storage/articles/images/e18bc26b.webp 1x, /storage/articles/images/d857a180.webp 2x">
  <img srcset="/storage/articles/images/e18bc26b.jpg 1x, /storage/articles/images/d857a180.jpg 2x">
</picture>

皆さんこんにちは 😀 　
私はいま作っているこのサイトでは、**シンプルかつこだわりをもって**あれこれやっているつもりです。
そのひとつとして、Google の [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/)というものを利用しています。
これはページの読み込み速度をスコアリングして、さらにスピードを改善するためのアドバイスもしてくれるすばらしいツールです。
ページスピードは Web 検索にも影響すると言われ、サイトを運営している方であれば多くの人が活用できるのではないでしょうか？
この記事では、読み込み速度改善の 1 つとして**Webp 画像形式の導入**について紹介したいと思います。

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

## まずはページスピードを測定

まずは自分の Web サイトの状況を確認するために、[PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/) を使います。
サイトの URL を入力して測定するだけで、数十秒後にはスコアが表示されると思います。
なお、PageSpeed Insights は**モバイル**と **PC** に分けて測定、スコアリングするため、両方確認する必要があります。

私のサイトでは、まず重要な箇所としてランディングページ (LP) の測定をしてみました。
LP には特にコンテンツもなく負荷が軽いので、スコア 90 点超えの良い結果となりました。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/730e4f28.webp">
  <img src="/storage/articles/images/730e4f28.png">
</picture>

しかし、記事一覧ページでは 80 点台という標準的な結果でした。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/3c502d96.webp">
  <img src="/storage/articles/images/3c502d96.png">
</picture>

こんなとき、Google はスピード改善のための提案をしてくれます。
そこに「次世代フォーマットでの画像の配信」というものがありました。
これは、Web サイトに埋め込まれている jpg や png などの形式の画像を、
jpeg2000 や Webp という画像形式に置き換えることで通信負荷が抑えられることを意味しています。
さらに、次世代フォーマットを導入することによるファイルサイズの削減量まで教えてくれるといういたわり尽くせリです。

<picture>
  <source type="image/webp" srcset="/storage/articles/images/e6e28656.webp">
  <img src="/storage/articles/images/e6e28656.png">
</picture>

## Webp 形式に変換する

というわけで、さっそく Web サイト中のあらゆる png, JPEG ファイルを Webp に変換してみました。
Ubuntu をお使いの方は、以下のコマンドで Webp に変換するコマンドをインストールできます。

<pre class="prettyprint">
$ sudo apt install webp
</pre>

そして、以下のコマンドで png, JPEG ファイルを一括で Webp ファイルに変換できます。
なお、`cwebp` コマンドは変換のクオリティがデフォルトで 80% に設定されています。
コマンドオプションを追加して、クオリティを変更することも可能です。

<pre class="prettyprint">
for path in *.png *.jpg ; do cwebp ${path} -o ${path%.*}.webp; done
</pre>

Webp 形式に変換した結果、どれくらいファイルサイズが削減されたか確認してみます。
シェルで頑張ってファイルサイズの一覧表示をしてみました。

<pre class="prettyprint">
$ for path in *.png *.jpg ;do echo `du -h ${path}`" -> "`du -h ${path%.*}.webp` "("$(( `wc -c < ${path%.*}.webp`*100/`wc -c < ${path}` ))"%)"; done
736K images/1d1900bc.png -> 32K images/1d1900bc.webp (4%)
644K images/c355feaf.png -> 32K images/c355feaf.webp (4%)
140K images/1814cc87.jpg -> 20K images/1814cc87.webp (13%)
944K images/21c8af6c.jpg -> 72K images/21c8af6c.webp (7%)
2.2M images/45e49d86.jpg -> 208K images/45e49d86.webp (9%)
328K images/5b213ef0.jpg -> 40K images/5b213ef0.webp (12%)
2.2M images/62fd5d1b.jpg -> 196K images/62fd5d1b.webp (8%)
360K images/7c38fe6d.jpg -> 56K images/7c38fe6d.webp (14%)
972K images/90f383dd.jpg -> 28K images/90f383dd.webp (2%)
132K images/a92fe036.jpg -> 8.0K images/a92fe036.webp (5%)
300K images/dc698745.jpg -> 36K images/dc698745.webp (11%)
2.2M images/e5b5e4ae.jpg -> 176K images/e5b5e4ae.webp (7%)
</pre>

ファイルサイズが 4%, 4%, 13%, ……に削減されており、平均すると 8%、つまり **92%** もサイズが削減されていることになります。
ちょっとこれだけサイズが減ってしまうと、正常に変換されているのか……？と不安になるレベルですね 😮 　
私が目視で確認したところだと、若干の劣化は感じるものの十分画像は保たれていますね。
なお、このページやサイトにも Webp を導入していますので、みなさんもぜひファイルサイズや画質を確認してみてください。

## 従来の画像形式と両対応する

とても効率が良い Webp 画像形式ですが、残念ながら現状では従来の png, JPEG 形式のファイルを残したまま両対応する必要があります。
というのも、一部のメジャーな Web ブラウザでは、Webp 画像形式に対応していないからです。

そこで、img タグではなく [picture タグを使用](https://blog.jxck.io/entries/2016-03-26/webp.html)して、従来の画像形式と Webp の両方をサイトに埋め込みます。
以下のようにすると、Webp 対応のブラウザでは Webp、非対応のブラウザでは JPEG 画像が読み込まれます。

<pre class="prettyprint linenums">
&lt;picture&gt;
  &lt;source type=&quot;image/webp&quot; srcset=&quot;/storage/articles/images/e18bc26b.webp 1x, /storage/articles/images/d857a180.webp 2x&quot;&gt;
  &lt;img srcset=&quot;/storage/articles/images/e18bc26b.jpg 1x, /storage/articles/images/d857a180.jpg 2x&quot;&gt;
&lt;/picture&gt;
</pre>

## PageSpeed Insights に頼ろう

Webp 形式の驚異的な圧縮効率には私も驚かされました。
中には 132kB の JPEG ファイルが 8.0kB になってしまうものもあり、もはやテキストファイルと同等になっているような気もします。
Webp 形式のことは PageSpeed Insights を通じて知ったのですが、PageSpeed ではそのほかさまざまな提案をしてくれます。
たとえば画像やビデオのキャッシュを長くするなど、まだまだ改善の余地はありそうです。
