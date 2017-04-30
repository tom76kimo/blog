---
title: 改造 logdown 版面
tags: []
date: 2016-06-16 13:47:42
---

其實我一直覺得 logdown 的版面沒那麼對我的胃口，所以我小小的修改了一下，讓我使用的時候看起來更舒服。

#### 原本的版面

<figure>![](http://user-image.logdown.io/user/6035/blog/6047/post/737444/gSs94Yd0Si2NnB67FXAn_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202016-06-15%20%E4%B8%8B%E5%8D%886.10.01.png)</figure>

#### 改動後的版面

<figure>![](http://user-image.logdown.io/user/6035/blog/6047/post/737444/YpcIR7BxQ2qzlgOIP9UX_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202016-06-15%20%E4%B8%8B%E5%8D%886.10.32.png)</figure>

這樣有比較好嗎？應該有吧XD

如果你也想達到這樣的效果：

在 dashboard 按-&gt;blog設定-&gt;佈景主題-&gt;選擇目前的佈景主題來修改

在 <heade> 裡面加上</heade>

<!--more-->

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>&lt;style&gt;
      .html {
        font-size: 20px;
      }
      body {
        background: white;
      }
      .title {
        font-size: 1.5rem;
      }
      .content .container {
        background: white;
          border-radius: initial;
          box-shadow: initial;
      }
      .content .container::before {
        width: 0;
        height: 0;
      }
      .content .container::after {
        width: 0;
        height: 0;
      }
      figure.code pre {
        background-color: #F7F7F7;
      }
      .pagination &gt; ul &gt; li &gt; a:hover {
        background: #4d4d4d;
        color: white;
      }
      .pagination &gt; ul &gt; li.active &gt; a {
        color: #f9f9f9;
      }
      .content .post .article {
        font-size: 1.1rem;
      }
      code { border-radius: 4px; }
&lt;/style&gt;
</pre></div>
</figure>

就可以囉！