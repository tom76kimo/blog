---
title: Atomic CSS 介紹
tags: ['CSS', 'programming']
date: 2016-06-17 10:31:10
keywords: atomic,css
---

### 簡介

這篇文章想介紹一下 [Atomic CSS](http://acss.io/)，這是 Yahoo 開發的一個開源工具，讓使用者用一個比較不同的觀點來撰寫 CSS。

Atomic CSS 主要是想要解決目前在大型的 (large scale) 專案下，CSS 會有的問題：

1.  CSS class 命名使用的衝突問題
2.  stylesheet 過於肥大的問題

因為 CSS styles 作用在全域，所以 CSS 們有可能會彼此打架。尤其是一個大型 web app，你有可能會將它拆成許多的 modules，不同的 modules 會交給不同的 team 負責，你可能無法保證 class 的命名、使用不會有衝突。你可能會說，簡單啊，幫 class 加上 namespace 就可以了：

```css
#module1 .header {
    background: #ffffff;
}

#module2 .header {
    background: #000000;
}
```

但 CSS 的階層愈多，效能就愈不好。

另一個問題，不同的團隊開發的時候，可能會寫了不同的 class，但卻擁有一樣的效果 (style)，比如說：

```css
#module1 .highlight-text {
  color: white;
}

...

#module2 .white-text {
  color: white;
}
```

如果我們可以避免這樣的情況發生，一樣的 style，確保它在整個大型 app 中只會被定義一次，這樣，我們可以有效的重複使用 style。尤其是在大型的專案當中，可以大幅減少 stylesheet 的 size。

Atomic CSS 使用了一些方法來解決以上的問題。

### 來撇一眼

Atomic CSS 到底會長什麼樣子呢？

```html
<div class="D(ib) W(300px)">
    ...
</div>
```

根據你的 HTML file，Atomic CSS tool 會自動幫你產生下面的 CSS file

```css
.D\(ib\) {
  display: inline-block;
}

.W\(300px\) {
  width: 300px;
}
```

看出什麼端倪了嗎？ `D(ib)` 對應到 `display: inline-block`，`W(300px)` 對應到 `width: 300px;`，所以 div 就擁有了 `inline-block` 跟 `width: 300px` 兩個 style，這跟直接寫 style 進去 HTML 

```html
<div style="display:inline-block; width:300px;"></div>
```

，有 90% 像。

你會說，什麼 90% 像，那就是寫 style 啊，反而還多此一舉，先寫成 class，再把它放進 HTML element，何必呢？

一開始看的確會覺得很怪，但仔細想想，這樣寫有幾個好處：

### 1\. 將 CSS style 最小元件化，並將元件的重複使用 (re-use) 最大化。

拿 `display: inline-block` 來說，Atomic CSS 保證它只會被宣告一次，就是

```css
.D\(ib\) {
    display: inline-block;
  }
```

不會有另一個 class 被宣告，然後擁有同樣的屬性了，就是 `D(ib)` 而已。如果整個 app 有多個 element 需要 `width:300px` 的屬性，也保證就只會有 `W(300px)` 這個 class 被宣告而已，不會有別的了。由此可以確保你的 stylesheet 的size ，會是最小的。

但這樣一來，HTML 裡面，假設我有兩個 div，需要 `display:inline-block`的 style，我還是要寫兩次 `D(ib)` 啊，感覺只是把 stylesheet 的 size 轉嫁到 HTML file 裡面而已。不過字串 D(ib) 比 display:inline-block; 短，甚至可以說是短不少，當你需要寫 D(ib) 愈多次，你就省下愈多空間。在 Atomic CSS 裏，產生出來的 class name，都會比它對應的 style 要短。

### 2\. 產生出來的 Stylesheet，非常適合壓縮

Atomic CSS 產生出來的 stylesheet，會盡量照著 gzip 的壓縮方式，將適合壓縮、重複的字元擺在一起，讓 stylesheet 可以變得更小。

### 3\. HTML element 的 style 非常易懂

這一點就見仁見智，Atomic CSS 好處就是你從 HTML file 看到一個 element，你馬上就知道他擁有什麼 style，比如：

```html
<div class="D(ib) H(100px) P(10px)">
</div>
```

你就可以知道它擁有 style: `display:inline-block; height: 100px; padding: 10px;` （當然前提是你熟悉了 class name 的對照，但這個記很快的）。不過就失去了一般 CSS class 的 semantic 能力。

### 4\. 解決了 CSS class 衝突的問題

每一個 style 都有對應的 class name，不同 team 使用的 class name 也都會是一樣的，確保整個 app 不管切了再多 module，stylesheet 可以只要有一份就好。

## 結論

Atomic CSS 最主要的功能，就是把 stylesheet 變得很小 （畢竟需要的屬性都只會寫一次），尤其撰寫的 app 愈大，省下的容量更是可觀，代價就是你會寫出像是 style 的 class，但是這個代價是優點還是缺點，我想就因人而異了。

*   [Atomic CSS Website](http://acss.io/)
*   [Atomic CSS Github](https://github.com/acss-io/atomizer)

## Real World DEMO

```Javascript
var React = require('react');

var Body = React.createClass({
    render: function () {
        return (
            <div className="P(10px) M(20%) Mstart(10px) Bgc(yellow)">test</div>
        );
    }
});

module.exports = Body;
```


Atomic CSS tool 產生出來的 CSS file
```css
.Bgc\(yellow\) {
  background-color: yellow;
}
.M\(20\%\) {
  margin: 20%;
}
.Mstart\(10px\) {
  margin-left: 10px;
}
.T\(0\) {
  top: 0;
}
.P\(10px\) {
  padding: 10px;
}

```
