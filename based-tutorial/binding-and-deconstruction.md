# 绑定与解构

> 是时候给我们的值取个名字了！

## 绑定
　
在之前的学习中，我们学会了如何使用简单的数据结构 --- `list` 和 `vector`，但是每次使用的时候，我们都需要重新写一遍我们的元素内容，使用起来非常麻烦。

于是 `Clojure` 提供了“取名”的功能：`def` 函数 （即 define，定义），`def` 函数的第一个参数是你想给你的值取的名字，第二个参数是你的值

```lisp
=> (def my-items ["短剑" "火把" "汽油"])
#'user/my-items
```

这样以来我们就可以更为方便的使用我们的 `vector` 了

```lisp
=> (first my-items)
"短剑"
```
>（注意，def 所定义的内容不是变量，会在之后的文章中讨论这一问题）

我们观察它的返回值 `#'user/my-items` 它表示我们成功地在 `user` 空间中创建了一个 `my-items`，`user` 空间是 `REPL` 启动后默认的命名空间（namespace），它表示我们的 `my-items` 只在 `user` 中才能使用。

>（命名空间的作用和用法，日后再说…现在你可以把它理解为一个文件夹，我们是在 user 这个“文件夹”下面新建了一个 my-items，如果你去另外的文件夹里面访问，即使那个文件夹也有一个叫 my-items 的东西，那它也不是我们这个 my-items）

### 这里我们简单提一下 `Clojure` 的命名规范

我们使用全小写字母和减号间隔来表示 `Clojure` 里的一个值的名称

如：
> a-beautiful-world 

> items 

> lovely-girl

使用 `def` 函数定义一个值，这个值在整个 `user` 空间都可以被访问

## 下面我们介绍的 `let` 函数，它被称为“局部绑定”

> （在其它文章中也称之为“本地绑定”、“本地值”、“let 绑定”，指的都是我们这里介绍的内容）

之所以叫它“局部绑定”，是因为使用它来命名的名称，只在 `let` 的括号范围内才能使用。

```lisp
=> (let [my-items ["短剑" "火把" "汽油"]]
        (println my-items))
["短剑" "火把" "汽油"]
nil
```

