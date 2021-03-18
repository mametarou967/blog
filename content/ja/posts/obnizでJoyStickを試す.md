---
title: "<調査> obnizでJoyStickを試す"
date: 2021-03-18T19:29:15+09:00
draft: false
tags:
- obniz
- JOyStick
description: obnizでJoyStickの操作感を確認します
image: images/postIcon/chousa1.png
---

## 背景

obnizでJoyStickで上下左右の入力を取りたかったので試してみました。

## 準備物

* obniz (1Y)
* JoyStick

このようにつけます

{{< figure src="/images/post/obnizでJoyStickを試す/実装.jpeg" class="center" >}}

もてば、このような感じでコントローラーっぽくなります

{{< figure src="/images/post/obnizでJoyStickを試す/操作感.jpeg" class="center" >}}


## 実行手順

obnizは公式がかなり手厚いので基本的に[こちら](https://obniz.com/ja/sdk/parts/JoyStick/README.md)をそのまま実施すればできます

## コード

developer's console側で以下のコードを実行すると入力に上下左右の入力に応じて文字が変わります

```html
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <link
      rel="stylesheet"
      href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    />
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <script
      src="https://unpkg.com/obniz@3.13.0/obniz.js"
      crossorigin="anonymous"
    ></script>
  </head>
  <body>
    <strong id="xStatus">xStatus</strong> <br>
    <strong id="yStatus">yStatus</strong> <br>
    <script>
    var obniz = new Obniz("OBNIZ_ID_HERE");
    obniz.onconnect = async function () {
      var joystick = obniz.wired("JoyStick", {gnd:4, sw:0, y:1, x:2, vcc:3});
      joystick.onchangex = function(val){
        if(val > 0.9){
          $("#xStatus").text("Up/Down   :" + "DOWN")
        }else if(val < -0.9){
          $("#xStatus").text("Up/Down   :" + "UP")
        }else{
          $("#xStatus").text("Up/Down   :" + "None")
        }
      };

      joystick.onchangey = function(val){
        if(val > 0.9){
          $("#yStatus").text("Left/Right :" + "LEFT")
        }else if(val < -0.9){
          $("#yStatus").text("Left/Right :" + "RIGHT")
        }else{
          $("#yStatus").text("Left/Right :" + "None")
        }
      };
    }
    </script>
  </body>
</html>

```

## 動作する様子

{{< youtube 4OsfqId8IJg >}}
