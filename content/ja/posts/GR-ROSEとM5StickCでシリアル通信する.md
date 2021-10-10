---
title: "GR-ROSEとM5StickCでシリアル通信する"
date: 2021-10-10T21:32:53+09:00
draft: false
tags:
- M5StickC
- GR-ROSE
- 調査
image: images/postIcon/chousa1.png
---

## 概要
 
GR-ROSEから何かデータを送って、M5StickCで受信したデータを表示する。ということをやります。

## 結線

M5StickCとGR-ROSEのIOをそれぞれ以下のように接続します。
今回はGR-ROSEのSerial4を使用します。

* GR-ROSEのPIN6 -> M5StickC2のG26
* GR-ROSEのPIN7 -> M5StickC2のG0

{{< figure src="/images/post/GR-ROSEとM5StickCでシリアル通信する/connect.JPG"  >}}

GR-ROSEは汎用的に使えるシリアル通信が7つ用意されており(そのうち一つはESP8266用で予約)どこにつないでも大丈夫と思われます。
※シリアル専用という意味ではSerial7を使うと他のリソースを圧迫しない?
シリアル番号とピンの対応とコードについては[基本ライブラリ-シリアル通信](https://www.renesas.com/jp/ja/products/gadget-renesas/reference/gr-rose/library-serial)を参照してください

## コード

* GR-ROSEのコード

```C
#include <Arduino.h>

void setup() {
	Serial4.begin(9600);
}

void loop() {
	Serial4.write(1);
	delay(1000);
}
```

* M5StickCのコード

```C
#include <M5StickC.h>

void setup(){
  M5.begin();
  Serial1.begin(9600, SERIAL_8N1, 0, 26); // EXT_IO
}

void loop() {
  M5.update();

  if (Serial1.available()) {
    int inByte = Serial1.read();
    M5.Lcd.printf("%d",inByte);
  }
}
```

## 結果

1秒ごとにM5StickCの液晶に1が表示されましたので、通信できて、GR-ROSEからデータが送信されていることがわかります。

{{< figure src="/images/post/GR-ROSEとM5StickCでシリアル通信する/dousa.JPG"  >}}