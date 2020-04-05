---
title: 在 Ubuntu 上安裝 Cisco Packet Tracer
categories:
  - 程式
tags: [Linux, Cisco, packet tracer]
date: 2019-10-12 22:37:23
---

註冊、等審核信、登入、找安裝包這些流程一概略過，因爲這都不是重點。
<!--more-->
不過在我下載軟體時發現目前 Cisco 有提供兩個 PacketTracer7 的版本。 (7.1.1, 7.2.2)

我不確定兩者具體差別在哪裡，不過如果選 7.1.1 (PacketTracer711_64bit_linux.tar) 就只提供文字界面安裝；而 7.2.2 (PacketTracer-7.2.2-ubuntu-setup.run) 則提供圖形化的極簡易安裝。

裝好後就可以用 packettracer 來開啓 Packet Tracer。

****

但是，一切就是這個但是！
事情可不是憨人想的這麼簡單！！

執行 packettracer 後你可能會看到終端機就跳這麼一行訊息：
![](https://truth.bahamut.com.tw/s01/201910/7ae8755eaaf80864bba234657879faa6.JPG)

Starting Packet Tracer 7.2.2

...  
.......  
.............????

爾後去問偉大的谷歌，才知道這不是個案。
原因是缺少套件。（OS：大哥你缺套件都不唉一聲的嗎？）

**可能缺少的套件 A： libicu52**
1. 下載該套件的安裝包（註：若遇到 404 Not Found，請自行到[該層目錄找對應版本](http://security.ubuntu.com/ubuntu/pool/main/i/icu/)，因爲可能有小升級）
   ```bash
   wget http://security.ubuntu.com/ubuntu/pool/main/i/icu/libicu52_52.1-3ubuntu0.8_amd64.deb
   ```
2. 安裝
   ```bash
   sudo dpkg -i libicu52_52.1-3ubuntu0.8_amd64.deb
   ```

3. 更新與驗證
   ```bash
   sudo updatedb; locate libicui18n
   ```

**可能缺少的套件 B： libpng12**

1. 下載安裝包
   ```bash
   wget http://security.ubuntu.com/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1.1_amd64.deb
   ```
   或者到[這個頁面](https://packages.ubuntu.com/xenial/amd64/libpng12-0/download)下載。

2. 安裝
   ```bash
   sudo dpkg -i libpng12-0_1.2.54-1ubuntu1.1_amd64.deb
   ```

以上兩項皆安裝完成後應該就能正常開啓了。
![](https://truth.bahamut.com.tw/s01/201910/619ad9a911d4283082c0e07c312db0ec.JPG)


***

安裝時相關環境與版本：  
* OS: Ubuntu 18.04.3 LTS
* Kernel: 4.15.0-65-generic
* DE: Cinnamon 3.6.7
* Packet Tracer: 7.2.2

參考文章：  
* [Cisco Packet Tracer 7.1 won't start](https://askubuntu.com/questions/963457/cisco-packet-tracer-7-1-wont-start)
* [Cisco Packettracer fails to start](https://askubuntu.com/questions/1052924/cisco-packettracer-fails-to-start)
