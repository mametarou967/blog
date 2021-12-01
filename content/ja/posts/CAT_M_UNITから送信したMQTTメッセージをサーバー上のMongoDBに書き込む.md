---
title: "CAT_M_UNITから送信したMQTTメッセージをサーバー上のMongoDBに書き込む"
date: 2021-12-01T22:32:21+09:00
draft: false
tags:
- 調査
- CAT-M
- SORACOM
- MQTT
description: MQTTメッセージをサーバー上のMongoDBに書き込んでみます
---

## 構成

1. CAT-Mに接続されたEDGE DEVICEからMQTTのメッセージをサーバーのmqtt brokerに送信する
    * topic:edge/1
    * payload:任意
1. サーバー上で実行中のプログラムがMQTTのメッセージを受信し、MongoDBに書き込む
    * db:can_data
    * collection:edge_device

①MongoDBの準備、②Server上のプログラム、③EDGE DEVICEの操作の順に書きます

## MongoDBの準備

さくらのVPSのCentOS8上で試します。
[How To Install MongoDB on CentOS 8](https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-centos-8)実行すればMongoDBがインストールできます

MongoDBインストール後に、書き込み先のDBとCollectionを作っておきます
```
> mongo // 操作モードに遷移
> use can_data  // dbの作成 今回はcan_dataとする
> db.createCollection('edge_device') // collectionの作成 今回はedge_deviceとする
```
db:can_data
collection:edge_device
にMQTTで受信したデータを書き込みます。

## MQTTで受信したデータをMongoDBに書き込むプログラム

mosquitto等のmqttブローカーがインストールされていることを前提として、以下のプログラムを実行します。
topic:edge/1で受信したpayloadをdb:can_data,collection:edge_deviceに書き込みます。
deamon serviceにして起動時から自動的に実行するとより扱いやすくなると思いますが、ここでは省略します。

```python
import pymongo
from pymongo import MongoClient
import datetime
import paho.mqtt.client as mqtt

def main():
        mqttReceiver = MqttReceiver(DataWriter())
        mqttReceiver.startReceive()

class DataWriter:

        def __init__(self):
                client = MongoClient()
                self.db = client.can_data
                self.collection = self.db.edge_device

        def dataWrite(self,data):
                post = {"data": data,
                        "date": datetime.datetime.utcnow()}
                post_id = self.collection.insert_one(post).inserted_id
                print(post_id)

class MqttReceiver:

        def __init__(self,dataWriter):
                self.dataWriter = dataWriter
                self.client = mqtt.Client()
                self.client.on_connect = self.__on_connect
                self.client.on_disconnect = self. __on_disconnect
                self.client.on_message = self.__on_message
                self.client.connect("localhost",1883,60)

        def startReceive(self):
                self.client.loop_forever()

        def __on_connect(self,client, userdata, flag, rc):
                print("Connected with result code " + str(rc))
                client.subscribe("edge/1")

        def __on_disconnect(self,client, userdata, flag, rc):
                if  rc != 0:
                        print("Unexpected disconnection.")

        def __on_message(self,client, userdata, msg):
                self.dataWriter.dataWrite(str(msg.payload))
                print("Received message '" + str(msg.payload) + "' on topic '" + msg.topic + "' with QoS " + str(msg.qos))

if __name__ == "__main__":
        main()
```

## EDGE DEVICEの操作

[CAT M_UNIT+SORACOM_SIMでMQTTメッセージを送信してみる](https://mametarou963.com/posts/cat-m_unit+soracom_sim%E3%81%A7mqtt%E3%83%A1%E3%83%83%E3%82%BB%E3%83%BC%E3%82%B8%E3%82%92%E9%80%81%E4%BF%A1%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B/)を参考にEDGE DEVICE(PC)を構成します。


通信の接続を行います
```
AT+CGDCONT=1,"IP","soracom.io"
OK
AT+CNCFG=0,1,"soracom.io"
OK
AT+CNACT=0,1
OK
```

MQTTの接続とデータの送信を行います。
ここではtopic:edge/1 payload:testを送信します。
```
AT+SMCONF="CLIENTID","id"
OK
AT+SMCONF="URL","<hostname or ipaddress>","1883"
OK
AT+SMCONN
OK
AT+SMPUB="edge/1",4,1,1
> test
OK
```


## 確認方法

ここではデータベースに正しく書けているか確認します。
直接MongoDBを確認します


```
> mongo // 操作モード
> use can_data // DB選択
> db.edge_device.find() // データ一覧表示
{ "_id" : ObjectId("61a76c1f670af3fe3e061d35"), "data" : "b'test'", "date" : ISODate("2021-12-01T12:35:43.084Z")
...
```

送信した分のデータが表示されていれば成功です