---
title: "新しいAVRマイコンシリーズの紹介"
date: 2019-09-18 23:10:00 +0900
categories: diy
tags: diy electronics avr mcu
---

## 新しいAVRマイコンシリーズ

AVRマイコンの発売元Atmel社が、競合のPICマイコンの発売元Microchip社に買収されてから、新しいAVRマイコンシリーズが発売されました。

## ラインナップ

現在、発売されている新シリーズはtinyAVR 0シリーズ（ATtiny1607ファミリー）・tinyAVR 1シリーズ（ATtiny3217ファミリー）・magaAVR 0シリーズ（ATmega4809ファミリー）の３つです。今後tinyAVR 2シリーズ（ATtiny1627ファミリー）が出るようです。

型番の付け方は、上2桁がフラッシュROMのサイズを、その次がシリーズ名を、最後の桁がピン数を示すようです。ピン数は、2:4ピン、4:14ピン、6:20ピン、7:24ピン、8:32ピン、9:48ピンとなっています。

パッケージは、基本的に表面実装のパッケージのみですが、ATmega4809のみ40ピンDIPパッケージが用意されています。新AVRシリーズのプロトタイプにはATmega4809のDIP版を利用すればよさそうです。tinyシリーズは1.27mmピッチのSOICがあるので、はんだ付けが苦手な人にも使いやすいと思います。

### tinyAVR 0-series

- ATtiny202
- ATtiny204
- ATtiny402
- ATtiny404
- ATtiny406
- ATtiny804
- ATtiny806
- ATtiny807
- ATtiny1604
- ATtiny1606
- ATtiny1607

### tinyAVR 1-series

- ATtiny212
- ATtiny214
- ATtiny412
- ATtiny414
- ATtiny416
- ATtiny417
- ATtiny814
- ATtiny816
- ATtiny817
- ATtiny1614
- ATtiny1616
- ATtiny1617
- ATtiny3216
- ATtiny3217

### tinyAVR 2-series

- ATtiny1624
- ATtiny1626
- ATtiny1627

### magaAVR 0-series

- ATmega808
- ATmega809
- ATmega1608
- ATmega1609
- ATmega3208
- ATmega3209
- ATmega4808
- ATmega4809

## アーキテクチャ

今回のtiny, megaマイコンともXMEGA系のアーキテクチャに属していて、tinyやmegaという名称はピン数のみで区別するようになったようです。

以前はtinyマイコンはハードウェア乗算器が無いなど劣るアーキテクチャでしたが、このシリーズからはmegaと同様に乗算器が載り、小ピンでも高性能なCPUコアが使用できます。

従来のAVRマイコンではRAMのアドレス空間とフラッシュROMのアドレス空間が分離されていて、フラッシュへの読み書きは専用の命令を介して行う必要がありました。このシリーズではフラッシュもNVMコントローラーを通してRAMと同一アドレス空間に属したことにより、ld命令・st命令を使ってアクセスできるようになりました。アクセスのためのクロック数が減少したわけではないですが、アセンブリでプログラミングするのが楽になりました。

![ATtiny161xのメモリーマップ](https://mngu.net/blog/assets/images/introduction-of-the-new-avr-series/memory_map.png)

AVRマイコンのアドレス幅は16bitなので最大空間は64KBです。そのため、各種レジスタとRAMとROMを同一空間上に配置すると48KBのフラッシュが最大となるでしょう。64KB以上の大規模なプログラムとなると32bitマイコンの方が有利と感じているので、大容量品を切り捨ててうまく住み分けを図ってるなと思います。

## 周辺

tiny、megaと名前がついていてもXMEGA系に属しているように周辺も豊富に乗っています。DMAが無いなどXMEGAには若干劣りますが、XMEGAと従来のmegaの中間くらいでしょうか。

従来のtinyは通信機能が無かったりタイマーが貧弱だったりしたので、驚くほど豪華になった気がします。

- 最大48KBのフラッシュROM、最大6KBのRAM、最大256BのEEPROM
- 20MHz(16MHzに変更可能)の発振器
- 32kHzの低消費電力発振器
- 3つのモードのスリープ（アイドル・スタンバイ・パワーダウン）
- イベントシステム
- 2レベル割込み
- 16bitタイマー（TCA, TCB)　※TCAはmegaAVR 0-seriesのみ
- 12bitタイマー（TCD）　※tinyAVR 1-Seriesのみ
- リアルタイムクロック（RTC）
- ウォッチドッグ　タイマー（WDT）
- USART
- TWI(I2C)
- SPI
- 構成可能なカスタムロジック（CCL）
- アナログコンパレーター（AC）
- 10bit(12bit)アナログデジタルコンバーター（ADC）　※tinyAVR 2-Seriesのみ12bit
- 8bitデジタルアナログコンバーター（DAC）　※tinyAVR 1-Seriesのみ
- 内部基準電圧
- 1ピンしか占有しないプログラム・デバッグインターフェース（UPDI）

