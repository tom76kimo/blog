---
title: 解決在 FireFox 上 Backbone.js 的 navigate() 被 trigger 兩次的問題
tags: []
date: 2014-06-16 13:51:22
---

## 問題

當我使用 navigate() 時，發現有的 routes 會被執行兩次，找了好久找不出原因。

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>router.navigate('show/' + place, {trigger: true});
</pre></div>
</figure>

甚至連 source code 都 trace 了，發現在`this.fragment = fragment;`這行之後就會出錯。

## 解法

原來是這樣，我的 place 這個變數有可能會是中文，必須要加上 encodeURIComponent()，navigate() 才會正常執行。

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>router.navigate('show/' + encodeURIComponent(place), {trigger: true});
</pre></div>
</figure>

這個問題只有在 firefox 上會發生，推斷大概是`window.location.hash`實作的方式不同所致。
