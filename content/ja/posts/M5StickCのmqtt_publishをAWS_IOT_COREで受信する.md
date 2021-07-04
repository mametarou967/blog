---
title: "<調査> M5StickCのmqtt_publishをAWS_IOT_COREで受信する"
date: 2021-07-04T17:39:00+09:00
description: M5StickCのmqtt_publishをAWS_IOT_COREで受信する
draft: false
hideToc: true
enableToc: true
enableTocContent: false
tags:
- M5StickC
- 調査
- AWS
image: images/postIcon/chousa1.png
---

## AWS側の設定

### エンドポイントの確認する

* 「AWS IoT Core　> 設定 > エンドポイント」のURIを控えておきます(AWS_IOT_ENDPOINTで使用予定)

### ポリシーを作成する

* 「AWS IoT Core　> 安全性 > ポリシー」を選択します
* 「作成」ボタンを押します
* 以下の項目を入力して、「作成」ボタンを押します
    * 名前:任意
    * アクション:iot:*
    * リソース ARN:*
    * 効果:許可

### モノと証明書を作成する

* 「AWS IoT Core　> 管理 > モノ」を選択します
* 「モノを作成」を押します
* 「１つのモノを作成」を選択し、「次へ」を押します
* 「モノの名前」の項目に任意の値を入れ、「次へ」を押します。なお、「モノの名前」は控えておきます(DEVICE_NAMEで使用予定)
* 「新しい証明書を自動生成」を選択し、「次へ」を押します。
* 前の項目で作成したポリシーを選択し、「モノを作成」を押します。
* 「証明書とキーをダウンロード」のページが出てくるので以下の内容をダウンロードし、「完了」を押します
    * デバイスの証明書
    * パブリックキーファイル
    * プライベートキーファイル
    * Amazon 信頼サービスエンドポイント（２種）

## デバイス(M5StickC)側の設定

* [M5StickcAwsPublish](https://github.com/mametarou967/M5StickcAwsPublish)をダウンロードします
* 定数定義を書き換えます
    * WIFI_SSID:Wi-FiのSSIDを入力します
    * WIFI_PASSPHREASE:Wi-Fiのパスワードを入力します
    * DEVICE_NAME:デバイスの名前を入力します
    * AWS_IOT_ENDPOINT:AWSのエンドポイントを入力します
    * root_ca:AmazonRootCA1.pem(AmazonRootCA3)の内容を入力します
        * "-----BEGIN CERTIFICATE-----"や"-----END CERTIFICATE-----"も含めて入力します
    * certificate:***.certificate.pem.crtの内容を入力します
        * "-----BEGIN CERTIFICATE-----"や"-----END CERTIFICATE-----"も含めて入力します
    * private_key:***.private.pem.keyの内容を入力します
        * "-----BEGIN RSA PRIVATE KEY-----"や"-----END RSA PRIVATE KEY-----"も含めて入力します
* ビルドします
* ターゲットデバイスに書き込みます

## 動作確認

* 「AWS IoT > テスト > MQTTテストクライアント > トピックをサブスクライブする」を選択します
* 「トピックのフィルター」に「myhome/chime」を入力します
* 「サブスクラブ」ボタンを押下します
* スケッチが書き込まれたM5StickCを起動します
* 一定時間経過すると"Hello"が表示されます。表示されれば動作確認完了です。

## 参考

* [ミーティング中の来客時に便利 ! AWS IoT でインターホンと警告灯を連動させてみた](https://aws.amazon.com/jp/builders-flash/202101/iot-patlite/?awsf.filter-name=*all)