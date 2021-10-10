---
title: "M5StickC同士でシリアル通信する"
date: 2021-10-10T20:19:53+09:00
draft: false
tags:
- M5StickC
- 調査
image: images/postIcon/chousa1.png
---

## 結線図

M5StickCのIOをそれぞれ以下のように接続します

* M5StickC1のG26 -> M5StickC2のG0
* M5StickC1のG0 -> M5StickC2のG26

{{< figure src="/images/post/M5StickC同士でシリアル通信する/connect.JPG"  >}}

## コード

```M5StickC
#include <M5StickC.h>

void setup(){
  M5.begin();
  Serial1.begin(9600, SERIAL_8N1, 0, 26); // EXT_IO
}

void loop() {
  // Buttonクラスを利用するときには必ずUpdateを呼んで状態を更新する
  M5.update();
  
  if ( M5.BtnA.wasPressed() ) {
    Serial1.write(1);
  }

  if (Serial1.available()) {
    int inByte = Serial1.read();
    M5.Lcd.printf("%d",inByte);
  }
}
```

## 結果

各M5StickCのＡボタンを押すと反対のM5StickCの液晶に1が表示されましたので、通信できていることがわかります。

{{< figure src="/images/post/M5StickC同士でシリアル通信する/dousa.JPG"  >}}
