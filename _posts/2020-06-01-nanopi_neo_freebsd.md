---
title: "NanoPi NEOでFreeBSDを動かしてみた"
date: 2020-06-01 22:23:00 +0900
categories: computer
tags: computer nanopi sbc freebsd
---

## NanoPi NEO

NanoPi NEOは4cm正方形のシングルボードコンピューターです。Allwinner H3っていうSoCが載っています。秋月電子でも取り扱っていて、DRAMが512MBのものが¥2280です。前は¥1680だったのですが値上がりしたようです。

## FreeBSDのダウンロード

NanoPi NEOには専用のイメージは提供されていません。ARMアーキテクチャ用にGENERICSDが提供されているのでこれを使います。記事を書いた時点での最新版は12.1Rです。

[FreeBSD 12.1 RELEASE arm armv7 GENERICSD.img.xz](https://download.freebsd.org/ftp/releases/arm/armv7/ISO-IMAGES/12.1/FreeBSD-12.1-RELEASE-arm-armv7-GENERICSD.img.xz)

xzで圧縮されているので展開するとSDカードイメージができます。

以前はcrochetというツールを使ってコンパイルしてたのですが、格段に楽になりました。

## ブートローダーのダウンロード

OSを起動する前にはブートローダーが起動します。NanoPi NEOでFreeBSDを起動するためにはU-Bootというブートローダーを用いて起動します。

FreeBSDのpkgにNanoPi NEO用のU-Bootが用意されているのでインストールしましょう。

<pre><samp>% <kbd>pkg install u-boot-nanopi_neo</kbd></samp></pre>

インストールするとパッケージが`/usr/local/share/u-boot/u-boot-nanopi_neo/`以下に置かれています。`u-boot-sunxi-with-spl.bin`がブートローダーのバイナリ本体です。

パソコンはMacを使っているのでVPSにFreeBSDの環境を用意してパソコンにダウンロードしました。

## SDカードへブートローダーとOSの書き込み

まずはOSイメージをSDカードに書き込み、その後にブートローダーを書き込みます。先にブートローダーを書き込んでしまうとOSイメージで上書きされてしまいます。

これ以降はMac上で行いました。

まずは、SDカードをパソコンに挿して、どのデバイスか確認します。

<pre><samp>% <kbd>diskutil list</kbd></samp></pre>

自分の環境では`/dev/disk2`でした。自動でマウントされてしまっているのでアンマウントします。

<pre><samp>% <kbd>diskutil unmountDisk /dev/disk2</kbd></samp></pre>

ddでSDカードにOSイメージを書き込みます。ifとofで指定するデバイスを間違うと大変なことになるのでとても注意してください。

<pre><samp>% <kbd>sudo dd if=~/Downloads/FreeBSD-12.1-RELEASE-arm-armv7-GENERICSD.img of=/dev/rdisk2 bs=1m</kbd></samp></pre>

書き込みに数分かかるとおもいます。気長に待ちます。

書き込みが終わると、たぶん自動でマウントされてしまうので、ブートローダーを書き込むために再度アンマウントします。

<pre><samp>% <kbd>diskutil unmountDisk /dev/disk2</kbd></samp></pre>

最後にブートローダーを書き込みます。書き込みの方法はpkgでインストールした先のREADMEに書いてあります。

<pre><samp>% <kbd>sudo dd if=~/Downloads/u-boot-nanopi_neo/u-boot-sunxi-with-spl.bin of=/dev/disk2 bs=1024 seek=8</kbd></samp></pre>

アンマウントしてSDカードリーダーから取り外します。

<pre><samp>% <kbd>diskutil unmountDisk /dev/disk2</kbd></samp></pre>

## NanoPi NEOの起動

SDカードをNanoPi NEOに挿して、USBシリアル変換器を介してNanoPi NEOとパソコンをつなぎます。ボーレートは115200です。

<pre><samp>% <kbd>screen /dev/tty.usbserial-XXXXXXXX 115200</kbd></samp></pre>

microUSBポートに電源を繋ぐと起動メッセージが流れてきます。

起動したら、rootかfreebsdユーザーでログインすることができます。（パスワードはユーザー名と同じ）

オーディオは認識されていないような気がしますが、USBもネットワークも認識されているのでサーバーなどに使うのには十分そうです。

## USB接続のSSDからの起動

U-Bootを確認してみると、USBを認識しているようなので、USB接続のSSDからFreeBSDの起動を試してみました。破損しやすい
SDカードからの起動を避けられるので、サーバーでの運用がしやすくなると思います。

U-Bootの起動の優先順位がSDカードになっているので、SDカードないのデータを全て0書き込みして、先ほど書き込んだOSイメージを消去しておきます。

U-Bootの書き込み方法は上記のやり方と同じです。

FreeBSDのイメージを書き込むときに、SDカードではなくUSB SSDを書き込み先に指定します。

あとは、SDカードをNanoPiに差して、SSDを接続して電源を繋げばSSDからFreeBSDが起動するようになります。

SDカードにFreeBSDのイメージが残ったままUSBからの起動ができないか試してみましたが、U-Bootの起動優先順位を変更してUSBからOSを起動することができたもののß、rootfsのマウントがSDカードからされてしまいUSBでの運用はできませんでした。力不足で解決できなかったのでSDカードはU-Bootのみにして使っていこうと思います。

## まとめ

GENERICSDイメージとpkgにあるNanoPi NEO用のU-Bootを使って簡単にFreeBSDを使う環境を立てることができました。

また、SSDからの起動もできたので自宅サーバーにしたいと思います。
