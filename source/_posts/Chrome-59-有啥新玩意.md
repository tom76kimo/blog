---
title: Chrome 59 有啥新玩意?
date: 2017-05-22 22:42:23
tags: ['Chrome']
keywords: Chrome,59,new,stuff
---

Chrome 59 版發行在即，它究竟有什麼新玩意呢?

#### 1. CSS and JS code coverage

Chrome 新增了一個 Coverage 的 tab，能夠找出沒有用到(被執行)的 CSS 跟 JS 程式碼

<img src=https://developers.google.com/web/updates/images/2017/04/coverage.png style="max-width: 700px;" />
Image credit by [Google](developers.google.com)

對比你的網站載入的檔案大小，找出實際用到的程式碼究竟占了其中的多少。然後你可以斟酌減少實際上根本沒有用到的程式碼。

<img src=https://developers.google.com/web/updates/images/2017/04/coverage-breakdown.png style="max-width: 700px;" />
Image credit by [Google](developers.google.com)

點擊檔案可以看到詳細的程式碼執行涵蓋率，綠色的部分表示有被執行，紅色表示沒有被執行，而紅綠交雜的部份表示這一行程式碼只有部分被執行(e.g. 判斷式: `var b = (a > 0) ? a : 0`)

如何打開 Covarage tab?
 - 打開你的 Chrome dev tool，然後按下 **Cmd+Shift+P** (Mac) 或是 **Ctrl+Shift+P** (Windows, Linux)
 - 輸入 `Coverage`，然後選擇 `Show Coverage`


#### 2. Full-page screenshots

有些網頁很長很長，比如 Yahoo 的 [mobile site](https://tw.mobi.yahoo.com)，可以一直往下捲往下捲往下捲，現在 Chrome 讓你可以為這種網站製作一張超長的 screenshot。
可以看看影片介紹

{% iframe https://www.youtube.com/embed/r_6_9eFPhxI 560 315 %}

#### 3. Block requests

Chrome 可以模擬你的網站某個 request(script, stylesheet 或是其他資源) 被 block 的情況，藉此來看看你的網站會有什麼反應，來做出更好的使用者體驗。
<img src=https://developers.google.com/web/updates/images/2017/04/block-request-url.png style="max-width: 700px;" />
Image credit by [Google](developers.google.com)

#### 4. Step over async await

看一下下面這段程式碼

```js
function wait(ms) {
  return new Promise(r => setTimeout(r, ms)).then(() => "Yay");
}

// do some work in background.
setInterval(() => 42, 200);

async function test() {
  debugger;
  const hello = "world";
  const response = await fetch('index.html');
  const tmp = await wait(1000);
  console.log(tmp);
  return hello;
}

async function runTest() {
  let result = await test();
  console.log(result);
}
```

當你在 function **test** 裡面的 debugger 斷點之後開始 step over 每一行程式碼，你可能會一直被 setInterval 那行程式碼中斷。Chrome 59 讓 test() 裡面的程式碼不會再被中斷，而是可以讓你一行一行放心的 step over。(雖然筆者用 Chrome 58 測試也不會被中斷就是了...)

Reference:
[[1]](https://developers.google.com/web/updates/2017/04/devtools-release-notes)