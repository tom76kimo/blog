---
title: Fetch 跟 Webpack 的整合
tags: []
date: 2016-06-15 15:28:47
---

在專案裡面 `require('fetch')` ，再執行 webpack ，會噴出一堆 error 。

目前 fetch 在前端的支援還不足，解法可參考此篇文章

[Using WebPack with shims and polyfills](http://mts.io/2015/04/08/webpack-shims-polyfills/)

重點就是這一段：

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>plugins: [
  new webpack.ProvidePlugin({
    'fetch': 'imports?this=&gt;global!exports?global.fetch!whatwg-fetch'
  })
]
</pre></div>
</figure>

然後要記得先安裝

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>$ npm i imports-loader exports-loader --save
</pre></div>
</figure>

應該就可以正常被 webpack

如果以上方法幫不到你

可以直接使用 Github 版本的 fetch

[github/fetch](https://github.com/github/fetch)