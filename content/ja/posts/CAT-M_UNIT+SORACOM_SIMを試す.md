---
title: "CAT-M UNIT+SORACOM SIMを試す"
date: 2021-11-13T12:53:36+09:00
draft: false
tags:
- 調査
- CAT-M
- SORACOM
description: CAT-Mの通信を試します。
---

## 概要

CAT-M UNITにSORACOM SIMを挿してpingより通信確認を目指します。

### ハードウェア構成

* [CAT-M UNIT](https://shop.m5stack.com/products/sim7080g-cat-m-nb-iot-unit?variant=40493802160300)
* [SORACOM SIM plan-D](https://soracom.jp/store/13217/)
* [FT234X 超小型USBシリアル変換モジュール](https://akizukidenshi.com/catalog/g/gM-08461/)

{{< figure src="/images/post/CAT-M_UNIT+SORACOM_SIMを試す/構成1.JPG" class="center" >}}
{{< figure src="/images/post/CAT-M_UNIT+SORACOM_SIMを試す/構成2.JPG" class="center" >}}

## SORACOM　SIMのAPNまわりについて
APN設定はＳORACOM公式により以下の通りです。

* APN
    * plan-D / plan-K / plan-KM1 / plan01s / plan01s-LDV の場合 : soracom.io
* plan-DU の場合 : du.soracom.io
* ユーザー名: sora
* パスワード: sora
* PDP Type: IP
* 認証タイプ: CHAP または PAP

## ATコマンドでの設定とpingの確認

tera termにて以下のコマンドを打っていき、ping応答確認を目指します。

* AT+CGDCONT=1,"IP","soracom.io"
* AT+CNCFG=0,1,"soracom.io"
* AT+CNACT=0,1
* AT+SNPING4="google.com",3,16,1000

以下実行した様子です。

```
AT+CGDCONT=1,"IP","soracom.io"
OK
AT+CGDCONT=1,"IP","soracom.io"
OK
AT+CNCFG=0,1,"soracom.io"
OK
AT+CNACT=0,1
OK

+APP PDP: 0,ACTIVE
AT+SNPING4="google.com",3,16,1000
+SNPING4: 1,216.58.220.110,85
+SNPING4: 2,216.58.220.110,120
+SNPING4: 3,216.58.220.110,121

OK
```
googleからpingの応答を確認できました。

## 参考

* [SIM7080G CAT-M/NB-IoT Unit 公式](https://docs.m5stack.com/en/unit/cat_m)
* [AT Command Reference](https://m5stack.oss-cn-shenzhen.aliyuncs.com/resource/docs/datasheet/unit/sim7080g/en/SIM7070_SIM7080_SIM7090%20Series_AT%20Command%20Manual_V1.04.pdf)
* [SIMCOM社のSIM7080Gでさくらセキュアモバイルの通信テスト](https://next-step.asia/2020/12/simcom%E7%A4%BE%E3%81%AEsim7080g%E3%81%A7%E3%81%95%E3%81%8F%E3%82%89%E3%82%BB%E3%82%AD%E3%83%A5%E3%82%A2%E3%83%A2%E3%83%90%E3%82%A4%E3%83%AB%E3%81%AE%E9%80%9A%E4%BF%A1%E3%83%86%E3%82%B9%E3%83%88/)