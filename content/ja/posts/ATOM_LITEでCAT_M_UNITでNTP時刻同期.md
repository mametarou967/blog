---
title: "ATOM_LITEでCAT_M_UNITでNTP時刻同期"
date: 2021-12-13T22:06:09+09:00
draft: false
tags:
- 調査
- CAT-M
- SORACOM
- NTP
---

# 準備

ATコマンドでできるかどうか確認

* GROVEにCAT-M UNITを接続しているものとします
* SORACOMのSIMがCAT-M UNITに入っており、最低一度通信できているものとします
* SORACOMのNTPサーバーは"ntp.soracom.io"です
    * [ここ](https://users.soracom.io/ja-jp/docs/air/endpoints/)に紹介されています
    * [このあたり](http://www.venus.dti.ne.jp/~yoshi-o/NTP/NTP-Table.html)のNTPサーバーにはなぜか到達できません

## ソース

```C
#include "M5Atom.h"
#include "M5_SIM7080G.h"

M5_SIM7080G device;
String readstr;

void log(String str) {
    Serial.print(str);
}

void setup()
{
    M5.begin(true,true,true);
    Serial.println("Atom Install OK....");
    
    //SIM7080
    device.Init(&Serial2, 32, 26);

    //SIM7080の初期化
    log("[log]module reset\r\n");
    device.sendMsg("AT+CREBOOT\r\n");
    delay(1000);
    while(1){
        log("[log]csq check\r\n");
        device.sendMsg("AT+CSQ\r\n");
        readstr = device.waitMsg(1000);
        log(readstr);
        if(readstr.indexOf("+CSQ: 99,99") ==-1){
            log("CSQ is OK\r\n");
            break;
        }
    }
    log("[log]waiting 5sec...\r\n");
    delay(5000);

    log("[log]channel activate\r\n");
    device.sendMsg("AT+CNACT=0,1\r\n");
    readstr = device.waitMsg(200);
    log(readstr);
    
    log("[log]ntp server set up\r\n");
    device.sendMsg("AT+CNTP=\"ntp.soracom.io\",9,0,2\r\n");
    readstr = device.waitMsg(200);
    log(readstr);
    
    log("[log]ntp server utc request\r\n");
    device.sendMsg("AT+CNTP\r\n");
    readstr = device.waitMsg(200);
    log(readstr);    
    readstr = device.waitMsg(1000);
    log(readstr);
}


void loop()
{
    Serial.println("Hello");
    //waitCANData
    M5.update();
    if(M5.Btn.isPressed())
    {
        esp_restart();
    }
    delay(10000);
}
```

