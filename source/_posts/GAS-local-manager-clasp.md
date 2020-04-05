title: 在本機管理 Google Apps Script -- 使用 clasp
categories:
  - 程式
tags:
  - js
  - GAS
  - clasp
date: 2020-03-08 15:54:00
---
### I. 什麼情況要用這個東西？
- 覺得原本的網頁編輯器不好用
- 想用習慣的環境開發
- 開發時沒做版控會死www

符合上述其一，那你就可以考慮用這東西。

<!--more-->

### II. 主要功能
- 離線編輯
- 建立、更新、查看、部署腳本

### III. 要求
- Node.js \^4.7.4  
  https://nodejs.org/en/download/

### 1. 安裝
```bash
npm install -g @google/clasp
```
**注意：**
可能會有缺少相依套件的問題，需自行補安裝。
![](https://i.imgur.com/zRq4cQM.png)
以上圖爲例：
```bash
npm install -g inquirer@latest
```

接著，確保一切路徑都沒問題後，就能全域使用 `clasp` 了

### 2. 登入
在做一切腳本編輯之前，當然要先登入你的帳號（不然怎麽同步？）
```bash
clasp login
```
![](https://i.imgur.com/ogau2Lo.png)

接著會直接新開一個網頁，是熟悉的登入界面
![](https://i.imgur.com/eVpVtvu.png)

那這樣就完成登入了。
註：預設的 credentials 存在 `~/.clasprc.json`。

### 3-1. 建立專案
```bash
clasp create [標題] 
# 備註：我目前還不確定這個標題具體是幹嘛的... 
# 因爲建立後，從網頁上看到的專案名是我的電腦帳號名...
```
每次建立時都會問你這個專案是給什麼東西用的：
![](https://i.imgur.com/FrD2dfL.png)

目前共有七種類型：

1. **standalone** - 獨立的。跟直接開 GAS 檔案沒兩樣。
2. **docs** - Google 文件 > 工具 > 指令碼編輯器
3. **sheets** -  Google 試算表 > 工具 > 指令碼編輯器
4. **slides** -  Google 簡報 > 工具 > 指令碼編輯器
5. **forms** - Google 表單 > 更多選項 > 指令碼編輯器
6. **webapp** - GAS > 發佈 > 部署爲網路應用程式
7. **api** - GAS > 發佈 > 以 API 可執行檔形式部署

然後可能會看到這個訊息（如果是第一次用 Google Apps Script API 的話，這是正常的）
![](https://i.imgur.com/hqhuEyF.png)

那必須自行前往這個網頁：https://script.google.com/home/usersettings
然後啓用 Google Apps Script API
![](https://i.imgur.com/QmR94Yzh.png)

接著再重新執行一次，便能建立成功。
![](https://i.imgur.com/jRwYz5d.png)

**注意：**
 ＊同一目錄只能擁有一個專案。
  （專案檔爲 `.clasp.json`）

＊clasp 提供以目錄控管個別檔案的功能，以下是官方文件說明的對應關係：
```
# On script.google.com:
├── tests/slides.gs
└── tests/sheets.gs

# Locally:
├── tests/
│   ├─ slides.gs
│   └─ sheets.gs
```

因此，礙於這個功能，衍生了一個限制 -- 如果在其子目錄下試圖再建立一個專案，則會告知 "Nested clasp projects are not supported."（不支援巢狀專案）

如果要管理多個專案則必須...
1. 採用同級目錄：
```
├── Prj01/
│   ├─ foo.gs
│   └─ bar.gs
└── Prj02/
    └─ hakunamatata.gs
```
2. 編輯 `.claspignore` 。以下方結構舉例：
```
└── Prj01/
    ├─ Prj02/
    ├─ foo.gs
    ├─ bar.gs
    └─ .claspignore
```
它只是一個文字檔，因此可以透過簡單的指令達成：
```bash
echo "Prj02" >> .claspignore
```
此時就可以在 `Prj02/ ` 內建立專案了。

### 3-2. 複製現有專案
如果想同步某一個專案的話，先取得指令碼 ID，然後執行：
```bash
# GAS > 檔案 > 專案屬性 > 指令碼 ID
# 或從網址取得 https://script.google.com/d/<指令碼 ID>/edit
clasp clone <指令碼 ID>
```
![](https://i.imgur.com/TCGgCCg.png)

### 3-3. （同步）下載專案
```bash
clasp pull
```

### 3-4. （同步）上傳專案
```bash
clasp push
```

### 4-1. 取得專案所有版本
```bash
clasp versions
```
這裡將會列出所有版本與對應描述。
從網頁上（GAS > 檔案 > 管理版本）能看到十分相似的資訊。

### 4-2. 建立新版本（類似 commit）
```bash
# 強烈建議版本描述不要留空！
# 有用過 GAS 的話，應該知道它的「版本」跟普通軟體開發的「版本」概念有些落差...
clasp version [版本描述]
```

### 4-3. 新增部署（類似 branch）
```bash
# 我完全不知道後面加 [version] [description] 跟沒加有什麼差別 Orz
# 大概是下圖的 Version 那個部分吧（不過我試時沒有成功就是@@）
clasp deploy [version] [description]
```
這裡所謂的新增部署等於從網頁操作（GAS > 發佈 > 從資訊清單部署 > Create）
![](https://i.imgur.com/IJhBohE.png)

### 4-4. 列出所有部署
```bash
clasp deployments
```
執行後會顯示這種格式的訊息：
```text
4 Deployments.
- <Deployment ID - 1> @HEAD 
- <Deployment ID - 2> @<版本號> - web app meta-version
- <Deployment ID - 3> @<版本號> 
- <Deployment ID - 4> @<版本號>
```

### 4-5. 刪除部署
```bash
# <deploymentId> 從步驟 4-4 取得
clasp undeploy <deploymentId>
```

### 5. 用原本的編輯器開啓該專案
```bash
clasp open
```

***

備註：  
* 本文僅列出基本用法，還有一堆進階或衍生的用法沒有列出。（太多啦= =）  
* 本文可能含有一些錯誤或描述不周。

***

參考：
- 官方文件：[Command Line Interface using clasp](https://developers.google.com/apps-script/guides/clasp)