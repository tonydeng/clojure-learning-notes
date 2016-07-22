# 你好，集合

> It is better to have 100 functions operate on one data structure than 10 functions on 10 data structures. --- Alan J. Perlis

> 100 个函数操作 1 种数据结构，比 10 个函数操作 10 种数据结构要好 --- Alan J. Perlis

## Clojure 提供的基本数据结构[ [1] ](#structure)<span id="fn_link_structure"></span>

* list
* vector
* map
* set

而上述 4 种数据结构我们都称之为集合（`collection`）

集合就是把一些东西放在一起，打个包裹，方便管理。

它们各有特色：

* list 是 Clojure 中最为简单的数据结构
* vector 和 list 相似，它支持高效的随机访问
* map 是一种 “键值对” （Key-Value），在以后的文章中会详细介绍这种数据结构
* set 是一种不能出现重复元素的集合

然而由于篇幅限制和本人水平有限，并不能详尽的说明每一种集合的每一种用法

## 以 `list` 和 `vector` 作简要介绍

更为详细的内容可以查阅相关 `API`[ [2] ](#api)<span id="fn_link_api"></span>> 文档

好吧，读完上面的东西你可能想说：什么玩意儿

没事儿，文字描述总是很枯燥，我们更倾向于使用代码来向你介绍，我们使用 `list` 函数来创建一个 `list` （列表）

```lisp
=> (list "hello" "list")
("hello" "list")
```

如你所见， `list` 函数的返回值就是一个 `list`

它的“样子”看起来很眼熟，没错，一个列表里的每一个内容称之为元素

而元素被小括号包围，这就构成了一个列表

此例中的这个 `list` 包含两个元素 --- 字符串`"hello"` 和字符串 `"list"`

你也许已经注意到了，`Clojure` 语言本身就使用这种数据结构来表示语言本身

使用自身的数据结构来表示自身，这种特征我们给它单独取一个名字，以表示高端，称之为 “同像性”[ [3] ](#identity)<span id="fn_link_identity"></span>，又称 “* 代码即数据 *”

>（如果你学习过编译相关的知识，你就能立刻发现这种做法的意义。Clojure 和所有的 Lisp 一样，直接使用语法树作为语言本身， 这也是宏（Macro）功能的基础）

## Clojure 还提供了一个语法糖[ [4] ](#sugar)<span id="fn_link_sugar"></span> --- 单引号 '

它的功能是阻止求值

它的非语法糖形式是 `quote`

我们知道，在 `Clojure` 中，位于括号的第一个位置的元素会被认为是一个函数

如果我们使用阻止求值 `quote` 或者它的语法糖 `'`，就可以阻止试图求值一切的 `Clojure` 的一次求值

`quote` 的返回值是你阻止求值的代码本身（* 同时也是数据 *）

```lisp
=> (quote ("hello" "list"))
("hello" "list")
```

```lisp
=> '("hello" "list")
("hello" "list")
```

如果不进行阻止求值，那么由于 `“hello”` 在括号的第一个位置，会被认为是一个函数

然而我们并没有这个函数，自然也就无法得到值，此时程序扔出一个错误

```lisp
=> ("hello" "list")
ClassCastException java.lang.String cannot be cast to clojure.lang.IFn
```

> ;意为：某个 String（即字符串）无法被作为 IFn（即函数）来使用

说了一大堆，这和我们的 `list` 有什么关系呢？

由于语言本身即是一个 `list` 所以如果阻止它求值，那么我们得到的就是一个 `list`

所以在某种意义上，上面我们展示的三句代码的效果是相同的

又叙述了一大段的文字说明，你可能会发出这样的疑问，那么这又有什么屁用呢？

我们可以对这个 `list` 进行操作

## 这里介绍几个常用的操作：

### `first` 和 `second` 函数分别用来取出集合中的第一个或第二个元素

* 使用 `first`

```lisp
=> (first '("hello" "list"))
hello
```

* 使用 `second`

```lisp
=> (second '("hello" "list"))
list
```

### rest 函数用来返回除了 first 之后的元素，并以 list 的形式作为返回

```lisp
=> (rest '("hello" "list"))
("list")
```

### nth 函数用来取出指定位置的元素，要取第几个元素的索引值放在第三个参数的位置

```lisp
=> (nth '("hello" "list") 0)
hello
```

注意，在大多数程序语言中，索引是以 0 开始的，也就是第一个元素的编号为 0，第二个元素的编号为 1 ...

和我们的直觉一样，`list` 是可以嵌套的，这样就使得你可以创造更为复杂的结构

## 我们来看一下如何操作嵌套结构

* 获取list的第一个元素

```lisp
=> (first '(("屠龙宝刀" "点击就送") "激光剑" "无尽之刃" "传送枪" "物理学圣剑"))
("屠龙宝刀" "点击就送")
```

* 获取list的第一个元素的最后一个元素

```lisp
=> (second (first '(("屠龙宝刀" "点击就送") "激光剑" "无尽之刃" "传送枪" "物理学圣剑")))
点击就送
```

### 我们来讲解一下上面第二句代码

首先我们有了一个拥有 5 个元素的列表

```lisp
'(("屠龙宝刀" "点击就送") "激光剑" "无尽之刃" "传送枪" "物理学圣剑"))
```

> 这里我们注意，使用 `'` 会导致它之后的一个括号中的所有内容都被阻止求值，所以在内层的列表无需再次使用 `'`

#### 但如果直接使用 `list` 函数则会出现错误

```lisp
=> (list ("屠龙宝刀" "点击就送") "激光剑" "无尽之刃" "传送枪" "物理学圣剑")
ClassCastException java.lang.String cannot be cast to clojure.lang.IFn
```

#### 五个元素分别为

*　0号 ("屠龙宝刀" "点击就送")列表也可以作为一个元素（实际上任意表达式都可以作为元素）
*　1号 "激光剑"
*　2号 "无尽之刃"
*　3号 "传送枪"
*　4号 "物理学圣剑"

#### 在它的外面我们套了一层函数 `first`

```lisp
(first '(("屠龙宝刀" "点击就送") "激光剑" "无尽之刃" "传送枪" "物理学圣剑"))
```

`first` 函数的返回值，等于它的参数的第一个位置所存放的元素
> 即为 ("屠龙宝刀" "点击就送")

把这个值作为外层的 `second` 函数的参数

#### 所以我们就得到了类似的效果

```lisp
(second '("屠龙宝刀" "点击就送"))
```

于是取第二个位置，结果为 "点击就送"
>（至于字符串显示出来是否还有双引号，不同的环境的结果不同）

## 下面我们来看一下 vector

我们可以使用 `vector` 函数来创建一个 `vector`

```lisp
=> (vector "hello" "vec")
["hello" "vec"]
```

> 注意到 `vector` 看起和 `list` 有所不同，它使用中括号来包围元素

这也是我们简便生成它的语法

```lisp
=> ["hello" "vec"]
["hello" "vec"]
```

> 这里并不需要使用阻止求值

### 还记得本文第一句来自于第一位图灵奖得主 Alan J. Perlis 的格言么？

我们刚学到的可以用于 list 的函数也可以用于 `vector`

```lisp
=> (first ["hello" "vec"])
hello
```


```lisp
=> (second ["hello" "vec"])
vec
```

```lisp
=> (rest ["hello" "vec"])
("vec") ;虽然这里我们使用 rest 操作的是 vector，但 rest 仍任返回 list 形式
```

```lisp
=> (nth ["hello" "vec"] 1)
vec
```

### 同样可以嵌套

```lisp
=> (second (first [["屠龙宝刀" "点击就送"] "激光剑" "无尽之刃" "传送枪" "物理学圣剑"]))
点击就送
```

#### 我们甚至可以在 `vector` 里嵌套 `list`，或者在 `list` 里面嵌套 `vector`

```lisp
=> (second (first ['("屠龙宝刀" "点击就送") "激光剑" "无尽之刃" "传送枪" "物理学圣剑"]))
点击就送
```

```lisp
=> (second (first '(["屠龙宝刀" "点击就送"] "激光剑" "无尽之刃" "传送枪" "物理学圣剑")))
点击就送
```

> 这多亏了不同的数据结构使用了同一套抽象

> 我们得以用统一的形式（同一函数）来操作不同的数据结构，让你好似在操作同一种数据结构

### `list` 和 `vector` 有很多共同点，就如同已经向你展示的一样，但 `ector` 拥有一些其它特性

#### 使用 `subvec` 函数来取出指定起止位置的内容

后两个参数分别表示起止的索引位置（不包括结束位置元素）

```lisp
=> (subvec [["屠龙宝刀" "点击就送"] "激光剑" "无尽之刃" "传送枪" "物理学圣剑"] 1 3)
["激光剑" "无尽之刃"]
```
> 这个例子给出的起止位置为 1 3，不包括结束位置的元素，于是从 1 开始取到 2
> 结果组成一个 vector 作为返回值

#### 使用 `assoc` 来 “改变” 指定位置的内容

后两个参数分别是要 “改变” 的元素的位置，和改变后的值，返回 “改变” 后的结果

```lisp
=> (assoc [["屠龙宝刀" "点击就送"] "激光剑" "无尽之刃" "传送枪" "物理学圣剑"] 1 "lightsaber")
[["屠龙宝刀" "点击就送"]
"lightsaber"
"无尽之刃"
"传送枪"
"物理学圣剑"]
```

> 注意我们使用了带引号的 “改变”，这表示实际上并没有对原来的 vector 做出任何改变
> 这个特性会在之后的文章中进行说明

#### nth 语意可以直接被使用

```lisp
=> (nth ["hello" "vec"] 1)
vec

=> (["hello" "vec"] 1)
vec
```

> 其实 `["hello" "vec"]` 本身就是个函数，所以可以把它放在函数的位置

> 这个函数的功能和 `nth` 一样，所以我们可以方便的从 `vector` 里面取数据

> 你可以把你学到 `first` `second` `rest` 用于 `map` 和 `set`

> 但你不能把 `nth` 用于 `map`

> 原因是 `map` 没有实现 `Indexed`，而 `nth` 只能作用于实现了 Indexed 的集合

> 而 `first` `second` `rest` 可以作用于实现了 `Sequence` 的集合，所有的 `Clojure` 集合都实现了 `Sequence`

> `map` 会在之后的章节中做详细介绍，它是 `Clojure` 中非常实用的一种数据结构

如果你在阅读本文时感到吃力，你可以先把文中的代码在你的机器上运行一下，观察运行结果，然后试着更改参数，看看返回结果是否满足你的猜想

待你基本了解工作效果之后，再次阅读本文，这种学习方式会帮助你更好地理解本文（或者其它程序设计类教程）

## 除了本文介绍的方法之外，操作集合的函数还有很多

你可以访问 http://clojuredocs.org/ 查阅 API[2] 的小例子，也可以访问官方网站 http://clojure.github.io/clojure/ 来直接查阅官方 `API` 说明，英文苦手们可以访问中文翻译项目 http://clojure-api-cn.readthedocs.io/en/latest/


<span id="structure"></span>[1]: 数据结构：简单来说，就是如何“摆放”值，和如何对值进行操作的一种抽象 [↩](#fn_link_structure)

<span id="api"></span>[2]: Application Programming Interface，即应用编程接口。是一种描述函数如何工作的描述文档 [↩](#fn_link_api)

<span id="identity"></span>[3]: 同像性并不是 Clojure 的专利，其它很多语言也具有同像性，包括所有 Lisp 方言、机器语言（汇编语言）、Prolog 等 [↩](#fn_link_identity)

<span id="sugar"></span>[4]: 语法糖：便于程序员书写的一种简化语法的 “甜甜的” 东西 [↩](#fn_link_sugar)