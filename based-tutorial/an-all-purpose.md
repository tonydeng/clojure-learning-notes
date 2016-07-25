# 以不变应万变

> 一个抽象的过程就是寻找变化中的不变量

在之前的学习中我们学习了如何定义我们的集合，一个很自然的想法就是修改这个集合。

## 复习

我们可以使用 `assoc` 函数来替换一个集合中的元素

```lisp
=> (assoc ["第一个元素" "第二个元素"] 0 "first")
["first" "第二个元素"]
```

`assoc` 函数的第一个参数是你要进行替换的集合，第二个参数是你要替换的元素所在“位置”，第三个参数是替换后的元素。
>（注意，索引位置以 0 开始。）

返回值是修改过后的集合的值。

然而，`Clojure` 里集合类型是不可变的。`assoc` 函数的替换其实并没有改变原集合元素的内容,它返回的是一个“新的”集合。
>（你不用担心浪费存储空间，这个“新”的集合大部分重用了“老”集合。）

我们可以通过以下代码观察：

```lisp
=> (def my-vec ["第一个元素" "第二个元素"])
#'my-clojure-study.core/my-vec

=> (assoc my-vec 0 "first")
["first" "第二个元素"]

=> (print my-vec)
[第一个元素 第二个元素]
nil
```

在这段代码中，我们首先定义了一个 `vector`，然后我们使用 `assoc` 函数来对这个 `vector` 进行“替换”。把 `0` 号元素替换为 "`first"`。看起来我们已经改动了这个 `my-vec`。但是当我们使用 `print` 函数来观察这个集合的时候却发现，他的值没有发生变化。

那我们如何保存改变之后的值呢？

我们可以再给这个改变之后的值取一个名字。

```lisp
=> (def my-vec ["第一个元素" "第二个元素"])
#'my-clojure-study.core/my-vec
=> (def modifyed-vec (assoc my-vec 0 "first"))
#'my-clojure-study.core/modified-vec

=> (print my-vec)
[第一个元素 第二个元素]
nil

=> (print modifyed-vec)
[first 第二个元素]
nil
```

更常见的做法是把这个改变作为一个值，传递给下一个需要这个值的表达式（如一个函数。我们在之后的定义函数一节里再进行详细介绍）

## 在 `Clojure` 里，值是不可变的。

我们为什么需要这种不可变的设计呢？

一个很重要的原因就是，使用不可变的值可以更为简便的实现多线程，当你持有一个值的引用的时候，你不用担心会有其它线程修改你的值。

>（另一个“文绉绉”的说法是，这样更能实现一个“纯函数”，即不修改其它值或者其他状态的函数[ [1] ](#function)。）<span id="fn_link_function"></span>

## 这里再介绍几个对集合进行操作的函数

不再一一叙述每个参数是什么，大家在代码中观察，以及在自己的机器上运行观察结果，然后修改部分参数观察是否符合预期。

### 使用 `cons` 函数把一些元素添加到集合的头部

```lisp
=> (def my-vec ["第一个元素" "第二个元素"])
#'my-clojure-study.core/my-vec
=> (cons "第零个元素" my-vec)
("第零个元素" "第一个元素" "第二个元素")
```

### 使用 `conj` 函数来添加一些元素到集合中

```lisp
=> (def my-vec ["第一个元素" "第二个元素"])
#'my-clojure-study.core/my-vec
=> (conj my-vec "第三个元素")
["第一个元素" "第二个元素" "第三个元素"]
=> (conj my-vec "第三个元素" "第四个元素")
["第一个元素" "第二个元素" "第三个元素" "第四个元素"]
　
=> my-vec
["第一个元素" "第二个元素"]
my-vec 的值并没有发生改变。
```

> 注意，`conj` 函数并不是把元素添加到末尾的位置，它只能保证以最快的速度添加进一种数据结构。

#### 对于 `vector` 来说是末尾，但是对 `list` 来说是头部：

```lisp
=> (def my-list '("第一个元素" "第二个元素"))
#'my-clojure-study.core/my-list
=> (conj my-list "第三个元素")
("第三个元素" "第一个元素" "第二个元素")
=> my-list
("第一个元素" "第二个元素")
my-list 的值同样没有发生改变。
```

### 使用 `into` 函数来把两个集合进行合并。

和`conj` 函数一样，它只保证以最快的速度合并。

```lisp
=> (def vec-1 ["一号元素"])
#'my-clojure-study.core/vec-1
=> (def vec-2 ["二号元素" 3])
#'my-clojure-study.core/vec-2
　
=> (into vec-1 vec-2)
["一号元素" "二号元素" 3]
　
；list 的情况
=> (def list-1 '("first"))
#'my-clojure-study.core/list-1
=> (def list-2 '("second"))
#'my-clojure-study.core/list-2
　
=> (into list-1 list-2)
("second" "first")
```

如果你接触过其它“可变的”编程语言，那么你需要提醒自己，使用 `def` 定义的不是变量，而是一个不可变值。

而当你真的需要在两个操作之间交换一些信息，比如在多线程中共享一些资源，那么 `Clojure` 单独提供了一些类型。我们在以后的多线程部分再做详细讲解。


<span id="function"></span>[1]: 即，没有副作用的函数。 [↩](#fn_link_function)