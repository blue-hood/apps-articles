---
title: 【CI】git commit 時に自動整形 & コーディングチェックする
description: 継続的インテグレーションをサーバーレスで実現する方法を紹介します。
thumbnail: /storage/articles/images/2fa79dbd.jpg
---

<picture>
  <source type="image/webp" srcset="/storage/articles/images/2fa79dbd.webp 1x,/storage/articles/images/167bfe56.webp 2x">
  <img src="/storage/articles/images/2fa79dbd.jpg" srcset="/storage/articles/images/167bfe56.jpg 2x">
</picture>

皆さんこんにちは。
プログラミングやコーディングを続けていくうちに、過去に自分が書いたコードの意味が分からなくなったりしたことはないでしょうか？
今回は、中〜大規模な開発をする際に重要な**サーバレスな継続的インテグレーション（CI）**について書きたいと思います。

[導入方法はこちら](#install)

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

個人の趣味のスクリプトであれば CI を導入する必要はないかもしれません。
しかし、お仕事などでのチームでの開発では重要になると思います。
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

## pre_commit フックを使う

Git には、コミット時などにスクリプトを実行するフック機能があります。
その 1 つに pre_commit というものがあり、これはコミットをする直前に発動します。
よって、pre_commit に CI のプログラムを記述することで、`git commit` したときにコーディングチェックや自動整形を実行できます。

## 自動化ツール

コーディングチェックや自動整形を行うコマンドラインツールです。

- [clang-format](https://clang.llvm.org/docs/ClangFormat.html)　 C/C++ の自動整形をします。
- [cpplint](https://github.com/cpplint/cpplint)　 C/C++ の静的コード解析をします。
- [Prettier](https://prettier.io/)　 HTML, CSS, JavaScript, PHP, Markdown, Yaml などの自動整形をします。
- [ESLint](https://eslint.org/)　 JavaScript の静的コード解析をします。
- [phpmd](https://github.com/phpmd/phpmd)　 PHP の静的コード解析をします。

<span id="install"></span>

## pre_commit 例

私が開発するときに導入している Git フックは、このリポジトリで公開しています。
[blue-hood/.git_template](https://github.com/blue-hood/.git_template)

このリポジトリの pre_commit を以下に貼り付けます。
試行錯誤で修正しているので、最新版とは異なる場合があります。

<pre class="prettyprint linenums">
  #!/bin/bash

  cppformat="{}"
  cpplint="-whitespace/comments,-whitespace/indent"
  phpmd="cleancode,codesize,controversial,design,naming,unusedcode"
  prettier="--print-width 120"

  status=0
  for changed in `git diff --staged --name-only`
  do
    path=`git rev-parse --show-toplevel`/${changed}

    if [ -e ${path} ]; then
      basename=`basename ${changed}`
      extension=${basename##*.}

      case "${extension}" in
        "c"|"h"|"cpp"|"hpp" )
          colordiff -u ${path} <(clang-format -style="${cppformat}" ${path})
          clang-format -i -style="${cppformat}" ${path}
          if ! cpplint --filter=${cpplint} --quiet ${path}; then
            status=1
          fi;;

        "php" )
          colordiff -u ${path} <(prettier ${prettier} ${path})
          prettier --write ${prettier} ${path}
          if ! phpmd ${path} text ${phpmd}; then
            status=1
          fi;;

        "js"|"html"|"css"|"scss"|"yaml"|"yml"|"md" )
          colordiff -u ${path} <(prettier ${prettier} ${path})
          prettier --write ${prettier} ${path};;

      esac

      git add ${path}
    fi
  done

  exit ${status}
</pre>

`git diff --staged --name-only` によって、コミット予定のファイル一覧を取得しています。
そして、各ファイルの拡張子を判定して CI の処理を分岐しています。

`clang-format` や `prettier` を使って自動整形をする際に、`colordiff` によって整形された箇所を表示しています。
また、`cpplint` や `phpmd` のコーディングチェックに引っかかった場合は、pre_commit の終了コードを 1 にして `exit` しています。
このようにすることでコミットを防ぐことができます。
なお、`git commit --no-verify` でコミットすれば、pre_commit によるチェックをせずに**強制コミットする**ことも可能です。

## 実際に使ってみる

pre_commit を導入して `git commit` すると、このように CI が実行されます。

<pre class="prettyprint linenums">
$ git commit
--- /home/hato/apps/apps/app/Http/Middleware/Normalize.php	2019-05-11 21:37:46.888721101 +0900
+++ /dev/fd/63	2019-05-11 21:38:09.399885336 +0900
@@ -72,7 +72,9 @@
         $parsed['fragment'] = empty($parsed['fragment']) ? '' : '#' . $parsed['fragment'];
         $parsed['path'] = $dirname . $basename;
         $url = '';
-        foreach ($elements as $element) $url .= $parsed[$element];
+        foreach ($elements as $element) {
+            $url .= $parsed[$element];
+        }
         $url = $isHtmlEncoded ? htmlspecialchars($url) : $url;
         return $url;
     }
apps/app/Http/Middleware/Normalize.php 48ms
/home/hato/apps/apps/app/Http/Middleware/Normalize.php:16	handle accesses the super-global variable $_SERVER.
/home/hato/apps/apps/app/Http/Middleware/Normalize.php:22	The method handle() contains an exit expression.
</pre>

PHP の if 文に対して {} で囲むように修正してくれています。
また、`$_SERVER` や `exit()` は使わないように指摘されてしまいました 😅 　
このようにして、ソースコードの品質を保っていくことができますね。
