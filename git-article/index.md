---
title: 記事を Git で管理することにした
description: 記事原稿を Git で管理するメリットや方法をまとめました。
thumbnail: /storage/articles/images/e7c0bb07.jpg
---

<picture>
  <source type="image/webp" srcset="/storage/articles/images/e7c0bb07.webp">
  <img src="/storage/articles/images/e7c0bb07.jpg">
</picture>

このサイトでは記事をデータベースに保存していたのですが、Git での管理に移行しました。
ここでは Markdown と Laravel で記事を Git 管理する方法をまとめます。

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

## Git と記事は相性が良い

WordPress などの CMS には、下書き保存や承認フロー機能など、複数人で運営するための機能が実装されています。
また更新履歴を残せるため、誰がどこを書き換えたのか調査でき、以前のバージョンに戻すことも容易となっています。
このサイトは[オリジナルのシステム](/articles/staticgen/)で記事を管理していますが、このような機能を自力で実装するのはおおがかりです。

そこで、記事の管理をデータベースからファイルシステムに移行し、記事ディレクトリを Git で管理することにしました。
記事の管理機能と Git は共通点が多くあります。
たとえば、CMS の機能は以下のように代替可能です。

- 更新履歴 → コミットログ
- 下書き保存 → ブランチを切って作業
- 承認フロー →（GitHub の）Pull Request

さらに、次のようなメリットもあります。

- 記事をテキストエディタで編集するため、CMS に編集機能を実装する必要がない。
- 記事データをファイルとして配置するため、[textlint](https://textlint.github.io/) や [Prettier](https://prettier.io/) などのツールを適用しやすい。

というわけで、CMS での実装を減らしつつ記事管理を高機能にできます。

デメリットとしては、

- Git を非エンジニアに使わせるのは学習コストが高いかも。
- ファイルから記事データを読み込むため、読み込み負荷が非常に大きい。

が挙げられます。
視覚的に Git を操作できるツールを導入したりして、Git 独特の操作の難しさを解消する必要がありそうですね。
また、記事データを [RAM ディスク](https://blog.katsubemakito.net/linux/ramdisk-tmpfs)に配置したり、[Redis](https://redis.io/) にキャッシュさせたりとひと工夫必要そうです。

# FrontMatter でメタデータを管理する

私は記事を Markdown で書いているのですが、記事のタイトルなどのメタデータはデータベースのカラムとして管理していました。
そこで、これらのメタデータも [FrontMatter](https://jekyllrb.com/docs/front-matter/) として記事本文の .md ファイルに埋め込みます。Markdown の先頭行に `---` で囲んだ YAML データを定義できます。

<pre class="prettyprint">
ーーー (←半角に置き換える)
title: 記事を Git で管理することにした
description: 記事原稿を Git で管理するメリットや方法をまとめました。
thumbnail: /storage/articles/images/e7c0bb07.jpg
---

(記事本文)
</pre>

記事の更新日時はファイルシステムを利用します。

# Laravel での実装

# 記事をキャッシュする
