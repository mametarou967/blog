---
title: "ATOM LITEでCAN UNITを試す"
date: 2021-11-10T21:12:41+09:00
draft: false
tags:
- Atom Lite
- 調査
- CAN
description: ATOM LITEでCAN通信を試します。
---

## 試すこと

1対1のCAN通信をやってみたかったため、ATOM LITEとCAN UNITを使って試してみました。

## 材料

* ATOM LITE x 2
* CAN UNIT x 2
* GROVEケーブル x 2
* 抵抗120Ω x 2
* ワイヤ x 5

## 結線図

{{< figure src="/images/post/ATOMLITEでCANUNITを試す/kessenzu.JPG" class="center" >}}

HとLの間にそれぞれ120Ωを入れるところがポイントです。
抵抗は各終端に入れればよいので、ノード数が増えても抵抗の数は変わりません。
「High Speed CAN」などで画像検索すると、参考になる図が出てきます。

## 結線した様子

{{< figure src="/images/post/ATOMLITEでCANUNITを試す/kessenyousu.JPG" class="center" >}}


## 実行手順

* CAN SEND、CAN RECEIVE双方のATOM LITEに給電する
このとき、CAN RECEIVE側はパソコンなどにつないでシリアルモニタで受信の様子を観測する
* CAN SENDのATOM LITEのボタンを押す
* うまくデータが送信されていれば、シリアルモニタに以下のように表示される

```
rst:0x1 (POWERON_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 188777542, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:1
load:0x3fff0018,len:4
load:0x3fff001c,len:1044
load:0x40078000,len:8896
load:0x40080400,len:5816
entry 0x400806ac
M5Atom i⸮
CAN Unit Receive
15054,100,8,1,2,3,4,5,6,7,8, ← 受信できたフレーム
```

無事値がとれました。抵抗120Ωを入れないと値が全く取れないので、表示されない場合は結線を疑ってみると良いと思います。


## コード


* [CAN SENDのライブラリ](https://github.com/m5stack/M5-ProductExampleCodes/tree/master/Unit/CAN/CAN_SEND)をソースコードと同じ位置に配置してください
* ATOM LiteのCANフレーム送信側のコード

```C
#include "M5Atom.h"
#include "ESP32CAN.h"
#include "CAN_config.h"

#define TX_PORT GPIO_NUM_26
#define RX_PORT GPIO_NUM_32

CAN_device_t CAN_cfg;

// CAN初期化処理
void func_can_init(){
  CAN_cfg.speed=CAN_SPEED_500KBPS;
  CAN_cfg.tx_pin_id = TX_PORT;
  CAN_cfg.rx_pin_id = RX_PORT;
  CAN_cfg.rx_queue = xQueueCreate(10,sizeof(CAN_frame_t));
  //start CAN Module
  ESP32Can.CANInit();
}

// 初期化処理
void setup(){

  // M5Stack初期化
  M5.begin(true, false, true);
  Serial.begin(115200);

  // CANモジュール初期化
  func_can_init();
  
  // 起動時LED点灯
  M5.dis.drawpix(0, 0x00f0f0);  // 紫

}

// メイン処理
void loop(){

  CAN_frame_t rx_frame;

  if (M5.Btn.wasPressed()){

    rx_frame.FIR.B.FF = CAN_frame_std;
    rx_frame.MsgID = 0x100;
    rx_frame.FIR.B.DLC = 8;
    rx_frame.data.u8[0] = 0x01;
    rx_frame.data.u8[1] = 0x02;
    rx_frame.data.u8[2] = 0x03;
    rx_frame.data.u8[3] = 0x04;
    rx_frame.data.u8[4] = 0x05;
    rx_frame.data.u8[5] = 0x06;
    rx_frame.data.u8[6] = 0x07;
    rx_frame.data.u8[7] = 0x08;
        
    ESP32Can.CANWriteFrame(&rx_frame);
    Serial.println("Send Message 1 ");

    delay(100);
  }
  delay(50);
  M5.update();
}
```

* [CAN RECEIVEのライブラリ](https://github.com/m5stack/M5-ProductExampleCodes/tree/master/Unit/CAN/CAN_RECEIVE)をソースコードと同じ位置に配置してください
* ATOM LiteのCANフレーム受信側のコード

```C
#include "M5Atom.h"
#include "ESP32CAN.h"
#include "CAN_config.h"

#define TX_PORT GPIO_NUM_26
#define RX_PORT GPIO_NUM_32

CAN_device_t CAN_cfg;

// CAN初期化処理
void func_can_init(){

  CAN_cfg.speed=CAN_SPEED_500KBPS;
  CAN_cfg.tx_pin_id = TX_PORT;
  CAN_cfg.rx_pin_id = RX_PORT;
  CAN_cfg.rx_queue = xQueueCreate(10,sizeof(CAN_frame_t));
  //start CAN Module
  ESP32Can.CANInit();
}

// 初期化処理
void setup(){

  // M5Stack初期化
  M5.begin(true, false, true);
  Serial.begin(115200);
  
  // 表示
  Serial.println("");
  Serial.println("CAN Unit Receive");

  // CANモジュール初期化
  func_can_init();
  
  // 起動時LED点灯
  M5.dis.drawpix(0, 0x00f0f0);  // 紫
 
}

// メイン処理
void loop(){
    
  CAN_frame_t rx_frame;
  
  //キューからCANフレームを受信する
  if(xQueueReceive(CAN_cfg.rx_queue,&rx_frame, 3*portTICK_PERIOD_MS)==pdTRUE){
       
    Serial.print(millis());   // 受信時間
    Serial.print(",");
    Serial.print(rx_frame.MsgID,HEX);   // Msg ID
    Serial.print(",");
    Serial.print(rx_frame.FIR.B.DLC);   // DLC
    Serial.print(",");
 
    for(int i = 0; i < 8; i++){
      Serial.print(rx_frame.data.u8[i],HEX);   // CANデータ
      Serial.print(",");
    }
    printf("\n");
  }
}
```

## 参考

以下のページが大変参考になりました。
* [M5 ATOMとCAN-BUSユニットで、CANモニターツールを作る](https://knt60345blog.com/can-bus/)
* [M5Stackシリーズ（ATOM）とCAN-BUSユニットで、CAN送信ツールを作る。](https://knt60345blog.com/m5stack-can-tx/)