# 函数

`Clojure`是一门函数式编程语言，`function`具有非常重要的地位。

## Function Call

函数调用很简单，我们已经见过了很多例子:

```lisp
(+ 1 2 3)
(* 1 2 3)
```

我们也可以将函数返回让外面使用：

```lisp
user=> ((or + -) 1 2 3)
6
```

如果我们调用了非法的函数，会报错：

```lisp
user=> (1 2 3)

ClassCastException java.lang.Long cannot be cast to clojure.lang.IFn  user/eval1491 (form-init7840101683239336203.clj:1)
```

这里，1并不是一个合法的`operator`。

## Function Define

一个函数，通常由几个部分组成:

1. defn
1. 函数名称
1. 函数说明（可选)
1. 参数列表，使用[]封装
1. 函数body

一个例子：

```lisp
user=> (defn fun1
  #_=> "a function example"
  #_=> [name]
  #_=> (str "hello " name))
#'user/fun1
user=> (fun1 "world")
"hello world"
user=> (doc fun1)
-------------------------
user/fun1
([name])
  a function example
nil
```

## Overloading

我们也可以进行函数重载，如下：

```lisp
user=> (defn func-multi
  #_=> ([](str "no arg"))
  #_=> ([name1](str "arg " name1))
  #_=> ([name1 name2](str "arg " name1 " " name2)))
user=> (func-multi)
"no arg"
user=> (func-multi "1")
"arg 1"
user=> (func-multi "1" "2")
"arg 1 2"
```

## Variable arguments

我们使用`&`来支持可变参数

```lisp
user=> (defn func-args
  #_=> [name & names]
  #_=> (str name " " (clojure.string/join " " names)))
#'user/func-args
user=> (func-args "a")
"a "
user=> (func-args "a" "b")
"a b"
user=> (func-args "a" "b" "c")
"a b c"
```

## Destructuring

我们可以在函数参数里面通过特定的`name`来获取对应的`collection`的数据，譬如：

```lisp
user=> (defn f-vec1 [[a]] [a])
#'user/f-vec1
user=> (f-vec1 [1 2 3])
[1]
user=> (defn f-vec2 [[a b]] [a b])
#'user/f-vec2
user=> (f-vec2 [1 2 3])
[1 2]
```

在上面的例子里面，我们的参数是一个`vector`，然后`[a]`以及`[a b]`表示，我们需要获取这个`vector`里面的第一个以及第二个数据，并且使用变量`a，b`存储。

我们也可以使用`map`，譬如：

```lisp
user=> (defn f-map [{a :a b :b}] [a b])
#'user/f-map
user=> (f-map {:a 1 :b 2})
[1 2]
user=> (f-map {:a 1 :c 2})
[1 nil]
```

上面这个例子我们可以用`:keys`来简化，

```lisp
user=> (defn f-map-2 [{:keys [a b]}] [a b])
#'user/f-map-2
user=> (f-map-2 {:a 1 :b 2 :c 3})
[1 2]
```

我们可以通过`:as`来获取原始的`map`:

```lisp
user=> (defn f-map-3 [{:keys [a b] :as m}] [a b (:c m)])
#'user/f-map-3
user=> (f-map-3 {:a 1 :b 2 :c 3})
[1 2 3]
```

## Body

`Function`的`body`里面可以包括多个`form`，`Clojure`会将最后一个`form`执行的值作为该函数的返回值:

```lisp
user=> (defn func-body []
  #_=> (+ 1 2)
  #_=> (+ 2 3))
#'user/func-body
user=> (func-body)
5
```

## 匿名函数

我们可以通过fn来声明一个匿名函数

```lisp
user=> ((fn [name]  (str "hello " name)) "world")
"hello world"
```

我们也可以通过def来给一个匿名函数设置名称:

```lisp
user=> (def a (fn [name] (str "hello " name)))
#'user/a
user=> (a "world")
"hello world"
```

当然，我们更加简化匿名函数，如下:

```lisp
user=> #(str "hello " %)
#object[user$eval1584$fn__1585 0x72445715 "user$eval1584$fn__1585@72445715"]
user=> (#(str "hello " %) "world")
"hello world"
```

我们使用`%`来表明匿名函数的参数，如果有多个参数，则使用`%1`，`%2`来获取，使用`%&`来获取可变参数。

```lisp
user=> (#(str "hello " %) "world")
"hello world"
user=> (#(str "hello " %1 " " %2) "world" "a ha")
"hello world a ha"

user=> (#(str "hello " %1 " " (clojure.string/join " " %&)) "world" "ha" "ha")
"hello world ha ha"
```