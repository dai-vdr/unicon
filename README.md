## IMPORTANT NOTICE

project discontinued (2009/10/26)

## 重要なおしらせ

VD UNICON のメンテナンスは 2009/10/26 に 2.6.31 対応 patch を最後に終了しました。長らくのご利用ありがとうございました。
現在、UTF-8 対応コンソールとして [utf-8 kernel](http://blog.chinaunix.net/u/13265/showart.php?id=1008020) が開発されています。なお、一切関与していません。

## はじめに

UNICON は Linux のフレームバッファ上で CJK(中国語/日本語/韓国語)を表示するための仕組みです (**UTF-8 には未対応**)。[TurboLinux 北京オフィス](http://www.turbolinux.com.cn/) にて開発されていましたが、長らく更新が停止した状態になっています。[dai](https://vdr.jp/d/) が非公式に VD UNICON の名称でメンテナンスを続けていましたが、2009/10/26 に 2.6.31 対応 patch を最後に終了しました。

この文章およびパッチなどは無保証です。UNICON による Linux 日本語 console テストに際し、[UNICON を使って日本語コンソールを実現しよう](http://web.archive.org/web/20060830162724/http://www.kip.iis.toyama-u.ac.jp/~shingo/unicon/) および [UNICON](http://web.archive.org/web/20050331013856/http://ppc.linux.or.jp/~ipenguin/unicon/) を参考にさせて頂きました。多謝。

## Kernel 編

### Kernel にパッチを当てる

* フォント: [vd\_unicon-kernel-fonts-20040205.patch.bz2](https://gitlab.com/vdr-jp/unicon/uploads/82d1ca0a0d9389e4888acfe65397bd8d/vd_unicon-kernel-fonts-20040205.patch.bz2)

* 2.6.31.* 用: [vd\_unicon-kernel-20091021-2.6.31.patch](https://gitlab.com/vdr-jp/unicon/-/raw/main/vd_unicon-kernel-20091021-2.6.31.patch?ref_type=heads)
* 2.4 用: [vd\_unicon-kernel-20040219-2.4.patch](https://gitlab.com/vdr-jp/unicon/-/raw/main/vd_unicon-kernel-20040219-2.4.patch?ref_type=heads)

kernel に フォントのパッチと VD UNICON 本体のパッチの両方を当てる。

これらのパッチは [Turbolinux](http://www.turbolinux.co.jp/) の kernel SRPM に含まれている UNICON パッチを元に、[UNICON を使って日本語コンソールを実現しよう](http://web.archive.org/web/20060830162724/http://www.kip.iis.toyama-u.ac.jp/~shingo/unicon/)、[Okpos.com:: Unicon](http://web.archive.org/web/20060616021311/http://www.okpos.com/wiki/pos/Unicon) にて提供されているパッチ、dai による修正を加えてある。

	% cd /usr/src/linux-2.*.*
	
	# font patch
	% bzcat .../vd_unicon-kernel-fonts-YYYYMMDD.patch.bz2 | patch -p1
	
	# unicon patch
	% cat .../vd_unicon-kernel-YYYYMMDD-2.*.patch | patch -p1

### Kernel の再構築

実験的機能のサポート、ローダブルモジュールのサポート、フレームバッファサポートをそれぞれ有効にし(詳細は [Framebuffer HOWTO](http://www.linux.or.jp/JF/JFdocs/Framebuffer-HOWTO.html) を参照)、 UNICON サポートを組み込み、表示したい言語のフォントをモジュールとする。

	CONFIG_UNICON=y
	CONFIG_UNICON_GB=m
	CONFIG_UNICON_GBK=m
	CONFIG_UNICON_BIG5=m
	CONFIG_UNICON_EUCJP=m
	CONFIG_UNICON_JIS=m
	CONFIG_UNICON_SJIS=m
	CONFIG_UNICON_KSCM=m

通常は EUCJP のみでかまわないだろう。 (GB/GBK は簡体字中国語、BIG5 は繁体字中国語、 EUCJP/JIS/SJIS は日本語、 KSCM は韓国語それぞれのフォントサポート)

### bootloader の設定

UNICON はフレームバッファ上で動作するため、フレームバッファを利用するように bootloader で設定を行う。詳細は [Framebuffer HOWTO](http://www.linux.or.jp/JF/JFdocs/Framebuffer-HOWTO.html)、コンソールを高解像度で表示するには([GRUB編](http://www.atmarkit.co.jp/flinux/rensai/linuxtips/332vtextgrub.html), [LILO編](http://www.atmarkit.co.jp/flinux/rensai/linuxtips/333vtextlilo.html))を参照。

2.6.24 以降、boot option に「[vt.default\_utf8=0](https://vdr.jp/d/20080412.html#p01)」が必要となる場合がある。

GNU GRUB では kernel 行に

	kernel /boot/vmlinuz root=/dev/sda1 ro video=vesafb vga=0x305 vt.default_utf8=0

LILO では append 行に

	append="vt.default_utf8=0"

のように追加する。

## Userland 編

### unicon-tools を取得

[unicon-tools-0.0.0.20050707.tar.gz](https://gitlab.com/vdr-jp/unicon/uploads/3faa2515069412cf4fa1e8feb1a5011c/unicon-tools-0.0.0.20050707.tar.gz) を拾ってくる。

これは [unicon-3.0.4-20010924.tar.gz](https://gitlab.com/vdr-jp/unicon/uploads/cb8719f90b565cb0a75d348e597a85fb/unicon-3.0.4-20010924.tar.gz) ([Turbolinux 北京オフィス](http://www.turbolinux.com.cn/)ではもう配布されていないようなのでここで配布) に [vd\_unicon-userland-20031122.patch](https://gitlab.com/vdr-jp/unicon/-/raw/main/vd_unicon-userland-20031122.patch?ref_type=heads) ([UNICON を使って日本語コンソールを実現しよう](http://web.archive.org/web/20060830162724/http://www.kip.iis.toyama-u.ac.jp/~shingo/unicon/)、[UNICON](http://web.archive.org/web/20050331013856/http://ppc.linux.or.jp/~ipenguin/unicon/) にて提供されているパッチ及び dai による修正)を当て、必要な部分のみを取り出したものである。

### unicon-tools の make

	% ./configure --prefix=/usr
	% make
	# make install

### デバイスファイルの作成

devfs, udev を使っていれば必要ない。

	# mknod /dev/unikey c 10 202
	# chmod 640 /dev/unikey

### 起動設定

以下を適当な boot script に追加。

	#!/bin/sh
	#
	# VD UNICON for EUC-JP
	
	if [ \( -f /lib/modules/`/bin/uname -r`/kernel/drivers/video/unicon/unikey.o \
	     -o -f /lib/modules/`/bin/uname -r`/kernel/drivers/video/unicon/unikey.ko \) \
	     -a -x /usr/bin/uniconctrl \
	     -a -e /dev/fb0 ]; then
	        /sbin/modprobe unikey
	        /sbin/modprobe encode-eucjp
	
	        # console-tools
	        if [ -x /usr/bin/consolechars ]; then
	                /usr/bin/consolechars --sfm /usr/lib/unicon/direct.uni
	        # kbd
	        elif [ -x /usr/bin/setfont ]; then
	                /usr/bin/setfont -u /usr/lib/unicon/direct.uni
	        # kbd (obsoleted)
	        elif [ -x /usr/bin/loadunimap ]; then
	                /usr/bin/loadunimap /usr/lib/unicon/direct.uni
	        fi
	
	        /usr/bin/uniconctrl --eucjp tty1
	        /usr/bin/uniconctrl --eucjp tty2
	        /usr/bin/uniconctrl --eucjp tty3
	        /usr/bin/uniconctrl --eucjp tty4
	        /usr/bin/uniconctrl --eucjp tty5
	        /usr/bin/uniconctrl --eucjp tty6
	fi

## おしらせ等

### ライセンス

元々の UNICON が GPL2 なので、この非公式パッチ VD UNICON も GPL2 として公開しています。

### 制限事項

**UTF-8 には対応していません**。例えば UTF-8 でのかな漢字は 3バイトありますが、UNICON は実装上の仕様により 2バイトまでしか扱えないため、UTF-8 を扱うことができません。かつて UNICON を **Uni**code **Con**sole と紹介している文書が散見されたことや、UNICON という語感から UTF-8 も扱えるという誤解があるようですが、UNICON は正確には **Uni**versal **Con**sole です。

### リンク

このページへのリンクは [https://vdr.jp/d/unicon.html](https://vdr.jp/d/unicon.html) として頂けると覚えやすいし閲覧時の見栄えもよいと思います。

## 貢献者(順不同,敬称略)

* YAMAGUCHI Shingo
* OHNO Tetsuji
* Katsumi Kikuchi
* Toshi

## 関連リンク

* [UNICON - CJK (Chinese/Japanese/Korean) console input and display system](http://directory.fsf.org/project/UNICON/) - 更新されていない。ML の紹介もあるが生きていないようだ。
* [UNICONで日本語コンソール](http://web.archive.org/web/20050331013856/http://ppc.linux.or.jp/~ipenguin/unicon/) - 日本語での UNICON ページと言えばここ。
* [UNICON を使って日本語コンソールを実現しよう](http://web.archive.org/web/20060830162724/http://www.kip.iis.toyama-u.ac.jp/~shingo/unicon/) - [山さん]("http://web.archive.org/web/20060831042009/http://www.kip.iis.toyama-u.ac.jp/~shingo/)のページ。山さんが卒業したのでその後を引き継いだ。
* [Okpos.com:: Unicon](http://web.archive.org/web/20060616021311/http://www.okpos.com/wiki/pos/Unicon) - Korean patch を配布。
* [Bug 4087 UNICON: CJK fonts support on console.](http://bugzilla.kernel.org/show_bug.cgi?id=4087) - なんと Kernel Bug Tracker に登録されてる。 ~~今後の動向に注目。~~ 残念ながら REJECTED WILL\_NOT\_FIX となった。
* [Re: Two questions about console utf8 support](http://mail.nl.linux.org/linux-utf8/2001-12/msg00097.html) - Console/FrameBuffer のメンテナ James Simmons の見解「They unfortunely are hacks that break other things.」
* [UNICON が SuSE 9.1 から外された理由](http://lists.opensuse.org/opensuse-ja/2004-08/msg00064.html) - 公式見解としていいのかわからないけれど、「パッチ自体がかなりひどいでき」とのこと。
* [Turbolinux 北京オフィス](http://www.turbolinux.com.cn/) - すべてはここから始まった...が、現在 UNICON に関する資料等は見当たらない。
* [Turbolinux](http://www.turbolinux.co.jp/) - 現在 UNICON の公式メンテナンスを行っている。
* [Linux日本語コンソール](http://www.bigfield.com/~hiroshi/jconsole.html) - jconsole。
* [Japanese text patch for Linux console](http://achurch.org/jcon-e.html) - jcon。
* [utf8-kernel](http://blog.chinaunix.net/u/13265/showart.php?id=1008020) - [UTF-8 console の本命](https://vdr.jp/d/20091023.html#p02)か。
