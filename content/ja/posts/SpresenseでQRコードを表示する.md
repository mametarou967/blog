---
title: "SpresenseでQRコードを表示する"
date: 2022-09-11T23:07:56+09:00
draft: false
tags:
- Spresense
- ILI9341
- QRコード
description: SpresenseでQRコードを表示する
---

## 概要

SpresenseとILI9341を接続し、画面にQRコードを表示してみます。

## 結線

|ILI9341のピン|Spresenseのピン|
|:---:|:---:|
|MISO|MISO|
|LED|3.3V|
|SCK|SCK|
|MOSI|MOSI|
|DC|PWM2|
|RESET|GPIO|
|CS|CS|
|GND|GND|

## ソース

* ILI9341液晶ディスプレイライブラリ
    * https://github.com/kzhioki/Adafruit_ILI9341
* グラフィックスライブラリ
    * https://github.com/kzhioki/Adafruit-GFX-Library

* spresense-qr-code-disp.ino
```C
/***************************************************
  This is our GFX example for the Adafruit ILI9341 Breakout and Shield
  ----> http://www.adafruit.com/products/1651

  Check out the links above for our tutorials and wiring diagrams
  These displays use SPI to communicate, 4 or 5 pins are required to
  interface (RST is optional)
  Adafruit invests time and resources providing this open source code,
  please support Adafruit and open-source hardware by purchasing
  products from Adafruit!

  Written by Limor Fried/Ladyada for Adafruit Industries.
  MIT license, all text above must be included in any redistribution
 ****************************************************/


#include "SPI.h"
#include "Adafruit_GFX.h"
#include "Adafruit_ILI9341.h"
#include "qrcode.h"

#define TFT_DC 9
#define TFT_CS -1
#define TFT_RST 8

#define X_BASE_PIXLE 60
#define Y_BASE_PIXLE 90
#define SCALE_SIZE 4 // x3

Adafruit_ILI9341 tft = Adafruit_ILI9341(&SPI, TFT_DC, TFT_CS, TFT_RST);

void setup() {
  Serial.begin(115200);
  Serial.println("ILI9341 Test!"); 
 
  tft.begin(40000000);

  // read diagnostics (optional but can help debug problems)
  uint8_t x = tft.readcommand8(ILI9341_RDMODE);
  Serial.print("Display Power Mode: 0x"); Serial.println(x, HEX);
  x = tft.readcommand8(ILI9341_RDMADCTL);
  Serial.print("MADCTL Mode: 0x"); Serial.println(x, HEX);
  x = tft.readcommand8(ILI9341_RDPIXFMT);
  Serial.print("Pixel Format: 0x"); Serial.println(x, HEX);
  x = tft.readcommand8(ILI9341_RDIMGFMT);
  Serial.print("Image Format: 0x"); Serial.println(x, HEX);
  x = tft.readcommand8(ILI9341_RDSELFDIAG);
  Serial.print("Self Diagnostic: 0x"); Serial.println(x, HEX); 
  
  // Create the QR code
  QRCode qrcode;
  uint8_t qrcodeData[qrcode_getBufferSize(3)];
  qrcode_initText(&qrcode, qrcodeData, 3, 0, "https://developer.sony.com/ja/develop/spresense/");
  
  // Serial.print(F("Text                     "));
  // Serial.println(testText());
  tft.fillScreen(ILI9341_WHITE);
  // tft.setCursor(50, 50);
  for (unsigned int y = 0; y < qrcode.size; y++) {
    for (unsigned int x = 0; x < qrcode.size; x++) {
        if (qrcode_getModule(&qrcode, x, y)){
          tft.writeFillRect(
            X_BASE_PIXLE +  (x * SCALE_SIZE),
            Y_BASE_PIXLE +  (y * SCALE_SIZE),
            SCALE_SIZE,
            SCALE_SIZE,
            ILI9341_BLACK);
        }
    }
    // Serial.print("\n");
  }

  tft.setCursor(40, 240);
  tft.setTextColor(ILI9341_BLACK);
  tft.setTextSize(3);
  tft.println("Spresense");
  
  delay(3000);

  Serial.println(F("Done!"));

}


void loop(void) {
  
}
```

### 結果

QRコードが表示されれば成功です。