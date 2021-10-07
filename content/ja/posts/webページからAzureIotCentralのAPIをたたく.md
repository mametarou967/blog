---
title: "webページからAzureIotCentralのAPIをたたく"
date: 2021-10-07T08:30:48+09:00
draft: false
tags:
- 調査
- Azure
image: images/postIcon/chousa1.png
---

## コード


* index.html
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Azure IoT Central</title>
    <link rel="stylesheet" href="index.css">
  </head>
  <body>
    <p class="title">Azure IoT Central</p>
    <div id="area1">
      <button type="button" id="btn1">値取得</button>
      <br>
      <input type="text" id="tBox" class="font-main" />
    </div>
    <script type="text/javascript" src="https://code.jquery.com/jquery-3.4.1.min.js"></script>
    <script type="text/javascript" src="index.js"></script>
  </body>
</html>
```

* index.css
```css
.font-main {
  padding: 2px;
}

#btn1 {
  margin-bottom: 4px;
}
```

* index.js

※{}の中は適切なものに置き換えてください。
```javascript
$("#btn1").on("click",async function()  {
  response = (await fetch('https://{your app subdomain}.azureiotcentral.com/api/preview/devices/{Device ID}/telemetry/{telemetry name}',
  {
   method: 'GET',
   headers: {
     'Content-Type': 'application/json',
     'Authorization': {api token}
   }
  }
  ))
  data = await response.json()
  
  $("#tBox").val(data.value);
});
```

## 例

以下の内容の場合、javascriptのコードを示します。
* your app subdomain:iot-gr-rose-application
* Device ID:2p02xiojzry
* telemetry name:Humidity
* api token:SharedAccessSignature sr=cad...(※ここは適切なものを入れてください)

```javascript
$("#btn1").on("click",async function()  {
  response = (await fetch('https://iot-gr-rose-application.azureiotcentral.com/api/preview/devices/2p02xiojzry/telemetry/Humidity',
  {
   method: 'GET',
   headers: {
     'Content-Type': 'application/json',
     'Authorization': 'SharedAccessSignature sr=cad...'
   }
  }
  ))
  data = await response.json()
  
  $("#tBox").val(data.value);
});
```

無事表示されました


{{< figure src="/images/post/webページからAzureIotCentralのAPIをたたく/telemetry_web.png"  >}}

## 参考

* [JavaScriptのFetch API について](https://qiita.com/sotasato/items/31be24d6776f3232c0c0)