<picture>
  <source type="image/webp" srcset="/storage/articles/images/2dfe71f9.webp 1x,/storage/articles/images/2dfe71f8.webp 2x">
  <img src="/storage/articles/images/2dfe71f9.jpg" srcset="/storage/articles/images/2dfe71f8.jpg 2x">
</picture>

皆さんこんにちは。
私はつい最近**静的サイトジェネレータ**という言葉を聞きました。
「静的」に興味を惹かれた私が、静的サイトジェネレータという言葉だけで静的サイトを作ってみた話をまとめます。
今回の記事は私の憶測が多いのでご注意ください（笑）

なお、当サイトはこの記事の通りに Laravel & wget & nginx による静的サイトとなっています。
もしご興味があれば、[GitHub](https://github.com/blue-hood/apps)を見ていただけると幸いです。

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

## 静的サイトとは？

まず、**静的サイト**の意味から考える必要があります。
おそらくこれは静的なサイト、つまりリクエストに対して Web サーバのレスポンスコンテンツが変わらないということです。
ということは、HTML, CSS, 画像アセットなどの静的なファイルを提供するだけのサイトであれば広義の静的サイトに当てはまります。

## 静的サイトのメリット・デメリット

ただ html を提供するだけの静的サイトであれば、なんだかインターネット初期の '90 年代のように感じてしまいます。
そこで、静的サイトのメリットをまとめてみました。

- Web サーバが PHP などを実行することはないため、動的コンテンツによる脆弱性が存在しない。
- すでにレスポンスコンテンツが用意されているため、高速である。
- レンタルサーバなどでは PHP を実行できない場合も多く、静的サイトであればサーバに求める要件が低い。

次に、静的サイトのデメリットをまとめます。

- 端末（スマートフォン、PC など）によってサーバレスポンスを変化させることは難しい。
- REST API や検索機能などの提供は不可能。

## 静的サイトジェネレータとは？

**静的サイト**の意味が定まったところで、次は静的サイト**ジェネレータ**について考えます。
これは静的サイトを作る、つまり静的サイトを作る元データがあり、それを加工して静的コンテンツを作るツールと考えられます。
ということは PHP で動的コンテンツを作り、それを `php` コマンドで html 出力して Web サーバで公開すれば、
PHP は静的サイトジェネレータになるということです。
逆に、アクセスがあった時点で PHP を実行して html を出力するのであれば、PHP は動的サイトのエンジンやエグゼキュータと呼べるのでしょう。

## クローラは静的サイトジェネレータ？

以上のことを踏まえると、どんな動的コンテンツでも、サーバからダウンロードしたときには静的コンテンツになっていることになります。
JavaScript による [DOM](https://developer.mozilla.org/ja/docs/Web/API/Document_Object_Model) 操作などは、サーバとしては静的であるとみなします。
よって、ローカルな開発環境で動的サイトのサーバを立ち上げ、それをクローラで html として取得すれば、
クローラは静的サイトジェネレータということになります。

## Laravel & wget & nginx で静的サイトを作る

ということで、Laravel で動的サイトを作り、wget でクローリングして静的サイトを生成します。
具体的な作り方はあとあと新しい記事で紹介していきたいと思いますが、ここでは全体の概要を説明したいと思います。
なお、以下の例では **GET クエリ**（URL の ? 以降に続くクエリ）についても対応していますが、現在のところ動作確認していません。

### Laravel

サイトデザインは PC とスマートフォンで切り替える必要があっても、CSS の[メディアクエリ](https://developer.mozilla.org/ja/docs/Web/CSS/Media_Queries/Using_media_queries)
によってクライアント側で対応するようにします。
また、検索機能などの動的なコンテンツは扱えないことを踏まえながら、開発をしていく必要があります。

### wget

`wget` を使って、ローカルな Laravel サーバ localhost をクローリングします。
さらに、sitemap.xml やエラーページなどはリンクがないため、個別に `curl` でクローリングしています。
さらに、クローリングした html ファイル内に含まれる「localhost」を、デプロイ先の Web サーバのドメインに置き換えます。
この処理をしないと、デプロイしたときにサイト内リンクが正しく機能しません。
また、nginx で GET クエリの対応をするために、ファイル名を置き換えています。

<pre class="prettyprint linenums">
# クローリング
wget -q --mirror --page-requisites --html-extension localhost
curl -s localhost/sitemap.xml -o localhost/sitemap.xml
curl -s localhost/404.html -o localhost/404.html
curl -s localhost/50x.html -o localhost/50x.html
mv localhost html

# ドメイン置換
grep -lr 'http://localhost&/#039; html/* | xargs sed -i -e "s#http://localhost/#${domain}#g"

# ファイル名置換
for path in `find html/ -name '*.html?*'`
do
  rename="${path//index.html?/_}"
  rename="${rename//.html?/.html_}"
  mv "${path}" "${rename}"
done
</pre>

### nginx

nginx で静的 html を提供しますが、URL に 〜.html がつかないように対応します。
まず、html ファイルは[内部リダイレクト]()でのみアクセス可能とします。
そして、GET クエリがある場合は、レスポンドする html ファイルを名前解決して内部リダイレクトします。

<pre class="prettyprint linenums">
server {
  listen       80;
  server_name  apps.bluehood.net;

  root   /usr/share/nginx/html;
  index  index.html;
  
  # エラーページ
  error_page  403 404              /404.html;
  location = /404.html {
    internal;
  }
  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
    internal;
  }

  # html は内部リダイレクトのみ
  location ~ \.html$ {
    internal;
  }

  location / {
    # html ルーティング
    if ($is_args = ?) {
      # GET パラメータ名前解決
      rewrite ^(.*)$ ${uri}_${args}.html? break;
    }
  }
}
</pre>

## 本来の静的サイトジェネレータ

以上の内容は、あくまで私が静的サイトジェネレータという言葉だけで静的サイトを作ってみた話です。
本来の静的サイトジェネレータの 1 つとして、[GatsbyJS](https://www.gatsbyjs.org/) というものがあります。
Gatsby では、[React](https://reactjs.org/) を使うことで静的サイトでありながらも動的なコンテンツをレンダリングするようです。

なお、当サイトはこの記事の通りに Laravel & wget & nginx による静的サイトとなっています。
もしご興味があれば、[GitHub](https://github.com/blue-hood/apps)を見ていただけると幸いです。
