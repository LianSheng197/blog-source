---
title: 在虛擬終端機無法正常登入 -- 輸入密碼時自動換行導致無止境登入失敗
date: 2020-01-29 20:33:43
categories: [程式]
tags: [Linux, gpm]
---

### 環境：Ubuntu Server 16.04.6

### 大致狀況
輸入帳號後，在輸入密碼時系統會自動添加換行導致登入失敗；循環約五六次後，getty 更新畫面，等待使用者再次輸入帳號，然後再循環自動換行的詭異錯誤，永無止境。

<!--more-->

### 可能原因
安裝並啟用了 gpm 這個功能，而目前已知 Kernel Version 4.15.0-44, 4.15.0-45, 4.4.0-142 均與此不相容，造成意外錯誤。[[1]](https://forums.linuxmint.com/viewtopic.php?p=1591640&sid=61f374f059667a24c1d8a0a0d750d62b#p1591640), [[2]](https://bugs.launchpad.net/ubuntu/+source/gpm/+bug/1814261)

### 解決方法
1.  升級或降級 Kernel Version
2.  解除安裝 gpm 及相關套件

### 解決流程
由於常規登入已無法使用，因此必須在 GRUB 介面選擇進階開機並選擇含有 Recovery 字眼的選項開機，接著選用 root 操作，就能以 root 權限進入熟悉的終端機介面了。

![](https://i.imgur.com/Cug93zC.png)
![](https://i.imgur.com/HqICBle.png)
![](https://i.imgur.com/s8hD7Ik.png)

而這裡我採取的是**第 2 個**解決方法，選用原因純粹相對單純。

``` bash
apt remove gpm
apt autoremove
reboot
```

接著以常規開機便能登入了。

( 但是這裡我又遇到另一個問題了，登入後 tty 自動重設的怪問題，但是在文字閃過瞬間是有看到最後登入紀錄的，表示有登入成功，只是不明原因自動 exit shell 或是 restart getty，詳細是什麼情況還有待解決。)
( 如果成功解決的話就會有下篇筆記 )

### 參考文章
1. [Unable to login on virtual terminals: Entering username "auto-enters" blank passwords; gretty crashing - Linux Mint Forums](https://forums.linuxmint.com/viewtopic.php?t=287366)
2. [Bug #1814261 “gpm prevents loging to the console: spurious “Ente...” : Bugs : gpm package : Ubuntu](https://bugs.launchpad.net/ubuntu/+source/gpm/+bug/1814261)
