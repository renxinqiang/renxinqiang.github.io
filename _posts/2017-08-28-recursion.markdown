---
layout: post
title:  "递归加判断执行逻辑!"
date:   2015-04-18 08:43:59
author: Ren Xin Qiang
categories: Recursion
tags:	function 
cover:  "/assets/instacode.png"
---

### 代码展示
{% highlight javascript %}
<?php
    function p($i, $n){
        return ($i < $n && printf("%d\n", $i) && !p($i + 1, $n))
        || printf("%d\n", $i);
    }
    p(1,5);
?>
{% endhighlight %}

### 代码分析
这段代码要分析的话可以简化一下，分析p(1, 2)，首先 1 < 2 根据左值运算规则，会执行 第一个printf()，会输出1， 然后递归调用 p(2, 2), 递归中 2 < 2 不成立，执行第二个printf()， 会输出2, 由于printf执行后返回一个不为0的数字，所以递归p(2, 2) 的结果为真，取反后为假，所以第一次中的  i < N && printf() && !p(2, 2)为假，会执行 || printf(), 输出1
