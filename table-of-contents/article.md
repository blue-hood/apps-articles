<picture>
  <source type="image/webp" srcset="/storage/articles/images/5b213ef0.webp 1x,/storage/articles/images/62fd5d1b.webp 2x">
  <img src="/storage/articles/images/5b213ef0.jpg" srcset="/storage/articles/images/62fd5d1b.jpg 2x">
</picture>

WordPress 製のサイトなどで見かける**目次**。
そのページの内容がリストとして一目で確認できるため、Web サイトにも大きな役割を果たします。
JavaScript で目次を自動生成する方法を紹介します。

<p style="text-align: center; "><b>↓この目次のことです！</b></p>
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

<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>
<!-- textlint-enable -->

## JavaScript で目次を作る

基本的に見出しは h1〜h6 でタグ付けされているかと思います。
これらのタグには見出しという情報だけでなく、**見出しどうしの階層関係**も表現されています。
よって、h1〜h6 タグは目次を作るのに最適な情報源と言えます。

目次を置く場所に `<ol class="table-of-contents"></ol>` を配置して、この要素の中に JavaScript で目次の情報を書き込んでいきます。

<script src="https://gist.github.com/Hato6502/e10e01e891fa9abd56eda3d6beedeef0.js"></script>

上記が目次を自動生成する JavaScript の完成形になります。

### QuerySelectorAll で h1〜h6 タグを探す

<pre class="prettyprint linenums">
const table_of_contents = document.querySelectorAll(".table-of-contents");
const headings = table.parentElement.querySelectorAll("h1, h2, h3, h4, h5, h6");
</pre>

まずは `.table-of-contents` を設置した親要素から、h1〜h6 要素を探し出します。

### h1〜h6 タグを元に目次要素を生成

<pre class="prettyprint linenums">
const li = document.createElement("li");
const a = document.createElement("a");
const id = `section-${table_of_contents_counter++}`;
const ol = document.createElement("ol");

// 目次要素の生成
a.textContent = heading.textContent;
a.href = `#${id}`;
li.appendChild(a);
li.appendChild(ol);
</pre>

探し出した h1〜h6 タグが変数 `heading` に入ってループ処理されているとします。
まずは目次 1 行分に相当する li 要素を生成し、その中に a 要素と ol 要素を入れています。
a 要素には h1〜h6 のテキストを入れて、さらにアンカーリンクとして href 属性に `#section-${table_of_contents_counter++}` を入れています。
変数 `table_of_contents_counter` は 1、2、3…と増分していくので、生成されるアンカーリンクも #section-1、#section-2、…となります。
ol 要素は、見出しの階層構造を作るために後で必要となります。

### h1〜h6 タグにアンカーリンクも紐付け

<pre class="prettyprint linenums">
// リンク先の生成
heading.id = id;
heading.classList.add("anchor-link");
</pre>

生成したアンカーリンクを h1〜h6 タグの id に設定します。
これによって、目次の要素をクリックしたときにページ内ジャンプさせます。
さらに、必要であれば anchor-link クラスを付けて CSS でデザイン調整しましょう。

### 階層構造の生成

<pre class="prettyprint linenums">
// 階層構造の生成
const level = Number(heading.tagName.substring(1));
let parent;
do {
    parent = stack.pop();
} while (parent.level >= level);
parent.element.appendChild(li);
stack.push(parent);
stack.push({ level: level, element: ol });
</pre>

最後に、生成した li 要素を目次に挿入します。
このとき、h1〜h6 タグの**レベル**(h1→1、h2→2、…)に応じて、li 要素の挿入先が変わります。

たとえば h1 タグの次に h2 タグがある場合は、h1 タグの見出しの配下に h2 タグがあると判定します。
このレベルの比較によって、どの目次要素の配下に新しい li 要素を挿入するかを、上記 4〜7 行目で行っています。

## CSS で見出しの番号を付ける

JavaScript で生成した目次には何の CSS も適用していないため、見出しの番号が正しく付かないと思います。

<pre class="prettyprint lang-html">
1. h1 タグ
  1. h2 タグ
  2. h2 タグ
2. h1 タグ
</pre>

これを、CSS で正しく番号を振るようにします。

<pre class="prettyprint linenums">
.table-of-contents {
  max-width: 640px;
  margin-left: auto;
  margin-right: auto;
  border: 2px solid rgba(0,0,0,0.1);
  padding: 0.5rem 1rem;
}
.table-of-contents::before {
  content: "目次";
  font-weight: 700;
}
.table-of-contents ol {
  counter-reset: ol-counter;
}
.table-of-contents ol li {
  position: relative;
  list-style: none;
}
.table-of-contents ol li::before {
  counter-increment: ol-counter;
  content: counters(ol-counter,".");
  padding-right: 0.5rem;
  position: absolute;
  top: 0;
  left: -40px;
  display: inline-block;
  text-align: right;
  width: 40px;
}
</pre>

<pre class="prettyprint lang-html">
1 h1 タグ
  1.1 h2 タグ
  1.2 h2 タグ
2 h1 タグ
</pre>

### デフォルトの番号付けを無効化

まずはデフォルトの番号付けを無効します。

<pre class="prettyprint linenums">
.table-of-contents ol li {
  list-style: none;
}
</pre>

### counters で番号を作る

<pre class="prettyprint linenums">
.table-of-contents ol {
  counter-reset: ol-counter;
}
.table-of-contents ol li::before {
  counter-increment: ol-counter;
  content: counters(ol-counter,".");
}
</pre>

CSS には counters という、番号を自動で振る便利な機能があります。
ol 要素を番号振りの原点として、以降は li 要素が来るたびにカウンタを +1 して表示しています。
そのほか、目次のデザインを整えるために `position: absolute;` とかしていますが、ここでは省略します 🙇

## 1 からサイトを作るのは楽しい

WordPress を使えば、目次を生成するプラグインもありますしデザインも洗練されています。
しかし、1 からサイトを作るのも、JavaScript や CSS の勉強としてよいかなと思います！
私の場合は、1 から作ろうとしてつまずいたことが、そのまま記事として書けて良い循環になっています 😊
