---
title: 用 php 產生 png 圖片：近 x 個上線使用者
categories:
  - 程式
tags: [php, png]
date: 2020-05-08 13:55:10
---

這篇只是一個紀錄（或是心得）。  
以最近做的「近 x 個上線使用者」為例。

<!--more-->

![](http://ls197.000webhostapp.com/kater/lastSeen5.png)

### 前言
基本架構來自於以前寫的一個破專案 [php-png](https://github.com/LianSheng197/php-png)

### 產生圖片
**幾個關鍵函式**
* 建立圖片物件：ImageCreate()
* 指定背景顏色：ImageColorAllocate()
* 插入文字：ImageTTFText()
* 產生圖片：ImagePng()
* 銷毀圖片：ImageDestroy()

為了減少伺服器壓力，因此產生圖片時是一次產生，逐行處理
（沒有二次疊加圖片。雖然我明白那樣才是解決排版的最簡易解法）

基於逐行處理，因此在內文的排版時就必須計算關鍵內容的寬度，並填入對應的空格。  
這裡我採用的是直接計算有幾個非 ASCII 文字跟幾個 ASCII 文字，前者直接當作全形文字，給兩格寬度；後者則當半形文字，給一格寬度。  
（然而這只能應付 87% 的文字）  
（遇到編碼超過 0xFFFF 就會亂碼）  
（遇到字形檔不支援的字就會變方框，佔 2/3 格）  
（遇到 RLM 之類的零寬控制字元時就會大崩壞）

總之，在一大堆問題下，我選擇了無視ww

### 架構
從伺服器取得資料 > 用 GAS 做中間媒介 > 再由 php 產生圖片。

會選擇加上中間的 GAS 純粹是為了方便修改。  
因為修改 GAS 比修改產生圖片那邊還要容易。  
（如果不是在家的話，每次要修改都要從 host 把它複製下來，改完再推上去，很麻煩。）  
（而 GAS 只要登入帳號就能改了）

### 核心原始碼
**GAS**
```js
// code.gs
function doGet(e) {
if(e.parameter.lastSeen != undefined) {
    // API: 取得最近 X 個上線
    let limit = e.parameter.lastSeen;
    let rawData = UrlFetchApp.fetch("https://kater.me/api/users?sort=-lastSeenAt&page[limit]=" + limit, opt).getContentText();
    let users = JSON.parse(rawData);
    
    let data = { data: [] };
    let final = {};
    users.data.forEach(function(user) {
      let temp = {};
      temp.id = user.id;
      
      if(temp.id == FETCH_DATA_UID){
        temp.id = "0";
        temp.username = "";
        temp.time = (Date.parse(user.attributes.lastSeenAt) / 1000) - 292563 + 32;
        final = temp;
        return;
      } else {
        temp.username = user.attributes.username;
        if(user.attributes.lastSeenAt != undefined){
          temp.time = (Date.parse(user.attributes.lastSeenAt) / 1000) + 29;
        } else {
          temp.time = "X";
        }
      }
      
      data.data.push(temp);
    });
    
    data.data.push(final);
    return ContentService.createTextOutput(JSON.stringify(data));
  }
}
```

**PHP (2020.05.08)**

```php
// lastSeen.php
$dt = new DateTime();
$dateTime = $dt->format('Y-m-d H:i:s');
$now = time();

$dnt_open = getDNT();
$ref = getRef();

if (isset($_GET["limit"])) {
    $limit = $_GET["limit"];
} else {
    $limit = 20;
}

if ($limit > 50) {
    $limit = 50;
}

if ($limit == 0) {
    $limit = 1;
}

$text = "最近 $limit 個使用者上線記錄 |圖片更新時間：$dateTime ||";

$url = "https://script.google.com/macros/s/AKfycbxejz2Zqgt1VyWM-0jWXdi8fIj9Nff4fLEjSj8FDS-CBG9Bu5bE/exec?lastSeen=$limit";
$data = file_get_contents($url);
$users = json_decode($data);

$text .= " UID \| 暱稱 " . str_repeat(" ", 32) . "\| 時間 |";
$text .= str_repeat("-", 60) . "|";

foreach ($users->data as $user) {
    $id = $user->id;
    $name = $user->username;
    if ($user->time != "X") {
        $temp = $user->time;
        $timeStr = "";
        $temp = $now - $temp;

        if ($temp > 60) {
            $min = intdiv($temp, 60);
            $timeStr .= "{$min} 分 ";
            $temp = $temp % 60;
        }

        $timeStr .= "{$temp} 秒前";

        $time = 0;
    } else {
        $timeStr = "X";
    }

    if ($id > 1000) {
        $space = " ";
    } elseif ($id > 100) {
        $space = "  ";
    } elseif ($id > 10) {
        $space = "   ";
    } else {
        $space = "    ";
    }

    $len = strlen($name);
    $mblen = mb_strlen($name);
    $full = ($len - $mblen) / 2;
    $half = $mblen - $full;

    $width = 2 * $full + $half;
    $repeat = 36 - $width;
    if ($repeat < 0) {
        $repeat = 0;
    }

    @$space2 = str_repeat(" ", $repeat + 1);

    $text .= "{$id}{$space}\| {$name}{$space2}\| {$timeStr}|";
}
```

**.htaccess**

```apache
RewriteEngine On
RewriteBase /kater/

RewriteRule ^lastSeen(\d+)\.png$ lastSeen.php?limit=$1 [L]
```