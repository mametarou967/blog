---
title: "CAT M_UNIT+SORACOM_SIMでMQTTメッセージを送信してみる"
date: 2021-11-23T23:05:56+09:00
draft: false
tags:
- 調査
- CAT-M
- SORACOM
- MQTT
description: CAT-M UNITを使ってMQTTメッセージを送信し、サーバーでメッセージが受信できることを確認します。
---

## 概要

CAT-M UNITを使ってMQTTメッセージを送信し、サーバーでメッセージが受信できることを確認します。

## ハードウェア構成

{{< figure src="/images/post/CAT-M_UNIT+SORACOM_SIMを試す/構成1.JPG" class="center" >}}

[CAT-M UNIT+SORACOM SIMを試す](https://mametarou963.com/posts/cat-m_unit+soracom_sim%E3%82%92%E8%A9%A6%E3%81%99/)の記事と同じ構成になっているものとします。
また、MQTTブローカーおよびMQTTサブスクライバーの設定を完了しtopic名"test"で待ち受けているものとします。詳しくは[さくらのVPS-CentOS8でMQTTBrokerを立てて、ラズパイからメッセージを送るまでを試す](https://mametarou963.com/posts/%E3%81%95%E3%81%8F%E3%82%89%E3%81%AEvps-centos8%E3%81%A7mqttbroker%E3%82%92%E7%AB%8B%E3%81%A6%E3%81%A6%E3%83%A9%E3%82%BA%E3%83%91%E3%82%A4%E3%81%8B%E3%82%89%E3%83%A1%E3%83%83%E3%82%BB%E3%83%BC%E3%82%B8%E3%82%92%E9%80%81%E3%82%8B%E3%81%BE%E3%81%A7%E3%82%92%E8%A9%A6%E3%81%99/)を参考にしてください。

## ATコマンドで通信設定

```
AT+CGDCONT=1,"IP","soracom.io"
OK
AT+CNCFG=0,1,"soracom.io"
OK
AT+CNACT=0,1
OK
```

## ATコマンドでMQTTの設定->接続->メッセージ送信

* CLIENTIDは適当です
* MQTTはセキュアになっておらず、最低限の設定しかしていません
* <hostname or ipaddress>は公開されているMQTTブローカーのドメイン名またはipアドレスを指定してください

```
AT+SMCONF="CLIENTID","id"
OK
AT+SMCONF="URL","<hostname or ipaddress>","1883"
OK
AT+SMCONN
OK
AT+SMPUB="test",4,1,1
> hoge
OK
```

## 結果

サーバー側のコンソールが以下のようになっていればCAT-M UNITからのMQTTメッセージが受信できています

```
[user@server]$ mosquitto_sub -h localhost -t test
hoge
```

