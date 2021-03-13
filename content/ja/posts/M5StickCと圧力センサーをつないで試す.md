---
title: "<調査> M5StickCと圧力センサーをつないで試す"
date: 2021-03-13T10:27:14+09:00
draft: false
tags:
- M5StickC
- 調査
image: images/postIcon/chousa1.png
---

## 背景

Interface 2018年 11月号をみていると、Esp32に圧力センサーが手軽につながるということで試してみたくなりました。

## 準備するもの

* 圧力センサー(FSR-406)
* ジャンパーワイヤー x 3
* 抵抗 3k(今回は1k + 2k)
* M5StickC

## 接続図と接続した様子

{{< figure src="/images/post/M5StickCと圧力センサーをつないで試す/接続図.jpg" class="center" >}}
{{< figure src="/images/post/M5StickCと圧力センサーをつないで試す/接続図2.jpg" class="center" >}}

## コード

M5StickCに書き込むコードです。コードは[こちら](https://github.com/mmametarou967/ForceSensing)

## 動作する様子

{{< youtube IoDv-5ZEykc >}}

## 参考にしたページ

* [圧力センサーを使って、デスクワークでどれだけ寿命が縮んでるか測ってみた](https://qiita.com/s_fujii/items/7e3f27df05685ad15e0c)
