---
title: 圖片延遲載入的現代作法
date: 2019-03-23 15:08:26
tags: ['image lazy load', 'image', 'lazy']
keywords: ['image lazy load', 'image', 'lazy', '延遲載入', '圖片']
---

### 背景知識

為了改善網頁的 load performance，我們很自然地會想到圖片的延遲載入。其精神是避免網站在一開始就載入使用者根本不會看到 (not in viewport) 的圖片，圖片的載入會卡住後面的 network request，所以我們應該要避免載入不必要的圖片。

![圖片卡住後面的 network request](https://i.gyazo.com/2a0f557f763eb96336f39339f454d9fe.png)
_可以看到一整排的圖片，推遲了網站需要的 js，像是 jQuery, main.bundle.js_

傳統的作法是，我們會監聽網站的 scroll event，然後透過 [getBoundingClientRect](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect) 看看每一個圖片的 [DOMRect](https://developer.mozilla.org/en-US/docs/Web/API/DOMRect) 來判斷這個張圖片現在是不是在 viewport 裡面，如果圖片已進入或是即將進入 viewport，我們再去把 src 換上去，把真正的圖片拉回來，放上頁面。

判斷是否進入 viewport 的範例如下:

<script src="https://gist.github.com/davidtheclark/5515733.js"></script>

#### 但傳統作法有幾個缺點

1.  getBoundingClientRect 是所謂的 [force reflow/layout 的操作](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)，scroll event 會頻繁的觸發，每次觸發就會強迫瀏覽器 reflow/layout，這是非常[昂貴的操作](/blog/2017/06/11/前端效能調校/)，很可能會引起畫面的卡頓。就算是有對 scroll event 做 debounce，也還是太昂貴了。
    &nbsp;
2.  現代網站有很多類 native app，左右 swipe 的操作，通常這樣的效果是通過 css 的 `transform: translate` 來達到的，因為他沒有 scroll，所以不會觸發 scroll event，你也就無從得知什麼時候該檢查元素的 DOMRect 狀態。

### Intersection Observer

現在，我們可以利用 [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) 來做到 image 的 lazy load，同時它不會有上述的問題。

這個 API 可以讓你監控兩個元素間相交的狀態，基本的用法是這樣:
假設你有一個 element A，然後你想知道 element B 什麼時候會跟 element A 有相交，你可以這樣寫

```js
const options = {
    // 告訴 api 以哪個元素當作底，好來監控其他元素與此相交的狀態
    root: document.querySelector('#elementA'),

    // 這個 root 的元素是否有 margin，預設是 0px，給了的話當其他元素碰到 root 的 margin 就會觸發 callback
    rootMargin: '0px',

    // 值為 0 ~ 1 之間，代表其他元素與 root 相交了多少百分比會觸發 callback，1.0 代表 elementB 全部進入了 element A 的範圍會觸發
    threshold: 1.0,
};

// 產生 intersection oberserver 的 instance
const observer = new IntersectionObserver(callback, options);

// 定義 callback，每次相交會觸發一次
const callback = function(entries, observer) {
    // 相交囉！
    entries.forEach(entry => {
        // target element:
        //   entry.boundingClientRect
        //   entry.intersectionRatio
        //   entry.intersectionRect
        //   entry.isIntersecting
        //   entry.rootBounds
        //   entry.target
        //   entry.time
    });
};

// 監控 elementB
const target = document.querySelector('#elementB');
observer.observe(target);
```

如此一來，我們就可以監控兩個元素之間相交的狀態。當 elementB 進入 elementA 時，就會觸發 callback function

#### 回到 image lazy load

我們需要的是檢查 viewport 跟 image 之間的相交狀況，所以我們可以這樣寫。

```js
const options = {
    // 如果 root 不給值，或是給 null，root 就會是你的 viewport，超讚！
    root: null,

    // 我希望它即將出現在 viewport 之前就觸發 callback，這邊設定它往下滑動時進入 viewport 之前 100px 就觸發去拉圖片
    rootMargin: '0px 0px 100px 0px',

    threshold: 1.0,
};
```

> 這邊重點就在於 root 不給的話，預設就會是你的 viewport，以及使用 rootMargin 去設定傳統上所謂的 threshold。

```js
// 監控 image
const $imgList = document.querySelectorAll('img');

const callback = function(entries, observer) {
    entries.forEach(function($img) {
        if ($img.isisIntersecting) {
            // 假設圖片真正的 src 放在它的 data-src 裡
            $img.target.src = $img.target.dataset.src;

            // 已經換上真正的 src，不用再監控了
            observer.unobserve($img.target);
        }
    });
};

// 註冊監控所有的圖片
$imgList.forEach($img => observer.observe($img));
```

上面的程式碼在描述，當圖片即將(100px 的 threshold)與使用者的 viewport 相交時，它就會將圖片換上真正的 src，而這個真正的 src 之前是預先存在 data-src 裡。 如此一來就達到我們想要的 image lazy load。

#### 這樣的好處是

1. 你無須註冊任何的 scroll 或是 resize event，不需要一直頻繁的在你的 main thread 上看元素的 DOMRect，然後觸發一堆 reflow，而是直到元素有相交的時候才會觸發 callback，這對效能來說會增進很多。
    <div class="tip"> 但要注意的是，callback 還是跑在 main thread 上的，所以 callback 裡面做的事情要盡量少，或是放在 [Window.requestIdleCallback()](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestIdleCallback) 裡面。</div>
2. IntersectionObserver 也能夠偵測使用 css transform 改變大小或是位置的相交狀態，太棒了！

### IntersectionObserver 支援程度

目前主流的瀏覽器大多支持，但要注意 Safari 直到 12.2 才支持。 不支持的瀏覽器我們可以使用 W3C 開發的 [polyfill](https://github.com/w3c/IntersectionObserver/tree/master/polyfill)
![IntersectionObserver 支援程度](https://i.gyazo.com/354c1384b45cd10b1a330a889f88bd32.png)

React 方面也有許多現成的 library 可以參考：

-   https://github.com/thebuilder/react-intersection-observer
-   https://github.com/researchgate/react-intersection-observer
-   https://github.com/bluebill1049/react-simple-img 使用 IntersectionObserver 的 react image component

### Reference

-   [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
-   [IntersectionObserver’s Coming into View](https://developers.google.com/web/updates/2016/04/intersectionobserver)
-   [Lazy Loading Images and Video](https://developers.google.com/web/fundamentals/performance/lazy-loading-guidance/images-and-video/)
