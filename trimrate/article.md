<picture>
  <source type="image/webp" srcset="/storage/articles/images/1814cc87.webp 1x, /storage/articles/images/21c8af6c.webp 2x">
  <img src="/storage/articles/images/1814cc87.jpg" srcset="/storage/articles/images/21c8af6c.jpg 2x">
</picture>

皆さんは画像をSNSや自身のブログなどに投稿する際に加工したことはないでしょうか？
Twitterでは画像が自動的に16:9で表示されるため、予め16:9でトリミングしておくことでどのように表示されるかを調整できます。
トリミング機能はAndroid版にはありますがWeb版にはないため、PCからの画像投稿のときに少し面倒に感じたりします。

<ol class="table-of-contents"></ol>

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- ディスプレイ広告 -->

<ins class="adsbygoogle"
    style="display:block"
    data-ad-client="ca-pub-7008780049786244"
    data-ad-slot="5063315418"
    data-ad-format="auto"
    data-full-width-responsive="true"></ins>

<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>

## 画像編集の自動化といえばImageMagick

そこでGIMPなどの画像編集ソフトが必要になりますが、私はImageMagickを利用しています。
画像の加工を自動で行うためのコマンドラインが用意されており、例えば以下のようにすればpngファイルを一括で余白トリミングできます。
GIMPでいう「最小枠で切り抜き」機能ですね。

<pre class="prettyprint">
$ mogrify -trim *.png
</pre>

画像サイズを指定しての切り抜きはImageMagickの `crop` オプションを使います。
座標 (`X`, `Y`) の位置で幅 `W` px、高さ `H` pxのトリミングをするには以下のコマンドを打ちます。

<pre class="prettyprint">
$ convert (元ファイル) -crop WxH+X+Y (出力パス)
</pre>

しかし、残念ながら比率（16:9など）の指定によるトリミングには対応していません。
そこで、比率指定によるトリミングのコマンドをシェルスクリプトで作りました。

## `trimrate` コマンドを作ってみた

結論から言うと、比率指定によるトリミングコマンドを以下のように作りました。

<script src="https://gist.github.com/Hato6502/dc46fd6108301ed9106da67db6768ec9.js"></script>

このシェルスクリプトを `trimrate` というファイル名で保存します。
保存先は `/usr/local/bin` などのパスが通ってるところが便利です。
そして `chmod +x trimrate` で実行権限を与えて以下の構文でトリミングを行います。

<pre class="prettyprint">
$ trimrate (元ファイル) (幅比率) (高さ比率) (X オフセット) (Y オフセット) (出力パス) [(ImageMagick オプション)]
$ trimrate src.jpg 16 9 0 0 dest.jpg -gravity center (←こんな感じ)
</pre>

コマンドの動作的には、比率から切り抜きの幅高さを計算してImageMagickの `crop` 機能を呼び出しているだけです。
デフォルトだと画像の上部でトリミングするため、`-gravity center` オプションを加えて中央でトリミングしています。

## `for` 文で一括処理する

`trimrate` コマンドだけだと `mogrify` のように複数のファイルを一括処理できません。
しかし、`bash` の `for` 文で画像ファイルごとに `trimrate` を実行して一括処理させることはできます。

<pre class="prettyprint">
$ mkdir 16-9
$ for file in `ls *.jpg`; do trimrate ${file} 16 9 0 0 16-9/${file} -gravity center; done
</pre>

やってることは、`16-9` という名前でディレクトリを作り、`ls *.jpg` と `for` 文でjpgファイルのループ処理をしています。
`identify` コマンドでトリミング前後の画像ファイルを確認すると、

<pre class="prettyprint">
$ identify *.jpg  (トリミング前)
publicdomainq-0029161ayicpk.jpg JPEG 3491x2410 3491x2410+0+0 8-bit sRGB 1.476MB 0.030u 0:00.020
publicdomainq-0029407uhs.jpg JPEG 900x675 900x675+0+0 8-bit sRGB 111KB 0.010u 0:00.009
publicdomainq-0031792moqgua.jpg JPEG 5184x3456 5184x3456+0+0 8-bit sRGB 3.616MB 0.000u 0:00.000
publicdomainq-0031794ftclzr.jpg JPEG 5184x3456 5184x3456+0+0 8-bit sRGB 5.388MB 0.280u 0:00.289
publicdomainq-0031795pgepte.jpg JPEG 5472x3648 5472x3648+0+0 8-bit sRGB 5.073MB 0.000u 0:00.000
publicdomainq-0033354fvanhv.jpg JPEG 4752x3168 4752x3168+0+0 8-bit sRGB 5.697MB 0.280u 0:00.269
$ identify 16-9/*.jpg  (トリミング後)
16-9/publicdomainq-0029161ayicpk.jpg JPEG 3491x1963 3491x1963+0+0 8-bit sRGB 1.462MB 0.010u 0:00.019
16-9/publicdomainq-0029407uhs.jpg JPEG 900x506 900x506+0+0 8-bit sRGB 112KB 0.000u 0:00.000
16-9/publicdomainq-0031792moqgua.jpg JPEG 5184x2916 5184x2916+0+0 8-bit sRGB 3.468MB 0.000u 0:00.000
16-9/publicdomainq-0031794ftclzr.jpg JPEG 5184x2916 5184x2916+0+0 8-bit sRGB 5.783MB 0.000u 0:00.000
16-9/publicdomainq-0031795pgepte.jpg JPEG 5472x3078 5472x3078+0+0 8-bit sRGB 5.342MB 0.000u 0:00.000
16-9/publicdomainq-0033354fvanhv.jpg JPEG 4752x2673 4752x2673+0+0 8-bit sRGB 5.354MB 0.010u 0:00.000
</pre>

このとおり16:9になっています。
`display` コマンドで中身を確認すると、ちゃんと中央でのトリミングになっていました。

トリミング前
<picture>

  <source type="image/webp" srcset="/storage/articles/images/1d1900bc.webp 1x">
  <img src="/storage/articles/images/1d1900bc.png" alt="トリミング前">
</picture>

トリミング後
<picture>

  <source type="image/webp" srcset="/storage/articles/images/c355feaf.webp 1x">
  <img src="/storage/articles/images/c355feaf.png" alt="トリミング後">
</picture>

## 作業効率化で差をつけよう

デスクワークでは画像の編集もよくあることだと思いますが、コマンドによる作業効率化はあまり知られていないように感じます。
ドキュメントであれば文字の置換など作業効率化のイメージがつきますが、画像になると人手でやるしかない、と思われる方も多いと思います。
（私の前職ではまさかのペイントしか使えない環境だったので……）。
ぜひ、画像においても作業効率化が普及することを願います。
