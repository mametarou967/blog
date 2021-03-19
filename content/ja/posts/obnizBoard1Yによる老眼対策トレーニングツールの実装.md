---
title: "<書きかけ> ObnizBoard1Yによる老眼対策トレーニングツールの実装"
date: 2021-03-18T15:20:08+09:00
draft: true
---


## 背景

twitterを眺めていると、[obniz IoT コンテスト2021 in elchika](https://elchika.com/promotion/obniz2021/)を見つけました。
以下、書きかけ

## つくるもの

老眼対策トレーニングツール

## 仕様

* obnizを手元15cm程度に持ち、スマホを2m以上離した位置に設置
* スマホはスリープモードを解除しておく
* トレーニングを開始するとobniz側にCマークがでるため、穴の開いているほうを入力する
* 正解すると次にスマホ側にCマークが表示されるので、同じように入力する
* 上記を繰り返すことでピントを前後に動かし、老眼対策トレーニングが実施できる

## TODO

1. JoyStickの入力
1. ランダムでCの入力
1. JoyStickからの入力を受け付けて、Cと入力があっていれば次の乱数を表示する
1. Step2:obniz側の液晶にCを表示
* Step3:JoyStick入力で次の画面にいく

* 

## 画像置き場

* アプリケーションを動かす際に画像の置き場が必要だったためここに置く

{{< figure src="/images/post/obnizBoard1Yによる老眼対策トレーニングツールの実装/↑.jpg" >}}

<br>

{{< figure src="/images/post/obnizBoard1Yによる老眼対策トレーニングツールの実装/←.jpg"  >}}

<br>

{{< figure src="/images/post/obnizBoard1Yによる老眼対策トレーニングツールの実装/→.jpg"  >}}

<br>

{{< figure src="/images/post/obnizBoard1Yによる老眼対策トレーニングツールの実装/↓.jpg"  >}}