---
title: "CAT M_UNIT+POVO_SIMでMQTTメッセージを送信してみる"
date: 2022-08-23T23:05:56+09:00
draft: false
tags:
- 調査
- M5Stack
- CAT-M
- POVO
- MQTT
description: M5StackにCAT-M UNIT+POVO_SIMを接続し、mqttのメッセージが送受信できるか試します。
---

## 概要

CAT-M UNITを使ってMQTTメッセージを送信し、サーバーでメッセージが受信できることを確認します。

## ハードウェア構成

M5StackとCAT-M Unitを接続します。

{{< figure src="/images/post/M5STACK+CAT-M_UNIT+POVO_SIMでMQTTメッセージを送受信してみる/構成1.JPG" class="center" >}}

CAT-MはUART通信ですがM5StackにはUART用のPORT.C相当のGroveコネクタがないので、[M5Stack PLUSエンコーダモジュール](https://www.switch-science.com/catalog/5206/)を重ねて接続しています。
（なければ、16,17PINをM5Stackの側面から引き出して接続します）

{{< figure src="/images/post/M5STACK+CAT-M_UNIT+POVO_SIMでMQTTメッセージを送受信してみる/構成2.JPG" class="center" >}}

M5StackとCAT-M Unitを接続します。接続には
## M5Stackのソース

以下のソースをM5Stackに書き込みます。
brokerはターゲットのサーバーのURLに修正します。
ソースの概要としては、CAT-Mを制御し、通信を確立します。
その後、topic:testから何かしらの文字列を受信すると、topic:test2に同じ文字列を送り返します。


```C
#define TINY_GSM_MODEM_SIM7080
#define TINY_GSM_RX_BUFFER 650 
#define TINY_GSM_YIELD() { delay(2); }
#include <TinyGsmClient.h>
#include <PubSubClient.h>

#include <M5Stack.h>

const char apn[]      = "povo.jp";
const char* broker = "XXX.com"; // your mqtt server

const char* topicTest       = "test";
const char* topicTest2       = "test2";
#define GSM_AUTOBAUD_MIN 9600
#define GSM_AUTOBAUD_MAX 115200

// TinyGsmClient
TinyGsm        modem(Serial1);
TinyGsmClient  client(modem);
PubSubClient  mqtt(client);

uint32_t lastReconnectAttempt = 0;

void mqttCallback(char* topic, byte* payload, unsigned int len) {
  M5.Lcd.clear(BLACK);
  M5.Lcd.setCursor(0, 0);
  M5.Lcd.print("Message arrived [");
  M5.Lcd.print(topic);
  M5.Lcd.println("]: ");
  M5.Lcd.print("bin:");
  for(int i = 0;i < len;i++){
    M5.Lcd.print(payload[i]);
  }
  M5.Lcd.println();

  char payloadBuf[256] = {0};
  strncpy(payloadBuf,(const char *)payload,len);
  
  String str = String(payloadBuf);
  M5.Lcd.print("str:");
  M5.Lcd.println(str);

  M5.Lcd.print("Message send to ");
  M5.Lcd.println(topicTest2);
  M5.Lcd.println(str);

  char buf[256];
  str.toCharArray(buf, 256);
  mqtt.publish(topicTest2, buf);
}

boolean mqttConnect() {
  M5.Lcd.print("Connecting to ");
  M5.Lcd.println(broker);

  boolean status = mqtt.connect("GsmClientTest");

  if (status == false) {
    M5.Lcd.println(" fail");
    return false;
  }
  M5.Lcd.println(" success");
  mqtt.subscribe(topicTest);
  return mqtt.connected();
}

void setup(){
  M5.begin();  //Init M5Core.  初始化 M5Core
  M5.Lcd.setTextSize(2);
  M5.Power.begin(); //Init Power module.  初始化电源模块
                    /* Power chip connected to gpio21, gpio22, I2C device
                      Set battery charging voltage and current
                      If used battery, please call this function in your project */
  Serial1.begin(9600, SERIAL_8N1, 16, 17);

  M5.Lcd.println("Wait...");  // Print text on the screen (string) 在屏幕上打印文本(字符串)

  // モデムのリスタート
  M5.Lcd.println("Initializing modem...");  // Print text on the screen (string) 在屏幕上打印文本(字符串)
  modem.restart();

  // モデムの情報の取得
  String modemInfo = modem.getModemInfo();
  M5.Lcd.print("Modem Info: ");
  M5.Lcd.println(modemInfo);

  M5.Lcd.print(F("Connecting to "));
  M5.Lcd.print(apn);
  if (!modem.gprsConnect(apn, "", "")) {
    M5.Lcd.println("-> fail");
    delay(10000);
    return;
  }
  M5.Lcd.println("-> success");

  if (modem.isGprsConnected()) { M5.Lcd.println("GPRS connected"); }

  mqtt.setServer(broker, 1883);
  mqtt.setCallback(mqttCallback);

  mqtt.publish(topicTest2, "Hello Server! I'm m5stack");
}

void loop() {
  if (!modem.isNetworkConnected()) {
      M5.Lcd.println("Network disconnected");
      
      if (!modem.waitForNetwork(180000L, true)) {
        M5.Lcd.println(" fail");
        delay(10000);
        return;
      }
      
      if (modem.isNetworkConnected()) {
        M5.Lcd.println("Network re-connected");
      }
      
      // and make sure GPRS/EPS is still connected
      if (!modem.isGprsConnected()) {
        M5.Lcd.println("GPRS disconnected!");
        M5.Lcd.print(F("Connecting to "));
        M5.Lcd.print(apn);
        if (!modem.gprsConnect(apn, "", "")) {
          M5.Lcd.println(" fail");
          delay(10000);
          return;
        }
        if (modem.isGprsConnected()) { M5.Lcd.println("GPRS reconnected"); }
      }
  }
      
  if (!mqtt.connected()) {
    M5.Lcd.println("=== MQTT NOT CONNECTED ===");
    // Reconnect every 10 seconds
    uint32_t t = millis();
    if (t - lastReconnectAttempt > 10000L) {
      lastReconnectAttempt = t;
      if (mqttConnect()) { lastReconnectAttempt = 0; }
    }
    delay(100);
    return;
  }
  
  mqtt.loop();
}
```

## サーバー側のソース

topic:test2からメッセージを受信すると、topic:testに同じ文字列を送信するプログラムです。

```python
# -*- coding: utf-8 -*-

# ブローカーに接続できたときの処理
def on_connect(client, userdata, flag, rc):
  print("Connected with result code " + str(rc))  # 接続できた旨表示
  client.subscribe("test2")  # subするトピックを設定

# ブローカーが切断したときの処理
def on_disconnect(client, userdata, rc):
  if  rc != 0:
    print("Unexpected disconnection.")

# メッセージが届いたときの処理
def on_message(client, userdata, msg):
  # msg.topicにトピック名が，msg.payloadに届いたデータ本体が入っている
  print("Received message '" + str(msg.payload) + "' on topic '" + msg.topic + "' with QoS " + str(msg.qos))
  client.publish("test",msg.payload)

# MQTTの接続設定
client = mqtt.Client()                 # クラスのインスタンス(実体)の作成
client.on_connect = on_connect         # 接続時のコールバック関数を登録
client.on_disconnect = on_disconnect   # 切断時のコールバックを登録
client.on_message = on_message         # メッセージ到着時のコールバック

client.connect("localhost", 1883, 60)  # 接続先は自分自身
```

## 動作確認

* サーバー側のプログラムをpython3で実行しておきます
* M5Stackの電源を入れます
* M5Stackがmqtt brokerにメッセージを送信するとサーバー側がメッセージを受信したタイミングで、M5Stackに同じ文字列を送り返します。そのまま、お互いがメッセージを永久に投げ続けます