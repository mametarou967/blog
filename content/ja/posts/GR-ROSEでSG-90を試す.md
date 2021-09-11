---
title: "<調査> GR-ROSEでSG-90を試す"
date: 2021-09-11T23:00:00+09:00
draft: false
tags:
- GR-ROSE
- 調査
image: images/postIcon/chousa1.png
---


## ハードウェア構成

* 電源コネクタに5Vの電源線をつなぎます
* シリアル通信(UART)用コネクタにサーボモーターSG-90をつなぎます
    * Servo1:1pin-Vm-G
    * Servo2:3pin-Vm-G
    * Servo3:5pin-Vm-G
    * Servo4:7pin-Vm-G
* ピンアサインは[GR-ROSEのページ](https://www.renesas.com/jp/ja/products/gadget-renesas/boards/gr-rose)より「GR-ROSEピンマップ」を参照のこと


## つないだ様子

{{< figure src="/images/post/GR-ROSEでSG-90を試す/hardware.JPG" >}}

## 動作の様子

{{< youtube o7L58XRCO3Y >}}

## ソース

```C
#include <Arduino.h>
#include <servo.h> 
 
#define INTERVAL 5000
Servo servo1;
Servo servo2;
Servo servo3;
Servo servo4;
 
void setup() 
{ 
    servo1.attach(1);
    servo2.attach(3);
    servo3.attach(5);
    servo4.attach(7);
}
 
void loop() {
    servo1.write(0);
    servo2.write(0);
    servo3.write(0);
    servo4.write(0);
    delay(INTERVAL);
    servo1.write(180);
    servo2.write(180);
    servo3.write(180);
    servo4.write(180);
    delay(INTERVAL);
} 
```