### クロック

デフォルトで20MHzで発振する内部発振器が搭載されました。外付けクロック源なしにAVRマイコンの最大周波数で動作させることができます。この内部発振器はFUSEを変更することで16MHzの発振に変更することができるので、2の累乗分周比で切りの良い値が欲しい時は変更するといいかもしれません。

内部発振器が進化した代償として、外部発振回路が取り除かれました。よって、水晶発振子やセラミック発振子を使ったクロックの供給は不可能です。水晶発振器やMEMS発振器など直接クロックを出力できるのものみに限られます。

### GPIO

GPIOはXMEGAと同様にトグルレジスタ・クリアレジスタ・セットレジスタに分かれ、特定のピンの制御が簡単にできるようになりました。しかしレジスタが増えてしまった影響で、シングルサイクルでアクセス可能なin, out命令、ビット単位で直接アクセス可能なsbi, cbi命令の範囲外にマップされているので、GPIOへのアクセスが遅くなってしまいました。

解決策としてIO空間内に仮想ポートレジスタ（VPORT）が用意されており、GPIOのレジスタを仮想ポートレジスタに結び付けることにより、仮想ポートレジスタを操作することでGPIOの高速なアクセスができる手段が用意されています。

### スリープモード

スリープモードは3つしかないように見えますが、スタンバイモードはどの周辺をスリープにするか選べるので実際の選択肢は増えました。

### USART

USARTはフラクショナル・ボーレート・ジェネレーターによりボーレートの設定が可能となりました。その名の通り、小数部分を含む数でクロックの分周を設定できるようになったので、高精度なボーレートを作り出すことができます。

### CCL

CCLは、論理ゲートやフィルター等に設定して周辺をつなぐことのできるものです。アプリケーションノートに、スイッチのチャタリングを除去してイベントを起こす例が載っています。[AVR® MCUのコア独立周辺モジュール入門](http://ww1.microchip.com/downloads/jp/AppNotes/00002451B_JP.pdf)

## マイコンへの書き込み

従来から使われていたISP, TPI, PDI, HVPP, HVSP, JTAGなどは使用できず、UPDI(Unified Program and Debug Interface)という新しいインターフェースに統一されました。

電子工作愛好者に利用者が多いと思われるAVRISP mkIIは、UPDIには対応していないため使うことはできません。ATMEL ICEを利用する必要があります。

しかしUPDI対応プログラマは高価なため、安価なUSB-UART変換チップを使った書き込み器とそれ用の書き込み用ソフトを自作しました。このUPDIプログラマーに関しては記事を分けましたので、そちらの記事を参照してください。プログラムはOSSとして公開しているので、新AVRシリーズを使おうとしている方に使ってもらえたらすごくうれしいです。

[AVR UPDI プログラマー](https://mngu.net/diy/avr-updi-programmer.html)  
すみません。現在、記事を書いている途中なのでリンク切れです。

## クロスコンパイル

新しいマイコンのため、そのままのlibcやgccは対応ができていないので使えません。そこで、Atmel(Microchip)から提供されているDevice Family Packsというものをダウンロードして、libcやgccを拡張して使用します。

Device Family Packsは[Microchip Packs Repository](https://packs.download.microchip.com/)からダウンロードします。

※古い方のリポジトリ[http://packs.download.atmel.com/](http://packs.download.atmel.com/)と新しい方のリポジトリは[https://packs.download.microchip.com/](https://packs.download.microchip.com/)が存在します。残念なことにATtinyの新しい方（バージョン２以降）はリンクでコケるようで使えません。現時点でtinyマイコンを使うときは古い方をダウンロードしましょう。

Device Family Packsの使い方を解説します。

ダウンロードするとMicrochip.ATxxxx_DFP.x.x.xx.atpackのような名前のファイルがダウンロードされます。実態はZIPファイルなので、.atpackを.zipに変更して適当なフォルダに展開します。

展開した中のフォルダには、AVRマイコンのレジスタの定義などが記述されたXMLファイルや、ヘッダーファイル、ライブラリなどが入っているのが見えます。

あとは、GCCでコンパイルする際にこのヘッダーファイルが入っているフォルダとライブラリファイルが入っているフォルダを-Iオプションと-Bオプションとして渡してあげます。

例えば、ホームフォルダにatpackを展開して、main.cファイルをATmega4809用にコンパイルする際は、

<pre><samp>$ <kbd>avr-gcc -mmcu=atmega4809 -B ~/Microchip.ATmega_DFP.2.0.12/gcc/dev/atmega4809/ -I ~/Microchip.ATmega_DFP.2.0.12/include/ main.c -o main.o</kbd></samp></pre>


とします。

## 実際に新しいAVRシリーズを使ってみる

記事が長くなりそうなので、別の記事で書こうと思います。

[新しいAVRシリーズを使ってみる](https://mngu.net/diy/playing-with-the-new-avr-series.html)  
すみません。現在、記事を書いている途中なのでリンク切れです。
