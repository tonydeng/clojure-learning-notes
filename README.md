# Clojure学习笔记

![Clojure Logo](http://xahlee.info/UnixResource_dir/gki/lambda/Clojure-logo.png)

## 为什么要学习Clojure

我写Java也有10多年了，其间用过Python、PHP、Ruby、Groovy、JS等动态语言。Java相对C、C++来说有明显的优势，可以说是一种更高级的语言，高级语言带来的优势是能用更少的代码写出同样的功能，代码更接近与人的表达。

Java依然是现在业界最流行的开发语言，但这并不意味着Java能够一直的辉煌下去。现在各种更高级的动态语言如雨后春笋，百花齐放，虽然目前还没有完全超越Java的地位，但是终究有一天会走向巅峰（毕竟，对于现在的业界来说，工程师的开发时间成本远远高于服务器的成本）。

而且这些动态语言的理念和特性也是值得Java开发者学习的，从更高级的语言学习到的东西可以反过来更好帮助自己写好Java代码，这也是学习一门新语言的初衷。

我最近选择[Clojure](http://clojure.org/)作为自己要学习的新语言，原因如下：

1. Clojure是[Lisp](http://en.wikipedia.org/wiki/Lisp_(programming_language))的一种方言版本，继承了Lisp的绝大多数特性，而Lisp是IT界大牛[Paul Graham](http://paulgraham.com/)的名著《[黑客与画家](http://book.douban.com/subject/6021440/)》中极力推荐的。
1. 这门古老的语言之一也诞生了50多年了，但它的先进性依然是其他语言不可比拟的，大多数的高级语言都或多或少的借鉴了Lisp的先进理念。Paul Graham在《[What Make Lisp Different](http://paulgraham.com/diff.html)》中有详细的说明。
1. Clojure可以运行在JVM上，可以方便的调用Java类库，不用担心之前在Java上积累的经验全无用武之地，每个人从内心来说都是害怕改变的，平滑的过渡不失为一个好办法
1. Twwitter非常著名的实时计算框架Strom采用的就是Clojure，说明它在高性能并发上具有特别的优势

## 参考

1. [Clojure的主要特性](http://clojure.org/features)
1. [为什么Lisp语言如此先进](http://www.ruanyifeng.com/blog/2010/10/why_lisp_is_superior.html)