---
title: "さくらのVPS-CentOS8でMQTTBrokerを立てて、ラズパイからメッセージを送るまでを試す"
date: 2021-11-23T17:09:04+09:00
draft: false
tags:
- 調査
- mosquitto
- さくらのVPS
- CentOS
- MQTT
description: CentOS8(さくらのVPS上)でmosquitto(MQTT)を試します。
---

## 背景

セキュリティはガバガバで、とにかくVPSにBrockerを起動し、raspberry pi側からmosquitto送信クライアントコマンドであるmosquitto_pubが通るところまでを確認します。
CAT-M UNITでMQTTの通信を確認する前に、整った環境で導通するか確認したかったので実施しています。

## 事前準備

独自ドメインを割り当てているものとします。私は、スタードメインでドメインを取得し[こちら](https://wada811.blogspot.com/2014/11/sakura-vps-domain-settings.html)を参考に設定しました。（※割り当てられていなくても問題ないと思われますが...）


## OSのインストール

| 項目 | 内容 |
|:---:|:---:|
|OSインストール形式|標準OS|
|インストールOS|CentOS8 x86_64|
|スタートアップスクリプト利用|利用しない|
|パケットフィルター設定|利用する|
|サーバーへのSSHキー登録|登録しない|

### パケットフィルター設定

mqttのポートを事前に開けておきます（利用しないを選択し、firewallなどで制御しても問題ないと思われます。)

* SSH:TCP 22
* カスタム:TCP 1883

## さくらのVPSの初期設定

[ネコでもわかる\! さくらのVPS講座 ～第二回 サーバーをさわってみよう～](https://knowledge.sakura.ad.jp/8218/?_gl=1*1g3eq4b*_gcl_aw*R0NMLjE2MzU2NTc3NDguQ2p3S0NBancydk9MQmhCUEVpd0FqRWVLOXRwYlNScDd4NGV3M2c4SG8yTTc0ZFYxdU5SaGdUbjNLY1V0QVMxX1kwZFlXNUVVaTdVQnR4b0NRYzBRQXZEX0J3RQ..&_ga=2.52038497.958675991.1637633108-928219423.1635598126&_gac=1.61556446.1635657749.CjwKCAjw2vOLBhBPEiwAjEeK9tpbSRp7x4ew3g8Ho2M74dV1uNRhgTn3KcUtAS1_Y0dYW5EUi7UBtxoCQc0QAvD_BwE)を参考に、OSアップデートと一般ユーザー作成までを完了させます。

## ユーザーにsudo権限を与える

[is not in the sudoers file.This incident will be reported.となった時の対応方法](https://qiita.com/ponsuke0531/items/2e30704be12f797e1c10)を参考に、作成したユーザーにsudo権限を与えます。

```
[user@os-x]su
Password:
[root@os-x]sudo usermod -aG wheel user
[root@os-x]su user
```

## mosquittoのインストール

[How To Install and Secure the Mosquitto MQTT Messaging Broker on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-the-mosquitto-mqtt-messaging-broker-on-centos-7)のstep1を参考にmosquittoをインストールします。

```
[user@os-x]sudo yum -y install epel-release
[user@os-x]sudo yum -y install mosquitto
[user@os-x]sudo systemctl start mosquitto
[user@os-x]sudo systemctl enable mosquitto
```

## mosquittoでのMQTTのサブクスライブ

以下のコマンドで待ち構えておく

```
[user@os-x]mosquitto_sub -h localhost -t test
```

## raspberry piでのmosquittoインストール

```
[user@raspi]sudo apt install mosquitto
[user@raspi]sudo apt install mosquitto-clients
```

## raspberry piでのMQTTのパブリッシュ

```
[user@raspi]mosquitto_pub -h iot-tech-lab.com -t test -m "hoge"
```

## 結果

結果としては以下のような感じになりました。

* raspberry pi 側

```
[user@raspi]mosquitto_pub -h iot-tech-lab.com -t test -m "hoge"
[user@raspi]mosquitto_pub -h iot-tech-lab.com -t test -m "hoge"
```

* さくらのVPS(cent os側)

```
[user@os-x]mosquitto_sub -h localhost -t test
hoge
hoge
```





