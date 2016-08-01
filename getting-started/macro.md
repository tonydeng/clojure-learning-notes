# 宏


`Macro`是函数式编程里面很重要的一个概念，在之前，我们已经使用了`Clojure`里面的一些`macro`，譬如`when`，`and`等，我们可以通过`macroexpand`获知：

```lisp
user=> (macroexpand '(when true [1 2 3]))
(if true (do [1 2 3]))
```

```lisp
user=> (doc when)
-------------------------
clojure.core/when
([test & body])
Macro
  Evaluates test. If logical true, evaluates body in an implicit do.
nil
```

可以看到，`when`其实就是`if` + `do`的封装，很类似C语言里面的`macro`。

## defmacro

我们可以通过`defmacro`来定义`macro`：

```lisp
user=> (defmacro my-plus
  #_=> "Another plus for a + b"
  #_=> [args]
  #_=> (list (second args) (first args) (last args)))
#'user/my-plus
user=> (my-plus (1 + 1))
2
user=> (macroexpand '(my-plus (1 + 1)))
(+ 1 1)
```

`macro`的定义比较类似函数的定义，我们需要定义一个`macro name`，譬如上面的`my-plus`，一个可选择的`macro document`，一个参数列表以及`macro body`。`body`通常会返回一个`list`用于后续被`Clojure`进行执行。

我们可以在`macro body`里面使用任何`function`，`macro`以及`special form`，然后使用`macro`的时候就跟函数调用一样。但是跟函数不一样的地方在于函数在调用的时候，参数都是先被`evaluated`，然后才被传入函数里面的，但是对于`macro`来说，参数是直接传入`macro`，而没有预先被`evaluated`。

我们也能在`macro`里面使用`argument destructuring`技术，进行参数绑定：

```lisp
user=> (defmacro my-plus2
  #_=> [[op1 op op2]]
  #_=> (list op op1 op2))
#'user/my-plus2
user=> (my-plus2 (1 + 1))

Symbol and Value
```

编写`macro`的时候，我们其实就是构建`list`供`Clojure`去`evaluate`，所以在`macro`里面，我们需要`quote expression`，这样才能给`Clojure`返回一个没有`evaluated`的`list`，而不是在`macro`里面就自己`evaluate`了。也就是说，我们需要明确了解`symbol`和`value`的区别。

譬如，现在我们要实现这样一个功能，一个`macro`，接受一个`expression`，打印并且输出它的值，可能看起来像这样:

```lisp
user=> (let [result 1] (println result) result)
1
1
```

然后我们定义这个`macro`：

```lisp
user=> (defmacro my-print
  #_=> [expression]
  #_=> (list let [result expression]
  #_=> (list println result)
  #_=> result))
```

我们会发现出错了，错误为`"Can't take value of a macro: #'clojure.core/let"`，为什么呢？在上面这个例子中，我们其实想得到的是`let symbol`，而不是得到`let`这个`symbol`引用的`value`，这里`let`并不能够被`evaluate`。

所以为了解决这个问题，我们需要`quote let`，只是返回`let`这个`symbol`，然后让`Clojure`外面去负责`evaluate`，如下：

```lisp
user=> (defmacro my-print
  #_=> [expression]
  #_=> (list 'let ['result expression]
  #_=> (list 'println 'result)
  #_=> 'result))
#'user/my-print
user=> (my-print 1)
1
1
```

## Quote

### Simple Quoting

如果我们仅仅想得到一个没有`evaluated`的`symbol`，我们可以使用`quote`:

```lisp
user=> (+ 1 2)
3
user=> (quote (+ 1 2))
(+ 1 2)
user=> '(+ 1 2)
(+ 1 2)
user=> '123
123
user=> 123
123
user=> 'hello
hello
user=> hello

CompilerException java.lang.RuntimeException: Unable to resolve symbol: hello in this context
```
### Syntax Quoting

在前面，我们通过`'`以及`quote`了解了`simple quoting`，`Clojure`还提供了`syntax quoting` ```

```lisp
user=> `1
1
user=> `+
clojure.core/+
user=> '+
+
```

可以看到，`syntax quoting`会返回`fully qualified symbol`，所以使用`syntax quoting`能够让我们避免命名冲突。

另一个`syntax quoting`跟`simple quoting`不同的地方在于，我们可以在`syntax quoting`里面使用`~`来`unquote`一些`form`，这等于是说，我要`quote`这一个`expression`，但是这个`expression`里面某一个`form`先`evaluate`，譬如:

```lisp
user=> `(+ 1 ~(inc 1))
(clojure.core/+ 1 2)
user=> `(+ 1 (inc 1))
(clojure.core/+ 1 (clojure.core/inc 1))
```

这里还需要注意一下`unquote splicing`:

```lisp
user=> `(+ ~(list 1 2 3))
(clojure.core/+ (1 2 3))
user=> `(+ ~@(list 1 2 3))
(clojure.core/+ 1 2 3)
```

`syntax quoting`会让代码更加简洁，具体到前面`print`那个例子，我们`let`这些都加了`quote`，代码看起来挺丑陋的，如果用`syntax quoting`，如下:

```lisp
user=> (defmacro my-print2
  #_=> [expression]
  #_=> `(let [result# ~expression]
  #_=> (println result#)
  #_=> result#))
#'user/my-print2
user=> (my-print2 1)
1
1
```

