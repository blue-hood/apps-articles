<picture>
  <source type="image/webp" src="/storage/articles/images/75c62197.webp">
  <img src="/storage/articles/images/75c62197.jpg">
</picture>

みなさんこんにちは 😁 　
今回は「<!-- textlint-disable -->リーダブルコード<!-- textlint-enable -->」に対する懐疑的な意見を述べたいと思います。

<ol class="table-of-contents"></ol>
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

## 10 年前の自分をコードレビュー

まずは、私が 10 年前、**中学生のころに作ったブロック崩し**のプログラムを紹介したいと思います。
言語は [HSP (Hot Soup Processor)](https://hsp.tv/make/hsp3.html) というものでして、とても初心者向けの言語です。
BASIC に似た言語ともいえます。

プログラムのファイル名は `本当のブロック崩し.hsp`（笑）
当時ブロック崩しを作るのに一度挫折したため、`ブロック崩し.hsp` には違うプログラムが入っていました。
そして 2 回目でブロック崩しのプログラミングに成功したので、`本当のブロック崩し.hsp` と名付けたのです（笑笑）

そのプログラムがこちらです。コードレビューしていきたいと思います。

<pre class="prettyprint linenums">
	font "HGPｺﾞｼｯｸE",22,0
	title "ブロック崩し"
	pos 241,100
	mes "ブロック崩し"
	pos 250,400
	button "START",*shoki2
	n=5
	buffer 1 : picload "brock.bmp",1
	buffer 2 : picload "ball.bmp",1
	gsel 0,2
	stop
*shoki2
	cls 0
*shoki
	m=0
	c=0
	w=1
	z=1
	x=150
	a=250
	b=300
	s=0
	k=5	
	v=0
	e=1
	p=175
	r=1
	dim o,80
	randomize
	rnd e,4
*shoki3
	mmload "oto2.wav"
	mmplay
	cls 0
	

	


*ball
	redraw 0
	font "ＭＳ ゴシック",16,0
	if m=5 :goto *over
	getkey k,1
	color 255,255,255 : boxf : color : pos 0,0
	if k=1 :goto *PAUSE
	mouse
	pos a,b
	gsel 0:gmode 2,16,16,150:gcopy 2
	a=a+(4)*z
	b=b+(4)*w
	pos 125,425
	color 0,0,0
	mes "SCORE"
	mes c
	pos 450,425
	mes "MISS"
	mes m
	color 0,0,0
	line 0,16,900,16
	line 16,0,16,650
	line 618,0,618,650

	
	
	mmload "oto1.wav"
	if b+16>400 :if 448>b+16:if a+8>mousex-36:if mousex+36>a+8:w=-1:mmplay
	if b+16>400 :if 448>b+16:if a+8>mousex-40:if mousex-30>a+8:w=-1:z=-1:mmplay
	if b+16>400 :if 448>b+16 :if a+8>mousex+30:if mousex+40>a+8:w=-1:z=1:mmplay
	mmload "oto2.wav"
	
	if a+16>618:z=-1:mmplay
	if 16>a:z=1:mmplay
	if 16>b:w=1:mmplay
	if b+16>532:m=m+1:w=1:z=1:a=250:b=300:s=0:mmload "oto3.wav":mmplay:wait 150:dim o,80:v=0
	pos mousex-40,400
	picload "ber2.bmp",1
	if o(0)=0:pos 64,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>64: if 96>a+8:c=c+100:t=64:y=64:o(0)=1:goto *atari
	if o(1)=0:pos 96,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>96: if 128>a+8:c=c+100:t=96:y=64:o(1)=1:goto *atari
	if o(2)=0:pos 128,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>128: if 160>a+8:c=c+100:t=128:y=64:o(2)=1:goto *atari
	if o(3)=0:pos 160,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>160: if 192>a+8:c=c+100:t=160:y=64:o(3)=1:goto *atari
	if o(4)=0:pos 192,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>192: if 224>a+8:c=c+100:t=192:y=64:o(4)=1:goto *atari
	if o(5)=0:pos 224,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>224: if 256>a+8:c=c+100:t=224:y=64:o(5)=1:goto *atari
	if o(6)=0:pos 256,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>256: if 288>a+8:c=c+100:t=256:y=64:o(6)=1:goto *atari
	if o(7)=0:pos 288,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>288: if 320>a+8:c=c+100:t=288:y=64:o(7)=1:goto *atari
	if o(8)=0:pos 320,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>320: if 352>a+8:c=c+100:t=320:y=64:o(8)=1:goto *atari
	if o(9)=0:pos 352,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>352: if 384>a+8:c=c+100:t=352:y=64:o(9)=1:goto *atari
	if o(10)=0:pos 384,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>384: if 416>a+8:c=c+100:t=384:y=64:o(10)=1:goto *atari
	if o(11)=0:pos 416,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>416: if 448>a+8:c=c+100:t=416:y=64:o(11)=1:goto *atari
	if o(12)=0:pos 448,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>448: if 480>a+8:c=c+100:t=488:y=64:o(12)=1:goto *atari
	if o(13)=0:pos 480,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>480: if 512>a+8:c=c+100:t=480:y=64:o(13)=1:goto *atari
	if o(14)=0:pos 512,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>512: if 544>a+8:c=c+100:t=512:y=64:o(14)=1:goto *atari
	if o(15)=0:pos 544,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>544: if 576>a+8:c=c+100:t=544:y=64:o(15)=1:goto *atari
	if o(16)=0:pos 64,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>64: if 96>a+8:c=c+100:t=64:y=80:o(16)=1:goto *atari
	if o(17)=0:pos 96,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>96: if 128>a+8:c=c+100:t=96:y=80:o(17)=1:goto *atari
	if o(18)=0:pos 128,:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>128: if 160>a+8:c=c+100:t=128:y=80:o(18)=1:goto *atari
	if o(19)=0:pos 160,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>160: if 192>a+8:c=c+100:t=160:y=80:o(19)=1:goto *atari
	if o(20)=0:pos 192,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>192: if 224>a+8:c=c+100:t=192:y=80:o(20)=1:goto *atari
	if o(21)=0:pos 224,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>224: if 256>a+8:c=c+100:t=224:y=80:o(21)=1:goto *atari
	if o(22)=0:pos 256,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>256: if 288>a+8:c=c+100:t=256:y=80:o(22)=1:goto *atari
	if o(23)=0:pos 288,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>288: if 320>a+8:c=c+100:t=288:y=80:o(23)=1:goto *atari
	if o(24)=0:pos 320,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>320: if 352>a+8:c=c+100:t=320:y=80:o(24)=1:goto *atari
	if o(25)=0:pos 352,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>352: if 384>a+8:c=c+100:t=352:y=80:o(25)=1:goto *atari
	if o(26)=0:pos 384,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>384: if 416>a+8:c=c+100:t=384:y=80:o(26)=1:goto *atari
	if o(27)=0:pos 416,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>416: if 448>a+8:c=c+100:t=416:y=80:o(27)=1:goto *atari
	if o(28)=0:pos 448,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>448: if 480>a+8:c=c+100:t=488:y=80:o(28)=1:goto *atari
	if o(29)=0:pos 480,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>480: if 512>a+8:c=c+100:t=480:y=80:o(29)=1:goto *atari
	if o(30)=0:pos 512,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>512: if 544>a+8:c=c+100:t=512:y=80:o(30)=1:goto *atari
	if o(31)=0:pos 544,80:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>80: if 96>b+8: if a+8>544: if 576>a+8:c=c+100:t=544:y=80:o(31)=1:goto *atari

	if o(32)=0:pos 64,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>64: if 96>a+8:c=c+100:t=96:y=96:o(32)=1:goto *atari
	if o(33)=0:pos 96,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>96: if 128>a+8:c=c+100:t=96:y=96:o(33)=1:goto *atari
	if o(34)=0:pos 128,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>128: if 160>a+8:c=c+100:t=128:y=96:o(34)=1:goto *atari
	if o(35)=0:pos 160,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>160: if 192>a+8:c=c+100:t=160:y=96:o(35)=1:goto *atari
	if o(36)=0:pos 192,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>192: if 224>a+8:c=c+100:t=192:y=96:o(36)=1:goto *atari
	if o(37)=0:pos 224,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>224: if 256>a+8:c=c+100:t=224:y=96:o(37)=1:goto *atari
	if o(38)=0:pos 256,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>256: if 288>a+8:c=c+100:t=256:y=96:o(38)=1:goto *atari
	if o(39)=0:pos 288,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>288: if 320>a+8:c=c+100:t=288:y=96:o(39)=1:goto *atari
	if o(40)=0:pos 320,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>320: if 352>a+8:c=c+100:t=320:y=96:o(40)=1:goto *atari
	if o(41)=0:pos 352,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>352: if 384>a+8:c=c+100:t=352:y=96:o(41)=1:goto *atari
	if o(42)=0:pos 384,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>384: if 416>a+8:c=c+100:t=384:y=96:o(42)=1:goto *atari
	if o(43)=0:pos 416,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>416: if 448>a+8:c=c+100:t=416:y=96:o(43)=1:goto *atari
	if o(44)=0:pos 448,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>448: if 480>a+8:c=c+100:t=488:y=96:o(44)=1:goto *atari
	if o(45)=0:pos 480,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>480: if 512>a+8:c=c+100:t=480:y=96:o(45)=1:goto *atari
	if o(46)=0:pos 512,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>512: if 544>a+8:c=c+100:t=512:y=96:o(46)=1:goto *atari
	if o(47)=0:pos 544,96:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>96: if 112>b+8: if a+8>544: if 576>a+8:c=c+100:t=544:y=96:o(47)=1:goto *atari
	
	if o(48)=0:pos 64,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>64: if 96>a+8:c=c+100:t=64:y=112:o(48)=1:goto *atari
	if o(49)=0:pos 96,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>96: if 128>a+8:c=c+100:t=96:y=112:o(49)=1:goto *atari
	if o(50)=0:pos 128,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>128: if 160>a+8:c=c+100:t=128:y=112:o(50)=1:goto *atari
	if o(51)=0:pos 160,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>160: if 192>a+8:c=c+100:t=160:y=112:o(51)=1:goto *atari
	if o(52)=0:pos 192,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>192: if 224>a+8:c=c+100:t=192:y=112:o(52)=1:goto *atari
	if o(53)=0:pos 224,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>224: if 256>a+8:c=c+100:t=224:y=112:o(53)=1:goto *atari
	if o(54)=0:pos 256,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>256: if 288>a+8:c=c+100:t=256:y=112:o(54)=1:goto *atari
	if o(55)=0:pos 288,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>288: if 320>a+8:c=c+100:t=288:y=112:o(55)=1:goto *atari
	if o(56)=0:pos 320,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>320: if 352>a+8:c=c+100:t=320:y=112:o(56)=1:goto *atari
	if o(57)=0:pos 352,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>352: if 384>a+8:c=c+100:t=352:y=112:o(57)=1:goto *atari
	if o(58)=0:pos 384,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>384: if 416>a+8:c=c+100:t=384:y=112:o(58)=1:goto *atari
	if o(59)=0:pos 416,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>416: if 448>a+8:c=c+100:t=416:y=112:o(59)=1:goto *atari
	if o(60)=0:pos 448,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>448: if 480>a+8:c=c+100:t=488:y=112:o(60)=1:goto *atari
	if o(61)=0:pos 480,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>480: if 512>a+8:c=c+100:t=480:y=112:o(61)=1:goto *atari
	if o(62)=0:pos 512,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>512: if 544>a+8:c=c+100:t=512:y=112:o(62)=1:goto *atari
	if o(63)=0:pos 544,112:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>112: if 128>b+8: if a+8>544: if 576>a+8:c=c+100:t=544:y=112:o(63)=1:goto *atari
	
	if o(64)=0:pos 64,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>64: if 96>a+8:c=c+100:t=64:y=128:o(64)=1:goto *atari
	if o(65)=0:pos 96,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>96: if 128>a+8:c=c+100:t=96:y=128:o(65)=1:goto *atari
	if o(66)=0:pos 128,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>128: if 160>a+8:c=c+100:t=128:y=128:o(66)=1:goto *atari
	if o(67)=0:pos 160,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>160: if 192>a+8:c=c+100:t=160:y=128:o(67)=1:goto *atari
	if o(68)=0:pos 192,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>192: if 224>a+8:c=c+100:t=192:y=128:o(68)=1:goto *atari
	if o(69)=0:pos 224,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>224: if 256>a+8:c=c+100:t=224:y=128:o(69)=1:goto *atari
	if o(70)=0:pos 256,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>256: if 288>a+8:c=c+100:t=256:y=128:o(70)=1:goto *atari
	if o(71)=0:pos 288,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>288: if 320>a+8:c=c+100:t=288:y=128:o(71)=1:goto *atari
	if o(72)=0:pos 320,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>320: if 352>a+8:c=c+100:t=320:y=128:o(72)=1:goto *atari
	if o(73)=0:pos 352,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>352: if 384>a+8:c=c+100:t=352:y=128:o(73)=1:goto *atari
	if o(74)=0:pos 384,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>384: if 416>a+8:c=c+100:t=384:y=128:o(74)=1:goto *atari
	if o(75)=0:pos 416,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>416: if 448>a+8:c=c+100:t=416:y=128:o(75)=1:goto *atari
	if o(76)=0:pos 448,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>448: if 480>a+8:c=c+100:t=488:y=128:o(76)=1:goto *atari
	if o(77)=0:pos 480,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>480: if 512>a+8:c=c+100:t=480:y=128:o(77)=1:goto *atari
	if o(78)=0:pos 512,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>512: if 544>a+8:c=c+100:t=512:y=128:o(78)=1:goto *atari
	if o(79)=0:pos 544,128:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>128: if 144>b+8: if a+8>544: if 576>a+8:c=c+100:t=544:y=128:o(79)=1:goto *atari
	if o(0)=1:if o(1)=1:if o(2)=1:if o(3)=1:if o(4)=1:if o(5)=1:if o(6)=1:if o(7)=1:if o(8)=1:if o(9)=1:if o(10)=1:if o(11)=1:if o(12)=1:if o(13)=1:if o(14)=1:if o(15)=1:if o(16)=1:if o(17)=1:if o(18)=1:if o(19)=1:if o(20)=1:if o(21)=1:if o(22)=1:if o(23)=1:if o(24)=1:if o(25)=1:if o(26)=1:if o(27)=1:if o(28)=1:if o(29)=1:if o(30)=1:if o(31)=1:if o(32)=1:if o(33)=1:if o(34)=1:if o(35)=1:if o(36)=1:if o(37)=1:if o(38)=1:if o(39)=1:if o(40)=1:if o(41)=1:if o(42)=1:if o(43)=1:if o(44)=1:if o(45)=1:if o(46)=1:if o(47)=1:if o(48)=1:if o(49)=1:if o(50)=1:if o(51)=1:if o(52)=1:if o(53)=1:if o(54)=1:if o(55)=1:if o(56)=1:if o(57)=1:if o(58)=1:if o(59)=1:if o(60)=1:if o(61)=1:if o(62)=1:if o(63)=1:if o(64)=1:if o(65)=1:if o(66)=1:if o(67)=1:if o(68)=1:if o(69)=1:if o(70)=1:if o(71)=1:if o(72)=1:if o(73)=1:if o(74)=1:if o(75)=1:if o(76)=1:if o(77)=1:if o(78)=1:if o(79)=1:dim o,80:mmload "oto4.wav":mmplay
	redraw 1
	await 16
	goto *ball

*atari
	mci "play oto5.wav"
	if y+8>b+8:z=-1:goto *atari2
	if b+8>y+8:z=1
*atari2
	if t+30>a+8:w=1:goto *ball
	if a+8>t+2:w=1
	await 16
	goto *ball
*OVER
	cls 0
	font "HGPｺﾞｼｯｸE",22,0
	pos 275,50
	mes "GAME OVER"
	pos 285,100
	font "ＭＳ ゴシック",16,0
	mes "SCORE"
	mes c
	pos 225,350
	button "START",*shoki2
	mes ""
	button "EXIT",*EXIT
	stop

*PAUSE
	redraw 0
	font "HGPｺﾞｼｯｸE",22,0
	color 255,255,255 : boxf : color : pos 0,0
	pos 241,100
	mes "PAUSE"
	getkey j,2
	if j=1 :goto *ball
	redraw 1
	await 16
	goto *PAUSE

*EXIT
	END

</pre>

😂🤣😅🙄😥

レビューしてたらきりが無さそうですが、ツッコミどころを頑張って 3 つに絞り込んでみました。

### ローマ字を使っている

ところどころにローマ字があります。
たとえば、「shoki」は初期化処理のことです。
「oto1.wav」は効果音 1 の音声ファイルですね。
「atari」は、ボールとブロックが当たったときの処理のことです、まるで[ゲーム会社](<https://ja.wikipedia.org/wiki/%E3%82%A2%E3%82%BF%E3%83%AA_(%E4%BC%81%E6%A5%AD)>)を彷彿させるネーミングです。

もちろん、こんなネーミングはダメです。
ちゃんと英語を使いましょう、「brock.bmp」のように。
これはブロックの画像ファイルです、brock、block?……
まあ、中学生だし英語のスペルミスは許してね。

### 変数名は 1 文字

プログラムを難読化させている大きな原因の 1 つですね。
変数名は `m=0, c=0, w=1` のように 1 文字です。

変数 x, y がボールの座標だと思うでしょう？
実は、変数 a, b がボールの座標です。
それでは `x=150` と初期化している変数 x は何に使用しているかというと……未使用です。
つまり、初期化しているのに一切使用していません（笑）

そのほか、スコアを記憶する変数が c（s ではない）など、初期の BASIC っぽいですね。
確か BASIC は変数名 2 文字までという制約がありませんでしたっけ？

### 怒涛のブロック処理

そして極め付きは、**ループを使わないブロックの処理**です。
画面上のブロック 80 個に対して、1 つ 1 つ丁寧にプログラムを記述しています。
おそらく中学生の私はループ処理すら知らなかった、こんなころがあったんですね。
限られた知識の中で強引にプログラムを書いたのは、若い力というものでしょうか。

<pre class="prettyprint">
if o(0)=0:pos 64,64:gsel 0:gmode 1,32,16,150:gcopy 1:if b+8>64: if 80>b+8: if a+8>64: if 96>a+8:c=c+100:t=64:y=64:o(0)=1:goto *atari
</pre>

一応紹介すると、`o(0)` はブロックを消したフラグです。
`if o(0)=0:pos 64,64:gsel 0:gmode 1,32,16,150:gcopy 1` によって、ブロックを消していなければ画面に描画しています。

`if b+8>64: if 80>b+8: if a+8>64: if 96>a+8` は、ボールとブロックの当たり判定です。
当たっていたら `c=c+100` で 100 点加算し、`o(0)=1` でブロックを消して `goto *atari` によってボールのはね返しています。

なお、↓ のプログラムではブロックをすべて消したことを if 文 80 連結（!?）で行い、ゲームクリアの処理を行っています。

<pre class="prettyprint">
if o(0)=1:if o(1)=1:if o(2)=1:if o(3)=1:…(省略)…if o(79)=1:dim o,80:mmload "oto4.wav":mmplay
</pre>

## コードから今を考える

さて、私がなぜこのようなコードをみなさんに紹介したかというと……

それは、最近**このコードとは真逆の状況**をよく見かけるからです。

つまりプログラミング言語の進化はもちろん、プログラムを機能ごとに独立させて保守性を保つ[マイクロサービス](https://ja.wikipedia.org/wiki/%E3%83%9E%E3%82%A4%E3%82%AF%E3%83%AD%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9)
など、コードをきれいに保つための技術が浸透しています。
しかし、**コードのきれいさを意識するあまり、それが負担となっている**場面をときどき見かけることがあります。

ブロック崩しのコードは、当時の私では手段を選べなかったために**目的へ一直線**であったことの現れでした。
いま、いろんな手段を選べるようになった結果、**一番良い手段を選ばなくてはならないという強迫概念**に陥ることはないでしょうか？

### 本質的なコードレビューを阻害する

コードはほかの人にとっても読みやすくあるべきですね。
しかしそれを重視した結果、コードレビューが進まなくなる場面を見かけます。
たとえばコードレビューをした結果、プログラムの動作上の問題は指摘されないが、コードスタイルに対する指摘ばかりが続いていたとします。
このようなレビューでは、[コーディング規約](https://ja.wikipedia.org/wiki/%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E4%BD%9C%E6%B3%95)を定めれば解決ですね。
逆にコーディング規約のない状態でのコードスタイルのレビューは、**複数ある正解のうち 1 つだけしか正解として認められないようなもの**です。

### システムの寿命を無視する

「エンジニアは、長期間の運用を想定したコーディングをしなければなりません」

とてもそのとおりのように聞こえるでしょう？
しかし、これは厳密には**偽 (false) **です。
理由は簡単で、この命題が成り立たないシステムも存在するからです。
（「いや、うちの組織では……」という条件はつけないことにします 😥）

丁寧にコーディングすることによる恩恵は、その後のメンテナンスのときなどに得られますよね。
そこで、そのシステムの**改修頻度 × 期間**という指標を想定してみるのはどうでしょうか？
すると、システムの寿命を無視して過度に丁寧なコーディングをしていた箇所が見つかるかもしれません。

### 時間調整としてリファクタリングする

これはエンジニアとしての立場を利用したものですね。
改修を続けていくために、いずれは**技術的負債を解消するため**にリファクタリングが必要になります。
しかし、それに必要な工数や期間は非エンジニアには判断ができないため、エンジニアを大切にしてくれる組織では判断を任せてくれることがあります。

これを、エンジニアの保身的な都合で調整することはとても悲しいことです。
幸いにもエンジニアの作業進捗は、[バージョン管理システム](https://ja.wikipedia.org/wiki/%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E7%AE%A1%E7%90%86%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0)のコミットとして可視化しやすいものだと思います。
オープンでありたいですね。

## <!-- textlint-disable -->リーダブルコード<!-- textlint-enable -->は should であり must ではない

このように、<!-- textlint-disable -->リーダブルコード<!-- textlint-enable -->は必ず守らなくてはならないというわけではありません。
（組織の取り決めによるものは除きます）。
コードの品質を維持する負担との兼ね合いを考えないと、**<!-- textlint-disable -->リーダブルコード<!-- textlint-enable -->が強迫観念になるかもしれません**。

## 自動整形や lint を導入しよう

負担を抑えつつコードの品質を高めるには、**自動整形ツール**や**静的コード解析** (lint) を導入することをお勧めします。
これらを一度導入すれば、以降はノンコストでコードスタイルを統一したり、潜在的なバグを発見したりできます。

さらに、動作保証を客観的に示す場合などには、**自動テスト**のコードを書くことも有用ですね。
このようなツールがオープンで公開されていることは、とても恩恵だと思います 🎉 　
[husky](https://www.npmjs.com/package/husky) を使った自動整形や lint については

[【PHPMD 対応】husky & lint-staged で CI を実行する](/articles/husky/)

にて紹介していますので、よろしければ参考にしてくださいね。