我们在第二和第三个参数之间换行，这并不影响程序的执行，无论你是否换行，但这增加了程序的可读性[ [1] ](#read)。<span id="fn_link_read"></span>

养成良好的编程风格，适当的缩进和换行会增加程序的可读性

* `let` 函数的使用比我们之前所学的函数要复杂一点 *

首先，它的第一个参数是一个 `vector` （使用中括号包围），中括号里元素个数必须为偶数，因为中括号里每一对元素表示“给值取的名字” - “值”（key-value），也就是说，我们可以一次性地给多个值取名字。

然后，它之后的参数是多个表达式，表达式会被 `Clojure` 依次执行。

最后， `let` 函数的返回值等于它最后执行的表达式的值。

### 我们来观察更多的例子

```lisp
=> (let [my-items ["短剑" "火把" "汽油"] my-coins [128]]
     (print my-coins)
     my-items)
[128]
["短剑" "火把" "汽油"]
```

```lisp
=> (let [my-items ["短剑" "火把" "汽油"] my-coins [128]]
     my-items
     my-coins
     (print my-coins))
[128]
nil
```

```lisp
=> (let [my-items ["短剑" "火把" "汽油"] my-coins [128]]
     my-items
     my-coins
     (print my-coins))
   (print my-coins)
[128]
nil
CompilerException java.lang.RuntimeException: Unable to resolve symbol: my-coins in this context
```

第一段代码展示了如何使用 `let` 来一次性地定义两个值 --- `my-items` 和 `my-coins`，然后在 `let` 的括号范围内，我们依次执行了两句表达式，而且我们在表达式之间使用换行以增加可读性。

第一句表达式输出 `my-coins` 的值

第二句表达式直接使用 `my-items`，没有使用括号包围，这表示我们直接使用它的值，而不是把它作为函数

> 由于 `let` 函数规定最后被执行的表达式的值为 `let` 函数的返回值，所以此例中 `let` 函数的返回值为 `my-items` 的值

> 而第二段代码与第一段不同的是，它最后才执行了 `print` 函数

> 我们可以看到， `print` 函数的副作用效果出现 ---  `my-coins` 的值 `[128]` 被打印出来，但是由于 `print` 函数的返回值始终为 `nil`。

> 所以此例中 `let` 函数的返回值为最后执行的表达式的值 --- 即 `print` 函数的值，虽然在它之前我们对 `my-items` 和 `my-coins` 的值进行的访问，但由于没有执行打印到屏幕上的操作，我们无法观察到它们的值

在第三段代码中，我们尝试在 `let` 函数的括号外访问 `my-coins`，结果可想而知，错误信息表示：`Unable to resolve symbol: my-coins` （无法理解符号 my-coins）。

因为 `let` 函数给值取的名字的有效性只在它的“势力范围”之内，即只能在它前后括号的范围内使用。

很多函数隐式地使用了 `let`，在今后的 “定义属于你自己的函数” 章节中，这里所学习到的有关 `let` 的知识就能够派上更大的用场了

## 解构
　
在前一个章节中，我们学习了如何访问一个集合中的元素，但如果每次都这样使用，显得繁琐而无聊。

```lisp
=> (def my-items ["短剑" "火把" "汽油"])
#'user/my-items

=> (first my-items)
短剑
=> (rest my-items)
("火把" "汽油") ;复习一下，rest 函数返回除 first 之外剩余元素的 list 形式
```

尤其是我们想给一个集合里面的元素都取一个新名字时

```lisp
=>(def first-item (first my-items))
#'user/first-item

=>(def rest-item (rest my-items))
#'user/rest-item
```

或者在访问一个多层嵌套的集合时

```lisp
=> (def my-coins 256)
#'user/my-coins
=> (def my-bag [my-items my-coin])
#'user/my-bag

=> (nth (first my-bag) 2)
汽油
```

可以想象如果一个集合里面的元素非常多，或者嵌套层数非常多的时候，这种方式效率十分低下。

不过，当你的嵌套层数非常多的时候，就该反思一下你的设计了。
>（可能写出来的代码你自己也读不懂）

幸亏 `let` 函数给我们提供了一个诱人的访问集合元素的方式

```lisp
=> (def my-bag [["短剑" "火把" "汽油"] 256])
#'user/my-bag
=> (let [[items coins] my-bag]
     (println "你所拥有的装备：" items) ;复习一下，print 函数家族可以接受多个参数，并依次输出他们的值
     (println "你所拥有的金币：" coins))
你所拥有的装备： [短剑 火把 汽油]
你所拥有的金币： 256
nil
```

我们称之为 解构，因为它可以清晰地表现原结构的样子。

如果不使用解构，就会长成这样：

```lisp
=> (let [items (first my-bag) coins (second my-bag)]
     (println "你所拥有的装备：" items)
     (println "你所拥有的金币：" coins))
你所拥有的装备： [短剑 火把 汽油]
你所拥有的金币： 256
nil
```

### 解构的具体用法十分简单

你只需要，在 `let` 函数的第二个参数里，把原来 “给值取的名字” 的位置，写成一个 `vector` 的形式（即用中括号包围），原来填写 “值” 的位置，写上你要解构的集合。

> (let [[你给集合里元素取的新名字] 你要解构的集合])

如果我们把原结构和解构形式放在一起观察的话

```lisp
my-bag [["短剑" "火把" "汽油"]   256 ]
       [      items           coins] my-bag
```

看起来像是把定义集合倒过来写一样，这样我们就在 let 绑定里面给这个两个元素取了一个名字：

* 集合 my-bag 的第一个元素取名为 items
* 集合 my-bag 的第二个元素取名为 coins
> 而且，这个对应关系真实反映了元素的位置

我们可以使用解构从集合中取部分元素

```lisp
=> (def my-bag [["短剑" "火把" "汽油"] 512 2])
#'user/my-bag

=> (let [[items silver-coin] my-bag]
        (println "你所拥有的装备：" items)
        (println "你所拥有的银币：" silver-coin))
你所拥有的装备： [短剑 火把 汽油]
你所拥有的银币： 512
nil
```

上面的例子中，虽然我们的 my-bag 有三个元素，但是解构可以只拿取前两个。

看起来就像这样

```lisp
my-bag [  ["短剑" "火把" "汽油"]     512        2]
       [        items           silver-coin    ]    my-bag
```       

如果你只想要物品和金币，你可以这样来操作

```lisp
=> (let [[items silver-coin gold-coin] my-bag]
        (println "你所拥有的装备：" items)
        (println "你所拥有的金币：" gold-coin))
你所拥有的装备： [短剑 火把 汽油]
你所拥有的金币： 2
nil
```

是不是看起来有点傻，给它取了名字却没有使用。

不过，如果我们不关心银币，那么我们可以给它随便扔一个名字，比如 `sth-I-don't-care`，不过 `Clojure` 规范更倾向于使用短下划线 _ 来命名你不感兴趣的名称。

```lisp
=> (let [[items _ gold-coin] my-bag]
        (println "你所拥有的装备：" items)
        (println "你所拥有的金币：" gold-coin))
你所拥有的装备： [短剑 火把 汽油]
你所拥有的金币： 2
nil
```

其实它只也是一个可以正常使用的名字而已

```lisp
=> (let [[items _ gold-coin] my-bag]
        (println "你所拥有的装备：" items)
        (println "你所拥有的银币：" _)
        (println "你所拥有的金币：" gold-coin))
你所拥有的装备： [短剑 火把 汽油]
你所拥有的银币： 512
你所拥有的金币： 2
nil
```

如果你使用重复的名字，比如使用多个 _ 来忽视掉你不关心的内容，那么后面的值会把前面的值覆盖掉。

```lisp
=> (let [[items _ _] my-bag]
        (println "你所拥有的装备：" items)
        (println "你所拥有的银币：" _)
        (println "你所拥有的金币：" _))
你所拥有的装备： [短剑 火把 汽油]
你所拥有的银币： 2 ;这里金币的值覆盖了银币的值
你所拥有的金币： 2
nil
```

在解构多层嵌套时，更能发挥出它的威力

```lisp
=> (def my-bag [["短剑" "火把" "汽油"] [512 2]])
=> (let [[[first-item _ third-item] [silver-coin gold-coin]] my-bag]
           (println "你背包里的第一件装备：" first-item)
           (println "你背包里的第三件装备：" third-item)
           (println "你所拥有的金币：" gold-coin)
           (println "你所拥有的银币：" silver-coin))
你背包里的第一件装备： 短剑
你背包里的第三件装备： 汽油
你所拥有的金币： 2
你所拥有的银币： 512
nil
```

我们像之前一样对比一下原集合和解构形式

```lisp
my-bag [[  "短剑"     "火把"   "汽油"     ]  [    512           2    ] ]
       [[first-item     _     third-item]  [silver-coin   gold-coin] ]  my-bag
```

这也是我们为什么说，它可以清晰地表现原结构的样子。

解构的额外特性，给剩余元素取名。

```lisp
=> (def my-bag [["短剑" "火把" "汽油"] 512 2])
=> (let [[[first-item _ third-item] & coins] my-bag]
      (println "你背包里的第一件装备：" first-item)
      (println "你背包里的第三件装备：" third-item)
      (println "你所拥有的钱币：" coins))
你背包里的第一件装备： 短剑
你背包里的第三件装备： 汽油
你所拥有的钱币： (512 2)
nil
```

使用 `&` 来把剩余元素作为一个 `list` 绑定到一个名字，在做递归调用的时候这个功能用起来就太爽了。

### 给原集合取名

#### 使用 :as 来给你的原集合取名

> :as 是一个 key （key 会在今后的介绍中出现）

```lisp
=> (let [[[first-item _ third-item] & coins :as my-bag-original] my-bag]
      (println "你背包里的第一件装备：" first-item)
      (println "你背包里的第三件装备：" third-item)
      (println "你所拥有的钱币：" coins)
      (println "全部物品：" my-bag-original))
你背包里的第一件装备： 短剑
你背包里的第三件装备： 汽油
你所拥有的钱币： (512 2)
全部物品： [[短剑 火把 汽油] 512 2]
nil
```

本文所介绍的解构称为顺序解构，它可以用来对顺序集合做解构，包括：

> `list`，`vector`

> 实现了 `java.unit.List` 接口的集合

> `Java` 数组

> 字符串

对字符串的解构结果是一个一个字符

```lisp
=> (let [[f s t] "123"]  
   (print s))
2
nil
```

还有为 map 服务的解构形式，在今后对 map 做单独介绍时再详细说明


<span id="read"></span>[1]: 可读性，指人类阅读程序语言时的“舒适程度”，“易于理解程度”。读起来更容易被理解的程序的可读性就越高 [↩](#fn_link_read)