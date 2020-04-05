---
title: "Userscript: 提高腳本開發效率 - 使用外部編輯器"
categories:
  - 程式
tags: [js, userscript, tampermonkey]
date: 2020-02-23 19:59:40
---

撰寫腳本時，若想提高生產效率，那麼有些步驟可以自動化。

<!--more-->

例如：

- **連結軟體原始碼代管服務平台，使用 webhook 自動更新腳本**
  ^ 這裡細節就不提了，直接上官方教學：https://greasyfork.org/zh-TW/users/webhook-info

- **使用外部編輯器**
    ^ 如果覺得原本的編輯器實在難用的話，這能有效擺脫原生附帶的編輯器。
    ^ 本文重點，詳見下方教學**（以 Chrome + Tampermonkey + Greasyfork 爲例）**

![](src)
***

### Step 1. 在原編輯器內新增一個腳本，檔頭參數加入這行
(Windows)
``` js
// @require      file://C:\path\to\your\userscript.user.js
```
(Mac, Linux)
``` js
// @require      file:///path/to/your/userscript.user.js
```

像這樣
![](https://i.imgur.com/CYYf3Za.png)

細節備註：
-  當然也可以直接在原腳本內加入 @require，不過鑑於更新方便，建議還是另存個腳本。
-  可省略大部分參數，但是必須要有 name（識別用）、include / match（頁面觸發用）、grant（若腳本有用到則不可少）、require（這不用多說了）
-  該新增腳本只需要有檔頭參數即可，在 ==UserScript== 外的所有 JavaScript 語法將被忽略。
-  存檔後，就可以把這個頁面關掉了。

### Step 2. 確保開啓允許存取本地檔案的權限
到 Tampermonkey 的附加元件設定，可透過右鍵選擇管理附加元件快速抵達該頁
![](https://i.imgur.com/SMdcSG6.png)

然後確保開啓這個功能
![](https://i.imgur.com/GIRgsfb.png)

### Step 3. 接著就能爽爽用你喜歡的編輯器了~
像這樣（這裡我是使用 vscode，vscode 真香）
![](https://i.imgur.com/lRexwZLh.png)

### 注意！！
- **Step 1** 新增的那列參數只能存在已安裝的腳本中，**它不能被發佈**。
若嘗試發佈的話，則會導致失敗。
像這樣... 
![](https://i.imgur.com/OHerZou.png)
^ 上圖爲 **Github webhook** recent deliveries 的 Response，若腳本正常更新，則紅框不該有東西  
^ 若遇到這種問題，則會造成 Repo 與 Greasyfork 的版本不一致。  
^ 必須修正後重新 commit 才能再次觸發 webhook  
^ ~~我才不會說我在這個問題上多摸了半個多小時才發現問題在哪裏~~

- 由於 **Step 1** 的參數不能上傳，因此在 Greasyfork 上的版本就不可能存在這個參數。
若一開始就在原腳本添加該參數的話，當更新腳本時就會導致這個參數被刪除。（畢竟腳本更新是整個覆寫）
這樣每次更新後就要回去開原生編輯器把參數加回去，非常麻煩。
這就是爲什麼在 Step 1 時，我建議直接新增一個腳本。

![](src)
***
### 參考：

- [How to update tampermonkey script to a local file programmatically?](https://stackoverflow.com/questions/49509874/how-to-update-tampermonkey-script-to-a-local-file-programmatically)
- [file URI scheme](https://en.wikipedia.org/wiki/File_URI_scheme#Examples)
