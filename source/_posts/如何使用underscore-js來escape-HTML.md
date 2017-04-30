---
title: 如何使用underscore.js來escape HTML
tags: []
date: 2016-03-01 01:22:43
---

在使用Backbone的時候，若使用underscore來compile你的html檔

為了預防XSS攻擊，我們應該要將輸出的字串做escaping，尤其是這段字串是自由讓某個使用者輸入的情形下，

更應該要escape。

使用underscore 的 _.template() function時，做escaping應該這樣寫

<figure class="figure-code code"><div class="highlight"><pre>&lt;%- variable %&gt;
</pre></div>
</figure>

不escaping的情形:

<figure class="figure-code code"><div class="highlight"><pre>&lt;%= variable %&gt;
</pre></div>
</figure>

實在簡單