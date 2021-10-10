---
title: "M5StickCでMP3モジュール(KT403A)を試す"
date: 2021-10-10T23:01:35+09:00
draft: false
tags:
- M5StickC
- 調査
image: images/postIcon/chousa1.png
---

## ハードウェア構成

* [Grove mp3 v2.0](https://wiki.seeedstudio.com/Grove-MP3_v2.0/)
* M5StickC

## Grove - MP3 v2.0の準備

1. micro-SDカードをフォーマットします
1. 任意のmp3ファイルを1つ以上SDカードにいれます
1. Grove mp3 v2.0にmicroSDカードを挿します
1. ardioジャックに任意のスピーカーを接続します
1. M5StickCとGROVE接続します

最終的に以下のようになります。

{{< figure src="/images/post/M5StickCでMP3モジュール(KT403A)を試す/connect.JPG"  >}}

## コード

* M5StickCのコード

```C
#include <M5StickC.h>

void setup(){
  M5.begin();
  Serial1.begin(9600, SERIAL_8N1, 33, 32); // EXT_IO
}

void play(int play_no){
  char sendBuffer[8] = { 0x7E,0xFF,0x06,0x03,0x00,0x00,play_no,0xEF };
  Serial1.write((uint8_t *)sendBuffer,8);
}

void loop() {
  M5.update();
  
  if ( M5.BtnA.wasPressed() ) {
    play(1);
  }
}
```


## 動作する様子

スピーカーの電源がONになっている状態でM5StickCのAボタンを押すと音声が再生されます。

{{< youtube "wnUyoZhIvbo" >}}

## KT403Aの仕様

[KT403のマニュアルの日本語版（非公式）](http://www.ne.jp/asahi/o-family/extdisk/MP3module/KT403A_DatasheetJP.pdf)を作ってくださった方がおり、大変参考になります。