---
title: JavaScript 的 Garbage Collection
tags: []
date: 2016-03-01 01:21:35
---

JavaScript 是個有實作 GC(Garbage Collection) 的程式語言，會自動釋放你再也用不到的物件所佔用的記憶體。

## Life Cycle

所有的程式語言記憶體的 Life Cycle 都差不多:

1.  配置需要的記憶體
2.  對記憶體做存取
3.  釋放記憶體

## GC演算法

JavaScript 的 GC 演算法把 **自動釋放再也用不到的物件所佔用的記憶體** 的這個問題，轉變成實作 **自動釋放完全沒有被參考的物件所佔用的記憶體**。 因為要預測一個物件是否再也不會被用到太困難了，所以實作GC的演算法換了個方式，來判斷需要釋放的物件。

比方說:

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>var a = {value: 1};
var b = a;
a = null;
b = null;
</pre></div>
</figure>

以上的程式碼的第一行說明有一個物件，他長這樣 `{value: 1}` ，然後被 a 跟 b 參考。當在第三行，把 a 設定參考到 null 時，因為還有 b 參考到這個物件，所以這個物件還不能被 collect ，但當到了第四行把 b 也設為 null 時，此時 `{value: 1}` 這個物件就完全沒有被參考到了，這時候這個物件就可以被 GC ，等待 browser 將它所佔用的記憶體釋放。

Mozilla 有一篇文章 Memory Management - JavaScript | MDN [[1]](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management) 說到，大家一般認為， JavaScript 是個有著 GC 的語言，所以不用特別去釋放記憶體，這種觀念是不對的。因為"自動釋放完全沒有被參考的物件所佔用的記憶體"的演算法，還是有其瑕疵，在某些[狀況下](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management#Limitation_.3A_cycles)有些被佔用的記憶體是無法準確被釋放的。 Mozilla 形容這種演算法是一種天真 (Naive) 的演算法。但在文章的文末也提到，現在 JavaScript 的 compiler 日新月異，現在使用的演算法也愈來愈聰明，此種演算法的限制也愈來愈少。

## GC pulse

在瀏覽器進行 GC ，釋放記憶體時，會產生 GC pulse ，你的瀏覽器有機會會短暫的被 block 住。因此，學習如何減少 GC pulse 也是很重要的，要減少 GC pulse 就要減少 GC ，減少 GC 就要減少記憶體的配置。

在平時寫程式的時候，就要注意減少配置新的記憶體區間，每次 assign 物件、變數的時候，都會配置新的記憶體。我們可以盡力的重複使用已配置的物件，來減少 GC 。(不過當然不要因為要重複使用物件就讓你的程式邏輯變得奇怪或是複雜，凡事都是過猶不及。)

### 減少配置新的記憶體的技巧

#### reset Array

有時候我們會需要把陣列的元素都清除，最簡單也最常見的方法就是

<figure class="figure-code code"><div class="highlight"><pre>var a = ['1', '2', '3'];
//....
a = [];  //reset array
</pre></div>
</figure>

將陣列設成 `[]` ，空陣列。

不過對於記憶體的使用來說，這種方式會配置一塊新的記憶體給a參考，然後原本 `['1', '2', '3']` 如果沒有人參考到它，它就進入 GC ，等著被釋放記憶體。最好 `reset` 陣列的方式應該是:

<figure class="figure-code code"><div class="highlight"><pre>a.length = 0;
</pre></div>
</figure>

藉由將陣列的長度設為0，既能達到 reset 的效果，又能避免 assign 一個新的陣列`[]`，而額外配置了記憶體。

#### setTimeout

使用 setTimeout 時也可以注意記憶體的配置

<figure class="figure-code code"><figcaption><span>不好的使用方式
</span></figcaption><div class="highlight"><pre>setTimeout(function(){
    //do something....
}, 500);
</pre></div>
</figure>

上面這段程式碼會每0.5秒配置一個新的記憶體區塊擺放那個匿名 function ，這樣會不斷的配置新的記憶體，不太好。

<figure class="figure-code code"><figcaption><span>好的使用方式
</span></figcaption><div class="highlight"><pre>function doSomething(){
  //do something...
}
setTimeout(doSomething, 500);
</pre></div>
</figure>

應該要用這樣參考到 doSomething 的方式來避免不斷配置新的記憶體

#### 謹慎使用 slice() 一類的函式

[slice()](http://www.w3schools.com/jsref/jsref_slice_string.asp)也是在陣列或字串處理中很常使用的函式，但要注意的是，此類函式會複製陣列/字串，產生新的陣列/字串回傳。每次使用都會配置新的記憶體，所以要謹慎使用。

#### References

[1] [Memory Management - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)

[2] [Writing Fast, Memory-Efficient JavaScript](http://coding.smashingmagazine.com/2012/11/05/writing-fast-memory-efficient-javascript/)