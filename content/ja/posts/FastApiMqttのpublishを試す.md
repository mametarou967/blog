---
title: "FastApi-MQTTのpublishを試す"
date: 2022-08-23T23:07:56+09:00
draft: false
tags:
- FastApi
- MQTT
- FastApi-MQTT
description: FastApi-MQTTのpublishを試します
---

## 概要

簡単なIoTシステムを組みたいときWebAPIをたたいて、MQTTのメッセージの送受信をしたいときがあります。
今回は、素早くWeb API環境を整えるＷebフレームワークに[FastAPI](https://fastapi.tiangolo.com/ja/)があり、これをトリガーとして、MQTT送受信する[FastApi-MQTT](https://sabuhish.github.io/fastapi-mqtt/)の使用感を確認しました。

## 準備

環境にあわせて、`FastApi-MQTT`をインストールします。
python3.8.6ではライブラリ依存により入らなかったのでpythonを3.10.6に引き上げてからインストールしました。




## 試したソース

`FastApi-MQTT`公式の[サンプル](https://sabuhish.github.io/fastapi-mqtt/example/)を試すだけなのですが、2022年9月4日現在、コードの入力ミスがあり、
そのままでは実行できません。コードを以下のように変更します。

```python
from fastapi_mqtt.fastmqtt import FastMQTT
from fastapi import FastAPI
from fastapi_mqtt.config import MQTTConfig

app = FastAPI()

mqtt_config = MQTTConfig()

fast_mqtt = FastMQTT(config=mqtt_config)

fast_mqtt.init_app(app)

@fast_mqtt.on_connect()
def connect(client, flags, rc, properties):
    fast_mqtt.client.subscribe("/mqtt") #subscribing mqtt topic
    print("Connected: ", client, flags, rc, properties)

@fast_mqtt.on_message()
async def message(client, topic, payload, qos, properties):
    print("Received message: ",topic, payload.decode(), qos, properties)
    return 0

@fast_mqtt.subscribe("my/mqtt/topic/#")
async def message_to_topic(client, topic, payload, qos, properties):
    print("Received message to specific topic: ", topic, payload.decode(), qos, properties)

@fast_mqtt.on_disconnect()
def disconnect(client, packet, exc=None):
    print("Disconnected")

@fast_mqtt.on_subscribe()
def subscribe(client, mid, qos, properties):
    print("subscribed", client, mid, qos, properties)


@app.get("/")
async def func():
    fast_mqtt.publish("/mqtt", "Hello from Fastapi") #publishing mqtt topic

    return {"result": True,"message":"Published" }
```

## 実行した内容

* `test`フォルダに上記のソースの入った`main.py`を配置します
* 以下のコマンドを実行しサーバーを立てます

```
python3 -m uvicorn main:app --port=8000 --host=0.0.0.0 --reload
```

あるいは

```
uvicorn main:app --port=8000 --host=0.0.0.0 --reload
```

* mqttのpublishが発行されることを確認するために以下のコマンドを実行します

```
mosquitto_sub -h localhost -t /mqtt
```

* クライアントPCのPOSTMANから以下のURIをGETのメソッドで発行します

```
http://YOUR_SERVER_URI:8000/
```

ちなみにGETメソッドのresponseは以下のような値が返ります

```
{
    "result": true,
    "message": "Published"
}
```

* mqttのメッセージが以下のように受信されていれば成功です

```
Hello from Fastapi
```

