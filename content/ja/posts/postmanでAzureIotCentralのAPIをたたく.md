---
title: "PostmanでAzureIotCentralのAPIをたたく"
date: 2021-10-06T23:01:48+09:00
draft: false
tags:
- GR-ROSE
- 調査
- Azure
image: images/postIcon/chousa1.png
---


## TELEMETRYの値を読みだす場合

POSTMANで以下のように設定してSendするとテレメトリの値が読み取れます。
```
method:GET
URI:https://{your app subdomain}}.azureiotcentral.com/api/preview/devices/{Device ID}/telemetry/{telemetry name}
Headers
├Key::Authorization
│Value:{api token}
└Key:Content-Type
 Value:application/json
```

変数の意味は以下の通りです。
* your app subdomain:Azure IoT CentralのMy appsの名前
* Device ID:「デバイス>デバイスID」(※「デバイス>接続」から文字列のコピーが可能)
* telemetry name:「デバイステンプレート>該当のデバイステンプレート>該当の名前」
* api token:「管理>APIトークン>+新規」（通常SharedAccessSignatureで始まる文字列）


例えば以下の場合、POSTMANの画面を示します。
* your app subdomain:iot-gr-rose-application
* Device ID:2p02xiojzry
* telemetry name:Humidity
* api token:SharedAccessSignature sr=cad...


{{< figure src="/images/post/PostmanでAzureIotCentralのAPIをたたく/telemetry_postman.png"  >}}


## COMMANDを送信する場合


POSTMANで以下のように設定してSendするとコマンドが送信できます。
```
method:POST
URI:https://{your app subdomain}}.azureiotcentral.com/api/preview/devices/{Device ID}/commands/{command name}
Headers
├Key:Authorization
│Value:{api token}
├Key:Content-Type
│Value:application/json
└Body:raw:JSON
```


例えば以下の場合、POSTMANの画面を示します。
※デバイスがつながっていない状態で試しているためCould not connect to deviceが返ります
* your app subdomain:iot-gr-rose-application
* Device ID:2p02xiojzry
* telemetry name:Humidity
* api token:SharedAccessSignature sr=cad...
* Body:{ "value" : 1}

{{< figure src="/images/post/PostmanでAzureIotCentralのAPIをたたく/command_postman.png"  >}}

## web画面のボタンで送信する場合

調査中...

## 参考

* [JavaScriptのFetch API について](https://qiita.com/sotasato/items/31be24d6776f3232c0c0)