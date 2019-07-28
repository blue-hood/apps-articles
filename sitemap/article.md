<picture>
  <source type="image/webp" srcset="/storage/articles/images/a92fe036.webp 1x,/storage/articles/images/90f383dd.webp 2x">
  <img src="/storage/articles/images/a92fe036.jpg" srcset="/storage/articles/images/90f383dd.jpg 2x">
</picture>
 
こんにちは、今回は内容の濃い記事が書けそうでワクワクしています😀　
このサイトでは、PHP を使わず静的な html でコンテンツを配信しています。
せっかく作っている Web サイト、この存在をクローラに気づかせるためには **sitemap.xml** の設置が重要となります。
今回は、sitemap.xml を自動的かつ**こだわりをもって(?)**生成する方法を紹介します。

このサイトの [sitemap.xml](/sitemap.xml)<br>
2019/05/04 　**sitemap.xml を生成するコマンド**を作りました！[GitHub](https://github.com/blue-hood/static-sitemap)

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

## sitemap.xml の構造

sitemap.xml は、そのサイトのサイトマップを表す xml ファイルです。
一般的にドキュメントルートに設置して、robots.txt で sitemap.xml の場所を指定します。

robots.txt の例

<pre class="prettyprint linenums">
User-agent: *
Sitemap: https://apps.bluehood.net/sitemap.xml
</pre>

sitemap.xml はさらに別の xml ファイルに分割することもできるのですが、
1 ファイルにすべてのページの情報を書き込むとするとこのような形式になります。

<pre class="prettyprint linenums">
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;urlset xmlns=&quot;http://www.sitemaps.org/schemas/sitemap/0.9&quot;&gt;
	&lt;url&gt;
    	&lt;loc&gt;https://apps.bluehood.net/&lt;/loc&gt;
        &lt;lastmod&gt;2019-04-26T00:00:00+09:00&lt;/lastmod&gt;
        &lt;changefreq&gt;weekly&lt;/changefreq&gt;
    &lt;/url&gt;
	&lt;url&gt;
    	&lt;loc&gt;https://apps.bluehood.net/articles/&lt;/loc&gt;
    	&lt;lastmod&gt;2019-04-29T19:40:41+09:00&lt;/lastmod&gt;
    	&lt;changefreq&gt;hourly&lt;/changefreq&gt;
    &lt;/url&gt;
    …
&lt;/urlset&gt;
</pre>

`url` タグで 1 ページ分の情報として列記していきます。
`url` タグ内の情報を以下のようにまとめました。

- loc: ページの URL(必須）
- lastmod: ページの最終更新日時（任意）
- changefreq: ページの更新頻度（任意、hourly, daily, weekly, monthly, yearly など）
- (priority): そのページの重要度（任意、今回は省略）

最低限の sitemap.xml を作るのであれば、`loc` だけを書いて簡易的に済ませることもできます。
しかし、できれば `lastmod` と `changefreq` も書いてサイトの情報を詳しく伝えたいところです。
ちなみに `priority` はページの重要度を 0.0〜1.0 の範囲内で指定できるのですが、[Google はこの値を無視](https://www.suzukikenichi.com/blog/google-ignores-priority-but-uses-lastmod-in-sitemaps/)しているそうです。
まあ、私としても `priority` は主観的であいまいなデータですし、あまりあてにならないかなと思います。
もちろん全部のページを `priority=1.0` とかやっても意味がないですからね！

sitemap.xml を手動で作成することもできるのですが、サイト更新のたびに修正するのも手間がかかります。
そこで、シェルスクリプトを使って `loc` を書いていき、さらにこだわりとして `lastmod` と `changefreq` も書いてくれるプログラムを作りました。

## `lastmod` と `changefreq` 生成のしくみ

まず、`lastmod` と `changefreq` を生成するには、現在のサイトコンテンツだけでなく、その**1 つ前のバージョンのコンテンツ**も必要になります。
……たとえば、現在のサイトコンテンツが old ディレクトリ内に入っているとして、サイトを更新するときは `old` ディレクトリを `html` ディレクトリにコピーして作業するものとします。

sitemap.xml を作成するときに、html ディレクトリ内の A.html と、old ディレクトリ内の A.html の比較をします。
もしファイル間の差異があれば「そのページは更新した」と判断できます。
そして、サイトを実際に更新したら、いまある old ディレクトリを削除して html ディレクトリが新たな old ディレクトリとなります。

もし old/A.html と html/A.html が同じ内容であれば、old/A.html のタイムスタンプを html/A.html にコピーします。
すると、A.html を更新しない限り、最終更新日時は old/A.html に保持され続けます。

`lastmod` は html/A.html のタイムスタンプをもとに生成するとして、`changefreq` は現在時刻と old/A.html の時間差から計算することにします。
たとえば時間差が 1 時間以内であれば hourly、24 時間以内であれば daily、……となります。

<div style="display: flex; flex-wrap: wrap; align-items: flex-start; justify-content: space-around; ">
	<img style="width: 240px; margin: 2rem 1rem; " src="/storage/articles/images/f76f2676.svg">
	<img style="width: 240px; margin: 2rem 1rem; " src="/storage/articles/images/31c2981a.svg">
	<img style="width: 215px; margin: 2rem 1rem; " src="/storage/articles/images/002a8287.svg">
</div>

## とりあえず完成形

今回は説明が難しくて長くなりそうですので、先に完成形のシェルスクリプトを貼らせてください 🙇 　
ちなみにこのスクリプトは、[crawl.sh](https://github.com/blue-hood/apps/blob/master/crawl.sh) としてこのサイトの運営にも組み込まれています。

<pre class="prettyprint linenums">
#!/bin/bash

# URL 先頭部分
domain="https://apps.bluehood.net"

# sitemap.xml 登録リストの生成
sitemaps=`find html/ -name *.html`
sitemaps=${sitemaps/html\/404.html/}
sitemaps=${sitemaps/html\/50x.html/}

# sitemap.xml の作成
cat &lt;&lt; EOS &gt; html/sitemap.xml
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;urlset xmlns=&quot;http://www.sitemaps.org/schemas/sitemap/0.9&quot;&gt;
EOS
for path in ${sitemaps}
do
  page=${path#html/}
  old=old/${page}
  page=${page/index.html/}

  # 変更チェック
  if [ -e ${old} ]; then
    prevmod=`date &quot;+%s&quot; -r ${old}`

    if diff ${path} ${old} &gt; /dev/null 2&gt;&amp;1; then
      # 変更なしの場合、古い更新日時を継承する
      mv ${old} ${path}
    fi
  else
    # 新規ファイルの場合、前回の更新時間は1970年1月1日とみなす
    prevmod=0
  fi

  # &lt;loc&gt; の生成
  loc=`echo &quot;${domain}/${page}&quot; | recode utf8..html`

  # &lt;changefreq&gt; の生成
  elapsed=$((`date &quot;+%s&quot;` - ${prevmod}))
  if [ ${elapsed} -le $((60*60)) ];then
    changefreq=&quot;hourly&quot;
  elif [ ${elapsed} -le $((60*60*24)) ];then
    changefreq=&quot;daily&quot;
  elif [ ${elapsed} -le $((60*60*24*7)) ];then
    changefreq=&quot;weekly&quot;
  elif [ ${elapsed} -le $((60*60*24*28)) ];then
    changefreq=&quot;monthly&quot;
  else
    changefreq=&quot;yearly&quot;
  fi

  # &lt;lastmod&gt; の生成
  # W3C Datetime: https://www.w3.org/TR/NOTE-datetime
  lastmod=`date &quot;+%Y-%m-%dT%H:%M:%S%:z&quot; -r ${path}`

  # &lt;url&gt; の登録
  echo &quot;&lt;url&gt;&lt;loc&gt;${loc}&lt;/loc&gt;&lt;lastmod&gt;${lastmod}&lt;/lastmod&gt;&lt;changefreq&gt;${changefreq}&lt;/changefreq&gt;&lt;/url&gt;&quot; &gt;&gt; html/sitemap.xml
done
echo &quot;&lt;/urlset&gt;&quot; &gt;&gt; html/sitemap.xml
</pre>

今回のプログラムは長めですね。
順を追って解説していきたいと思います。

### sitemap.xml 登録リストの生成

<pre class="prettyprint linenums">
sitemaps=`find html/ -name *.html`
sitemaps=${sitemaps/html\/404.html/}
sitemaps=${sitemaps/html\/50x.html/}
</pre>

まずは sitemap.xml に登録する html ファイルを `find` コマンドで取得します。
その後、sitemap.xml に登録しないファイルを、[bash の変数展開](https://qiita.com/bsdhack/items/597eb7daee4a8b3276ba)で除去しています。
今回の例では、404.html と 50x.html を除去しています。

### sitemap.xml のヘッダ書き込み

<pre class="prettyprint linenums">
cat &lt;&lt; EOS &gt; html/sitemap.xml
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;urlset xmlns=&quot;http://www.sitemaps.org/schemas/sitemap/0.9&quot;&gt;
EOS
</pre>

[bash のヒアドキュメント](https://qiita.com/take4s5i/items/e207cee4fb04385a9952)で sitemap.xml のヘッダを書き込みます。
注意点としては、`echo` コマンドではなく `cat` コマンドを使用しているところです。
EOS で囲まれたヒアドキュメントは標準入力として扱われるため、`cat` コマンドで標準入力 → 標準出力に流してあげる必要があります。

### 各 html ファイルのループ処理

<pre class="prettyprint linenums">
for path in ${sitemaps}
do
  page=${path#html/}
  old=old/${page}
  page=${page/index.html/}

  # 各 html ファイルのループ処理

done
</pre>

変数 `sitemaps` に各 html ファイルのパスが入っていますので、これを `for` 文でループ処理します。
パスの先頭は必ず html/ から始まるため、これを除去して変数 `page` に入れています。
そして、新たに old/ を先頭に付けて、1 つ前のバージョンのパス `old` を作ります。
また、sitemap.xml に登録するときは「index.html」を除去して URL を正規化することにします。

### 変更チェック

<pre class="prettyprint linenums">
  # 変更チェック
  if [ -e ${old} ]; then
    prevmod=`date &quot;+%s&quot; -r ${old}`

    if diff ${path} ${old} &gt; /dev/null 2&gt;&amp;1; then
      # 変更なしの場合、古い更新日時を継承する
      mv ${old} ${path}
    fi
  else
    # 新規ファイルの場合、前回の更新時間は1970年1月1日とみなす
    prevmod=0
  fi
</pre>

html/ と old/ で html ファイルの変更があるかを判定します。
まず、`if [ -e ${old} ];` によって old/ の html ファイルがあるかを判定します。
ない場合は**新規ページ**とみなせるため、前回の更新日時 `prevmod` を 1970 年 1 月 1 日にセットします。
プログラムでは `prevmod=0` としていますが、これは時間を [UNIX 時間](https://ja.wikipedia.org/wiki/UNIX%E6%99%82%E9%96%93)で扱っているためです。

old/ の html ファイルが存在する場合は、`diff` コマンドでファイル内容の変更チェックをします。
変更がない場合は、old/ の html ファイルを html/ に移動（上書き）することで、古い更新日時を継承します。
`prevmod` は、`date` コマンドで old/ の html ファイルから取得します。

この処理での出力は、前回の更新日時 `prevmod` と、html/ にある html ファイルの更新日時です。

### &lt;loc&gt; の生成

<pre class="prettyprint">
  loc=`echo &quot;${domain}/${page}&quot; | recode utf8..html`
</pre>

ドメイン名と html ファイルのパスをつなぎ合わせて loc タグを作ります。
なお、sitemap.xml では &lt; や &gt; などの特殊文字をエンコードする必要があるため、`recode utf8..html` を挟んでいます。

### &lt;changefreq&gt; の生成

<pre class="prettyprint linenums">
  # &lt;changefreq&gt; の生成
  elapsed=$((`date &quot;+%s&quot;` - ${prevmod}))
  if [ ${elapsed} -le $((60*60)) ];then
    changefreq=&quot;hourly&quot;
  elif [ ${elapsed} -le $((60*60*24)) ];then
    changefreq=&quot;daily&quot;
  elif [ ${elapsed} -le $((60*60*24*7)) ];then
    changefreq=&quot;weekly&quot;
  elif [ ${elapsed} -le $((60*60*24*28)) ];then
    changefreq=&quot;monthly&quot;
  else
    changefreq=&quot;yearly&quot;
  fi
</pre>

前述の処理で求めた `prevmod` をもとに、更新頻度 `changefreq` を求めます。
まずは `date &quot;+%s&quot;` で現在の UNIX 時間を取得し、`prevmod` と引き算をすることで経過時間 `elapsed` を計算します。
`elapsed` は秒単位となりますが、これが 60x60=3600 秒、つまり 1 時間以内であれば `changefreq=&quot;hourly&quot;` としています。
同様に daily、weekly、monthly、yearly の判定を `if` 文で記述していますが、monthly の判定は 28 日としました。

### &lt;lastmod&gt; の生成

<pre class="prettyprint linenums">
  # &lt;lastmod&gt; の生成
  # W3C Datetime: https://www.w3.org/TR/NOTE-datetime
  lastmod=`date &quot;+%Y-%m-%dT%H:%M:%S%:z&quot; -r ${path}`
</pre>

html/ にある html ファイルから lastmod を求めますが、sitemap.xml には [W3C Datetime](https://www.w3.org/TR/NOTE-datetime)
という形式で書き込む必要があります。細かい時分秒で出力するにはタイムゾーンの情報も必要になります。

### 最後に &lt;url&gt; を書き込んで完成

<pre class="prettyprint linenums">
  # &lt;url&gt; の登録
  echo &quot;&lt;url&gt;&lt;loc&gt;${loc}&lt;/loc&gt;&lt;lastmod&gt;${lastmod}&lt;/lastmod&gt;&lt;changefreq&gt;${changefreq}&lt;/changefreq&gt;&lt;/url&gt;&quot; &gt;&gt; html/sitemap.xml
done
echo &quot;&lt;/urlset&gt;&quot; &gt;&gt; html/sitemap.xml
</pre>

頑張って作った `loc`, `changefreq`, `lastmod` を `url` タグにまとめて sitemap.xml に追記します。
そして、各 html ファイルのループ処理後にフッタ `</urlset>` を書き込めば完成です。

## アルゴリズムでオリジナリティを出す

さらにこだわるとしたら、`priority` を計算するアルゴリズムを考えて実装するところでしょうか？
これで、Google にもサイトの評価をしてもらえそうです。
せっかく書いている記事、多くの人に見てもらいたいので 🌿

2019/05/04 　 sitemap.xml を生成するスクリプトをコマンド化しました。[GitHub](https://github.com/blue-hood/static-sitemap)
