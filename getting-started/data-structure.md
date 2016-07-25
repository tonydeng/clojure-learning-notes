# Clojure基础数据结构

`Clojure`有如下几种基本的数据类型，它们都是`immutable`的，也就是说，我们不可能更改它们，任何对原有数据结构的修改都会生成一份新的copy。

## Number

`Clojure`的`number`包括`int`，`float`以及`ratio`，譬如下面这些都是`number`：

```lisp
1      ; int
1.0    ; double
1 / 5  ; ratio
```

## String

`Clojure`的`string`是用双引号来表示的，譬如`”abc"`，如果字符串里面有双引号，我们使用`\"`来表示，譬如`"\"abc\""`。

## Map

`Clojure`的`map`跟其他语言的一样，就是一个`kv dictionary`，`clojure`的`map`有`hash map`以及`sorted map`两种，不过通常我们都不用特别区分。

`map`使用`{}`来表示，`map`的`key`可以是`keyword`，也可以是基本的数据类型，譬如`{:a 1, 2 2, 3.0 3, "4" 4}`，`map`里面也能嵌套`map`，譬如`{:a {:b 1}}`。

我们可以通过`hash-map`创建一个`hash map`，譬如 `(hasp-map :a 1 :b 2)`，通过`get`函数来获取`map`里面的数据，譬如：

```lisp
user=> (get {:a 1 :b 2} :a)
1
user=> (get {:a 1 :b 2} :c)
nil
```

通过`get-in`获取嵌套map的数据，譬如:

```lisp
user=> (get-in {:a {:b 1}} [:a :b])
1
user=> (get-in {:a {:b 1}} [:a :c])
nil
user=> (get-in {:a {:b 1}} [:b :a])
nil
```

## Keyword

在`map`里面，我们出现了`keyword`的概念，`keyword`使用`:`来表示，通常用在`map`的`key`上面。譬如下面这些都是`keyword:`

```lisp
:a
:hello
:34
:_?
```

`keyword`能够被当成`function`，譬如：

```lisp
user=> (:a {:a 1 :b 2})
1
```

它等价于

```lisp
user=> (get {:a 1 :b 2} :a)
1
```

如果`keyword`不存在，我们也可以指定一个默认值:

```lisp
user=> (:c {:a 1 :b 2} "abc")
"abc"
user=> (get {:a 1 :b 2} :c "abc")
"abc"
```

## Vector

`vector`就是数组，以`index 0`开始，使用`[]`表示。

```lisp
user=> [1 2 3]
[1 2 3]
user=> (get [1 2 3] 0)
1
```

我们可以使用`vector`来创建一个`vector`，譬如：

```lisp
user=> (vector 1 2 3)
[1 2 3]
```

使用`conj`函数往`vector`里面追加数据:

```lisp
user=> (conj [1 2 3] 4)
[1 2 3 4]
```

## List

`list`也就是链表，跟`vector`有一些不同，譬如不能通过`get`来获取元素。`list`使用`()`来表示，因为`()`在`Clojure`里面是作为`operations`来进行求值的，所以我们需要用`'()`来避免`list`被求值。

我们也可以使用`list`函数来构造`list`，譬如：

```lisp
user=> `(1 2 3)
(1 2 3)
user=> (list 1 2 3)
(1 2 3)
```

`list`不能使用`get`，但可以用`nth`函数，但需要注意`out of bound`的`error`。

```lisp
user=> (nth '(1 2 3) 0)
1
user=> (nth '(1 2 3) 4)

IndexOutOfBoundsException   clojure.lang.RT.nthFrom (RT.java:871)
```

我们也能够通过`conj`函数在`list`里面追加元素，不过不同于`vector`，是从头插入的:

```lisp
user=> (conj '(1 2 3) 4)
(4 1 2 3)
```

## Set

`set`是唯一值的集合，使用`#{}`表示，我们也可以`hash-set`函数来进行`set`的创建：

```lisp
user=> #{1 2 3}
#{1 3 2}
user=> (hash-set 1 2 3 1)
#{1 3 2}
```

我们可以使用`set`函数将`vector`或者`list`转成`set`，譬如：

```lisp
user=> (set [1 2 3 1])
#{1 3 2}
user=> (set '(1 2 3 1))
#{1 3 2}
```

我们使用`conj`函数在`set`里面添加元素:

```lisp
user=> (conj #{1 2} 1)
#{1 2}
user=> (conj #{1 2} 3)
#{1 3 2}
```

`contains?`用来判断某一个值是否在`set`里面，譬如：

```lisp
user=> (contains? #{:a :b} :a)
true
user=> (contains? #{:a :b} :c)
false
user=> (contains? #{:a :b nil} nil)
true
```

我们也可以使用`get`来获取某个元素：

```lisp
user=> (get #{:a :b nil} :a)
:a
user=> (get #{:a :b nil} nil)
nil
```

使用`keyword`的方式也可以:

```lisp
user=> (:a #{:a :b})
:a
user=> (:c #{:a :b})
nil
```