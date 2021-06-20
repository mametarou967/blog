---
title: "<調査> M5StickCのRTOSのチュートリアルを試す"
date: 2021-06-20T17:39:00+09:00
description: M5StickCのRTOSのチュートリアルを試す
draft: false
hideToc: true
enableToc: true
enableTocContent: false
tags:
- M5StickC
- 調査
- RTOS
- AWS
image: images/postIcon/chousa1.png
---

## 背景

M5StickCはしばらく触っていたのですが、AWS 認定デバイスに選ばれたということで、試してみました。
以下、チュートリアルを実施する際にはまったところを記録します。

## 準備物

- M5StickC
- USB-TypeA - USB-TypeC

## 実施内容

- [AmazonFreeRTOSワークショップ](https://teuteuguy.github.io/amazon-freertos-workshop-docs/labs/)
Lab2まで

## はまったところ

* ラボ0からやり始めてはまってしまいましたがよく見ると「Setup Guide」が存在していたこと
    * おとなしくそちらから実施

* 「Setup Guide」内のAmazon Linuxの選択肢があったこと
    * Amazon Linux2推奨だったのでそちらを選択

* 「Setup Guide」内の「pip install argparse cryptography serial pyserial pyparsing==2.0.3 cmake ninja」は誤記?
    * 「sudo pip install argparse cryptography serial pyserial pyparsing==2.0.3 cmake ninja --ignore-installed PyYAML」で無理やりインストールする 

* 「Lab0」の「esptool --chip esp32 --port [YOUR COMX] ...」の[YOUR COMX]に数値を指定してもうまく実施できない
    * 「COMX」のようにCOMも含めて入力する

上記のはまりを経て、無事M5StickCに書き込むことができました

## その他

* [Getting Started with the M5StickcC and FreeRTOS](https://docs.m5stack.com/en/quick_start/m5stickc/m5stickc_quick_start_with_AWS-FreeRTOS_Windows)
* [ESP-IDF Programming Guide](https://docs.espressif.com/projects/esp-idf/en/release-v3.0/index.html#)


## 参考

* [Getting started with the Espressif ESP32-DevKitC and the ESP-WROVER-KIT](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html)
* [AWS M5StickC](https://devices.amazonaws.com/detail/a3G0h0000077kOjEAI/M5StickC)
* [AWS FreeRTOS](https://aws.amazon.com/jp/freertos/)
* [Amazon FreeRTOS Workshop](https://teuteuguy.github.io/amazon-freertos-workshop-docs/)
* [Amazon FreeRTOS M5StickC Workshop](https://teuteuguy.github.io/afmw-docs/) ※現在は上のリンクに統合されているようです
* [自分だけ開ける「スマート宝箱」を作って、大事なモノをセキュアに保管してみた](https://aws.amazon.com/jp/builders-flash/202101/smart-treasure-box/)