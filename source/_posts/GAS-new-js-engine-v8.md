---
title: Google Apps Script 提供新的 JavaScript 解析引擎 (V8)！
categories:
  - 程式
tags: [GAS, js]
date: 2020-02-13 06:21:14
---

<img src="https://michigan.it.umich.edu/news/wp-content/uploads/2019/02/google-apps-script-1.png" height="200px">

<!--more-->



***

**原本使用：**[Rhino](https://zh.wikipedia.org/wiki/Rhino_(JavaScript%E5%BC%95%E6%93%8E))
![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Inicio_de_ldp_para_260px50px_moziyarinocrnt.jpg/220px-Inicio_de_ldp_para_260px50px_moziyarinocrnt.jpg)

**全新：**[V8](https://zh.wikipedia.org/wiki/V8_(JavaScript%E5%BC%95%E6%93%8E))
![](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3f/V8_JavaScript_engine_logo_2.svg/100px-V8_JavaScript_engine_logo_2.svg.png)

***

### 新增功能？
現代 ECMAScript 語法。（如 `let`, `const`, `() => {}` 等等，但仍未支援[ ES6 的樣板語法](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Template_literals)）

參考：[Modern ECMAScript syntax](https://developers.google.com/apps-script/guides/v8-runtime#modern_ecmascript_syntax)

### 不相容或棄用語法？
極少。（熟悉現代語法的話，基本上是無痛轉移）

參考：[Incompatibilities](https://developers.google.com/apps-script/guides/v8-runtime/migration#incompatibilities)

### 如何切換？
目前由使用者自行更換（隨時可以修改）：
![](https://i.imgur.com/yNfu88g.png)

（切換後需要重新發佈）