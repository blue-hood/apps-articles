---
title: 【Git×Twitter】連携 &amp; 通知してコミットの質を上げよう
description: Git と Twitter の連携をサーバレスで導入する方法を紹介します。
thumbnail: /storage/articles/images/dc698745.jpg
---

<picture>
  <source type="image/webp" srcset="/storage/articles/images/dc698745.webp 1x, /storage/articles/images/e5b5e4ae.webp 2x">
  <img src="/storage/articles/images/dc698745.jpg" srcset="/storage/articles/images/e5b5e4ae.jpg 2x">
</picture>

いつも私は Git のコミットメッセージを適当に済ませてしまうのですが、`git push` するとツイートされるしくみを導入したらコミットメッセージの質が格段に上がりました。

導入前

<pre class="prettyprint lang-html">
・Translating JS to C++ ...
・Translating JS to C++ now...
・Translating JS to C++ ... libuuid used.
・Merge pull request #8 from blue-hood/twiemoji  …
・Use Twemoji to display same font each OS.
</pre>

導入後

<pre class="prettyprint lang-html">
・WebAssemblyのpthreadはcreateの数に上限があるらしいからお洋服買ってくる。
・Component は Sketch の unique_ptr にして高速化。for 文での参照渡しを徹底した。
・shared_ptr の循環参照を解消し、高速化のため weak_ptr ではなく生ポインタで代替しました。
・シミュレーション時は生ポインタと参照渡しを使って高速化した。
・C++ の高速化をする前にバックアップコミットします。
</pre>

（英語から日本語に変わっているのはさておき、）コミットメッセージの内容が具体的になりました。また、Twitter を意識してかちょっと感情的なところもちらほらありますね。このような Git と Twitter の連携を**サーバレスで**導入する方法を紹介します。

[インストール方法](#install)

2019/03/30
gitwitter のレアケース対策をしました。

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

## Webhook は意外と不便

Git と Twitter の連携でメジャーと思われるのは、[Webhook](https://www.google.co.jp/search?q=Webhook) を利用する方法です。GitHub や Bitbucket には、リポジトリへの push イベントごとに POST リクエストを投げてくれる機能があります。それを別途 Web サーバで拾ってツイートします。いわゆる[クラウドハブ](https://www.google.co.jp/search?q=%E3%82%AF%E3%83%A9%E3%82%A6%E3%83%89%E3%83%8F%E3%83%96)はこの機能を利用しているわけですね。

しかし、クラウドハブを利用するには月額料金がかかったり、無料だとしても月ごとの使用回数に制限があったりします。また自前で Webhook を拾うには、サーバが必要なのはもちろん HTTPS 通信をするためのドメインと証明書なども必要になります。Git の push をツイートするためだけに Webhook を利用するのは、ちょっとオーバーな気がします。

また、実際に私が Webhook を導入してみて思ったのは、ツイートするかを毎回制御できないため不便だということです。たとえば、タイポを修正するだけのささいな push に対してもツイートされてしまい、手動で無駄なツイートを消すということがありました。タイムラインを荒らしてしまったと思いますね。

## サーバレスで連携する

そこで、Webhook を使わずクライアントで動かす連携アプリケーションを作ることにしました。これならサーバレスだしツイートするかも毎回決めることができます。ツイートする文章はシェルスクリプトで `git log` を呼び出したりして組み立てました。ツイートするには [TwitterOAuth](https://twitteroauth.com/) を使って [Twitter REST API](https://developer.twitter.com/en/docs.html) をたたく PHP スクリプトを書きました。使い方はこんな感じです。

<pre class="prettyprint">
$ commit -m "gitwitter のテストです。"
$ gitwitter
</pre>

<h3 id="install">インストール</h3>
私が適当にスクラッチした `tweet` コマンドと `gitwitter` コマンドのインストール方法を紹介します。自分の GitHub アカウントでしかテストしてません、問題があったら [MIT ライセンス](https://ja.wikipedia.org/wiki/MIT_License)なので自由に改変しちゃってください。

### `tweet` コマンド

[GitHub: Hata6502/tweet](https://github.com/hata6502/tweet)

コマンドラインからツイートします。標準入力から読み込む汎用的な作りにしたので、Git との連携以外にも応用できるかもしれません。

<pre class="prettyprint">
$ echo "tweet コマンドのテストです。" | tweet
</pre>

(1) /usr/local/src などにダウンロードします。

<pre class="prettyprint">
# cd /usr/local/src
# git clone https://github.com/Hata6502/tweet
</pre>

(2) Composer で依存パッケージをインストールします。

<pre class="prettyprint">
# cd tweet
# composer install
</pre>

(3) Twitter API の設定をします。

<pre class="prettyprint">
# mv config.php.example config.php
# nano config.php
</pre>

(4) コマンドのシンボリックシンクを張ります。

<pre class="prettyprint">
# cd /usr/local/bin
# ln -s /usr/local/src/tweet/tweet
</pre>

### `gitwitter` コマンド

[Gist: Hata6502/gitwitter](https://gist.github.com/Hata6502/d3bb634ec9beb9443a040c90733284bd)

Git のコミットメッセージを収集してツイートし、`git push` します。直接 /usr/local/bin などに配置すればインストール完了です。push 先の URL などによって正常動作しない場合や、ツイート内容の調整をする場合は編集してください。引数を指定すると、そのまま `git push` の引数になります。

<script src="https://gist.github.com/Hata6502/d3bb634ec9beb9443a040c90733284bd.js"></script>

## `git` コマンドを使いこなすための参考資料集

`git` コマンドは[シェル芸](https://qiita.com/t_nakayama0714/items/bfe4852e0535858ee662)しやすいのでぜひ参考にしてみてください。
私自身 `git` コマンドは使いこなせていませんが……

- [さるまりんのガレージ: git でまだ push していない commit を確認する方法](https://salumarine.com/how-to-see-commits-not-pushed-to-the-origin-yet/)
- [Qiita: git log のフォーマットを指定する](https://qiita.com/harukasan/items/9149542584385e8dea75)
- [Qiita: ロカール git リポジトリでリモートのリポジトリ URL 確認方法](https://qiita.com/zhao-xy/items/a35add58575ef7d9d4dc)
- [すがブロ: git で現在のブランチ名を取得する](http://sugamasao.hatenablog.com/entry/2013/11/02/174311)
- [Qiita: git でハッシュ値を取得](https://qiita.com/quattro_4/items/55e99a2c008c6875f267)

## 余談

当初 `gitweet` コマンドという名前にしようと思いましたが、同名の Web サービスがすでにあったので `gitwitter` コマンドにしました 🙄
