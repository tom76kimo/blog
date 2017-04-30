---
title: Javascript 的 hoisting 問題
tags: []
date: 2014-03-03 14:07:56
---

先讓我們來看看 Javascript 的 scope ，和 C 有什麼不一樣。

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>#include &lt;stdio.h&gt;
int main() {
    int x = 1;
    printf("%d, ", x);
    if (true) {
        int x = 2;
        printf("%d, ", x);
    }
    printf("%d, ", x);
}
</pre></div>
</figure>

上面這段程式的執行結果會是 1, 2, 1

而來看看相同架構但不同語言的 Javascript 呢

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>var x = 1;
console.log(x); // 1
if (true) {
    var x = 2;
      console.log(x); // 2
}
console.log(x); // 2
</pre></div>
</figure>

上面這段程式的執行結果會是 1, 2, 2，WOW~怎麼會這樣，理由是因為 C 語言的if條件的括號{}就會形成一個 scope ，而 Javascript 不會， Javascript 只有 function 會形成 scope ，所以if裡面的`var x=2;`把本來的`var x=1;`給蓋過去了。

了解了 scope 的差異後，來看看以下的程式碼：

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>var foo = 1;
function bar() {
    if (!foo) {
          var foo = 10;
    }
    alert(foo);
}
bar();
</pre></div>
</figure>

你覺得 alert() 出來答案會是多少呢？ 答案是10，什麼？你不信？

可以自己試試看 [http://jsfiddle.net/n958k/](http://jsfiddle.net/n958k/)

奇怪？就我們對 Javascript 的 closure 的了解，`bar()`裡面的foo不是應該會讀到外面的1嗎？這樣，if判斷式不可能會執行啊， foo 應該沒有機會被改成10才對。

不說你不知，說了你就.....覺得這沒什麼XD，原來是 hoisting 的緣故 ( hoisting 我不知中文怎麼翻比較好，各位可以去 Google 就可以知道大概的意思) ，當執行上面那串程式碼，Javascript 的直譯器會先把變數宣告提到一個 scope 裡的最頂端，也就是說上面的程式碼經過整理會變成：

<figure class="figure-code code"><figcaption><span>
</span></figcaption><div class="highlight"><pre>var foo = 1;
function bar() {
    var foo;
    if (!foo) {
          foo = 10;
    }
    alert(foo);
}
bar();
</pre></div>
</figure>

這樣一來！你應該就可以理解，為什麼`alert()`出來的值會是10了吧。應該if判斷式在判斷的時候 foo 的值還是`undefined`，所以囉，在寫 Javascript 時要注意這方面的問題，切之，慎之。

reference:

[1] [JavaScript Scoping and Hoisting](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html)

[2] [JavaScript Hoisting Explained](http://code.tutsplus.com/tutorials/javascript-hoisting-explained--net-15092)
