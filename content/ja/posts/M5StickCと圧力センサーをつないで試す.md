---
title: "<調査> M5StickCと圧力センサーをつないで試す"
date: 2021-03-13T10:27:14+09:00
draft: false
tags:
- M5StickC
- 調査
image: images/postIcon/chousa1.png
---

## 背景

Interface 2018年 11月号をみていると、Esp32に圧力センサーが手軽につながるということで試してみたくなりました。

## 準備するもの

* 圧力センサー(FSR-406)
* ジャンパーワイヤー x 3
* 抵抗 3k(今回は1k + 2k)
* M5StickC

## 接続図と接続した様子

{{< figure src="/images/post/M5StickCと圧力センサーをつないで試す/接続図.jpg" class="center" >}}
{{< figure src="/images/post/M5StickCと圧力センサーをつないで試す/接続図2.jpg" class="center" >}}

## コード

M5StickCに書き込むコードです。コードは[こちら](https://github.com/mametarou967/ForceSensing)

### 読み取れる値

* 何も押していないと0
* 一番強く押すと4096程度(※使用しているanalogReadの最大値が4096)
* どうやら0V～3.3Vを0～4095の範囲で読み取るようです

## 動作する様子

{{< youtube IoDv-5ZEykc >}}

## 余談

今回はM5StickCのG36を使用しましたが他のPINでも大丈夫なようです。具体的には
esp32がアナログを読み取れるピンのうち、M5StickCが外部ピン入力ピンに採用しているものがそれになります。

まず、esp32のアナログが読み取れるピンですが,[こちら](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2019/05/adc-pins-esp32-f.jpg?w=700&quality=100&strip=all&ssl=1)のADCと書かれているピンがそれに該当するようです。
そして、それがどのようにM5StickC外部にでているかをしめしているのが[こちら](https://docs.m5stack.com/#/en/core/m5stickc)です。
これらより

* GPIO26
* GPIO36
* GPIO32
* GPIO33

がanalogReadできるようです。（実際に試してはいませんので保証はできません）
実際にGPIO32に接続してみると、同じように値を読むことができました。隣接しているPINが5Vなので、GPIO32,33あたりを選択するメリットはあまりないかもしれませんが...

と、ここまで試しておいてM5Stack系の解説に詳しい[Lang-ship](https://lang-ship.com/blog/work/m5stickc-io/#toc6)さんに詳しい解説がありました。
IO0はやはりanalogReadできないようです。


## 参考にしたページ

* [圧力センサーを使って、デスクワークでどれだけ寿命が縮んでるか測ってみた](https://qiita.com/s_fujii/items/7e3f27df05685ad15e0c)
* [ESP32 ADC – Read Analog Values with Arduino IDE](https://randomnerdtutorials.com/esp32-adc-analog-read-arduino-ide/)
* [【ESP32】analogReadする方法](https://rikoubou.hatenablog.com/entry/2017/06/29/135819)
* [M5StickCのIOについて調べてみた](https://lang-ship.com/blog/work/m5stickc-io/#toc6)
