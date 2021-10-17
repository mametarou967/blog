---
title: "M5StickCでPPD42を試す"
date: 2021-10-17T13:17:28+09:00
draft: false
tags:
- M5StickC
- 調査
- PPD42
image: images/postIcon/chousa1.png
---


## 結線図

|PPD42(ダストセンサー)|M5StickC|
|:---|:---:|
|1 : COMMON(GND)|GND|
|2 : OUTPUT(P2)|-|
|3 : INPUT(5VDC 90mA)|5V|
|4 : OUTPUT(P1)|26|
|5 : INPUT(T1)･･･FOR THRESHOLD FOR [P2]|-|

{{< figure src="/images/post/M5StickCでPPD42を試す/connect.JPG"  >}}

## コード

```C
#include <M5StickC.h> 

#define pin 26

unsigned long duration;
unsigned long starttime;
unsigned long sampletime_ms = 30000;
unsigned long lowpulseoccupancy = 0;
float ratio = 0;
float concentration = 0;

void setup(){
  M5.begin();
  M5.Lcd.setRotation(1);
  M5.Lcd.setTextSize(2);
  
  pinMode(pin,INPUT);
  
  starttime = millis();//get the current time;
  Serial.print("start");

}

// the loop routine runs over and over again forever
void loop() {
 duration = pulseIn(pin, LOW);
 lowpulseoccupancy = lowpulseoccupancy+duration;
 if ((millis()-starttime) > sampletime_ms)//if the sampel time == 30s
 {
   ratio = lowpulseoccupancy/(sampletime_ms*10.0); // Integer percentage 0=>100
   concentration = 1.1*pow(ratio,3)-3.8*pow(ratio,2)+520*ratio+0.62; // using spec sheet curve
   M5.Lcd.fillScreen(TFT_BLACK);
   M5.Lcd.setCursor(25, 20);
   M5.Lcd.printf("%4.2f pcs",concentration);
   M5.Lcd.setCursor(25, 40);
   M5.Lcd.printf("/0.01cf",concentration);
    
   lowpulseoccupancy = 0;
   starttime = millis();
 }
}
```

## 動かすときの注意

* PPD42を立てる

PPD42のダストセンサーはまっすぐ立てていないと正しい値が取れません。
ケーブルが下についているので動作確認をする際にポン置きしてしまいがちですが、以下のように正しい形で置いてください。

{{< figure src="/images/post/M5StickCでPPD42を試す/dousa.JPG"  >}}


## 参考

* [データシート](https://files.seeedstudio.com/wiki/Grove_Dust_Sensor/resource/Grove_-_Dust_sensor.pdf)
* [Groveダストセンサー](https://wiki.seeedstudio.com/jp/Grove-Dust_Sensor/)