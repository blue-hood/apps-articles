---
title: 【PHPMD 対応】husky &amp; lint-staged で CI を実行する
description: CI をローカル環境に構築することで、サーバーレスでコードの品質を保つ方法を紹介します。
thumbnail: /storage/articles/images/2fa79dbd.jpg
---

<picture>
  <source type="image/webp" srcset="/storage/articles/images/2fa79dbd.webp 1x,/storage/articles/images/167bfe56.webp 2x">
  <img src="/storage/articles/images/2fa79dbd.jpg" srcset="/storage/articles/images/167bfe56.jpg 2x">
</picture>

みなさんこんにちは、今回は husky と lint-staged を使って CI 環境をローカルに構築する方法を紹介したいと思います。

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

## 継続的インテグレーション（CI）とは

[WikiPedia](https://ja.wikipedia.org/wiki/%E7%B6%99%E7%B6%9A%E7%9A%84%E3%82%A4%E3%83%B3%E3%83%86%E3%82%B0%E3%83%AC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3)<br>
継続的インテグレーション（CI）とは、ソフトウェア開発においてソースコードの品質や保守性を保ち、持続可能な開発を続けていくための方法です。
つまり、ほかの人が見ても理解しやすく、また自分が書いた古いコードでも処理内容が理解できるようにプログラミングしていくということですね。

個人の趣味のスクリプトであれば CI を導入する必要はないかもしれませんが、お仕事などでのチームでの開発では重要になると思います。
また GitHub で公開するものに対しても、CI を導入するとメリットが大きいと思います。

ソースコードの属人性（その人にしか分からないような書き方）を解消するために、開発ルールを定めることがあります。
たとえば、コーディングスタイルを定義したり（[Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) など）、コードレビューをするなどです。
これらは整形ツールやコーディングチェック（[lint](https://ja.wikipedia.org/wiki/Lint)）を使って自動化できます。

## サーバレスで CI を導入

CI を導入する方法として、CI サーバを立てる方法があります。
たとえば、GitHub では [Travis CI](https://travis-ci.org/) を導入することで、`git commit` 時に自動で CI を実行してくれます。
さらに、CI の実行結果を [GitHub のバッジ](https://qiita.com/dtan4/items/13b0ea9edf5b99926446) として公言でき、リポジトリの質を保証する 1 つの指標にできます。

しかし、CI サービスは有料プランのものもあり（Public リポジトリは無料も）、特に社内開発ではコストになる場合があります。
そこで、CI をローカル環境に導入し、`git commit` 時に自動でコーディングチェックと自動整形をしたいと思います。

## huskey と lint-staged を使うメリット

コミット時に CI スクリプトを実行するには [pre-commit](https://qiita.com/sue71/items/0b2f4a607e47c3095820) を自前で記述することも可能です。
しかし、ほかの人と共同開発する場合では手動で `pre-commit` および自動整形ツール（prettier や eslint）を導入する必要がありました。

そこで、husky を使って pre-commit をフックし、lint-staged でコーディングチェックと自動整形を実行させたいと思います。
リポジトリ直下に npm で `husky`、`lint-staged`、`prettier`、`eslint` などをインストールすれば、以降は `npm install` だけで CI 環境が整います。

## CI 環境の導入

npm で husky と lint-staged、prettier、eslint をインストールします。

<pre class="prettyprint">
$ npm install --save-dev husky lint-staged prettier eslint @prettier/plugin-php
</pre>

`.huskyrc.json` を用意し、pre-commit のフックに lint-staged を指定する設定を書きます。

<pre class="prettyprint linenums">
{
  "hooks": {
    "pre-commit": "lint-staged"
  }
}
</pre>

今度は `.lintstagedrc.json` を用意し、ファイルごとに実行する CI を定義します。

<pre class="prettyprint linenums">
{
  "linters": {
    "*.php": [
      "prettier --write",
      "git add"
    ],
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "git add"
    ],
    "*.{css,scss}": [
      "prettier --write",
      "git add"
    ],
    "*.{yaml,yml,md}": [
      "prettier --write",
      "git add"
    ]
  }
}
</pre>

最後に、prettier や eslint の設定ファイルを設置すれば完了です。
`.eslintrc.js` は `eslint --init` で作るのもよいと思います。

- .prettierrc

<pre class="prettyprint linenums">
{
  printWidth: 120,
  tabWidth: 2
}
</pre>

<pre class="prettyprint">
$ node_modules/.bin/eslint --init
</pre>

## CI を試してみる

試しに `test.php` を作ってみます。

<pre class="prettyprint linenums">
&lt;?php
  $test=1;
while(1){
          test();
}
</pre>

こんなやる気ナッシングなコードでも、`git add` してコミットすると、

<pre class="prettyprint">
$ git add .
$ git commit -m "PHP の prettier テスト。"
husky > pre-commit (node v8.9.1)
  ↓ Stashing changes... [skipped]
    → No partially staged files found...
  ✔ Running linters...
[master 78d95f6] PHP の prettier テスト。
 1 file changed, 4 insertions(+), 4 deletions(-)
</pre>

- test.php

<pre class="prettyprint linenums">
&lt;?php
$test = 1;
while (1) {
  test();
}
</pre>

きれいになりました。
JavaScript だと、

<pre class="prettyprint linenums">
const test = 0;
test = "This is const. ";
test;
</pre>

<pre class="prettyprint">
$ git add .
$ git commit -m "JavaaScript の eslint テスト。"
husky > pre-commit (node v8.9.1)
  ↓ Stashing changes... [skipped]
    → No partially staged files found...
  ❯ Running linters...
    ↓ Running tasks for *.php [skipped]
      → No staged files match *.php
    ❯ Running tasks for *.{js,jsx,ts,tsx}
      ✖ eslint --fix
        git add
    ↓ Running tasks for *.{css,scss} [skipped]
      → No staged files match *.{css,scss}
    ↓ Running tasks for *.{yaml,yml,md} [skipped]
      → No staged files match *.{yaml,yml,md}



✖ eslint --fix found some errors. Please fix them and try committing again.

/home/hato/repo/test.js
2:1  error  'test' is constant  no-const-assign

✖ 1 problem (1 error, 0 warnings)

husky > pre-commit hook failed (add --no-verify to bypass)
</pre>

このように `const` のエラーでコミットをやめてくれます。

## PHPMD を使うには？

PHP の静的解析ツールとして PHPMD が有名です。
スタイルチェックだけでなくプログラムの意味解析を行い、保守性を確保するためのアドバイスをしてくれます。
PHPMD のインストールには Composer を利用します。

<pre class="prettyprint">
$ composer require phpmd/phpmd
</pre>

肝心の phpmd を lint-staged で使う方法ですが……実は相性が悪いです。
まず、[phpmd のコマンド書式](https://github.com/phpmd/phpmd)より、次のようなコマンドで phpmd を実行できます。

<pre class="prettyprint">
$ phpmd test.php,test2.php text cleancode,codesize,controversial,design,naming,unusedcode
</pre>

このように、チェックする php ファイルを第 1 引数にカンマ区切りで指定します。
それに対し、[lint-staged](https://www.npmjs.com/package/lint-staged) では次のようにコマンドが展開されます。

<pre class="prettyprint">
$ phpmd test.php test2.php
</pre>

つまり、php ファイルをコマンド末尾にスペース区切りで指定してしまうのです。
そこで、phpmd と lint-staged をうまくつなぎ合わせるシェルスクリプトを書きます。

- .lintstaged-phpmd

<pre class="prettyprint linenums">
#!/bin/bash
targets=`echo "$@" | sed "s/ /,/g"`
vendor/bin/phpmd ${targets} text cleancode,codesize,controversial,design,naming,unusedcode
</pre>

- .lintstagedrc.json

<pre class="prettyprint linenums">
{
  "linters": {
    "*.php": [
      "prettier --write",
      "./.lintstaged-phpmd",
      "git add"
    ]
  }
}
</pre>

エラーが出る php ファイルを 2 つ用意して、

- test.php

<pre class="prettyprint linenums">
&lt;?php
$test = 1;
while (1) {
  test();
}
class not_camel_case
{
}
</pre>

- test2.php

<pre class="prettyprint linenums">
&lt;?php
class not_camel_case
{
}
</pre>

コミットしてみます。

<pre class="prettyprint">
$ git add .
$ chmod +x .lintstaged-phpmd	# 実行権限の付与を忘れずに！
$ git commit -m "PHP の PHPMD テスト。"
husky > pre-commit (node v8.9.1)
  ↓ Stashing changes... [skipped]
    → No partially staged files found...
  ❯ Running linters...
    ❯ Running tasks for *.php
      ✔ prettier --write
      ✖ ./.lintstaged-phpmd
        git add
    ↓ Running tasks for *.{js,jsx,ts,tsx} [skipped]
      → No staged files match *.{js,jsx,ts,tsx}
    ↓ Running tasks for *.{css,scss} [skipped]
      → No staged files match *.{css,scss}
    ↓ Running tasks for *.{yaml,yml,md} [skipped]
      → No staged files match *.{yaml,yml,md}



✖ ./.lintstaged-phpmd found some errors. Please fix them and try committing again.
/home/hato/repo/test.php:6	The class not_camel_case is not named in CamelCase.
/home/hato/repo/test2.php:2	The class not_camel_case is not named in CamelCase.
husky > pre-commit hook failed (add --no-verify to bypass)
</pre>

これで、フロントエンドとバックエンドの両方で CI が実行できますね。
もっと良い lint-staged と PHPMD の連携方法はないでしょうかね？
