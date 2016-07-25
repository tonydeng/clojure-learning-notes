# Clojure基本语法

`Clojure`的语法非常的简单，只要熟悉`Lisp`，几乎可以无缝使用`Clojure`了。

## Form

`Clojure`的代码是由一个一个form组成的，`form`可以是基本的数据结构，譬如`number`，`string`等，也可以是一个`operation`，对于一个`operation`来说，合法的结构如下:

```lisp
(operator operand1 operand2 ... operandn)
```

第一个是`operator`，后面的就是该`operator`的参数，譬如`(+ 1 2 3)`，`operator`就是“+”， 然后参数为1， 2， 3，如果我们执行这个`form`，得到的结果为6。

## Control Flow

`Clojure`的`control flow`包括`if`，`do`和`when`。

### If

`if`的格式如下:

```lisp
(if boolean-form
    then-form
    optional-else-form)
```

如果`boolean-form`为`true`，就执行`then-form`，否则执行`optional-else-form`，一些例子：

```lisp
user=> (if false "hello" "world")
"world"
user=> (if true "hello" "world")
"hello"
user=> (if true "hello")
"hello"
user=> (if false "hello")
nil
```

### Do

通过上面的`if`可以看到，我们的`then`或者`else`只有一个`form`，但有时候，我们需要在这个条件下面，执行多个`form`，这时候就要靠`do`了。

```lisp
user=> (if true
  #_=> (do (println "true") "hello")
  #_=> (do (println "false") "world"))
true
"hello"
```

在上面这个例子，我们使用`do`来封装了多个`form`，如果为`true`，首先打印`true`，然后返回“hello”这个值。

### When

`when`类似`if`和`do`的组合，但是没有`else`这个分支了，

```lisp
user=> (when true
  #_=> (println "true")
  #_=> (+ 1 2))
true
3
```

### nil, true, false

`Clojure`使用`nil`和`false`来表示逻辑假，而其他的所有值为逻辑真，譬如：

```lisp
user=> (if nil "hello" "world")
"world"
user=> (if "" "hello" "world")
"hello"
user=> (if 0 "hello" "world")
"hello"
user=> (if true "hello" "world")
"hello"
user=> (if false "hello" "world")
"world"
````

我们可以通过`nil?`来判断一个值是不是`nil`，譬如：

```lisp
user=> (nil? nil)
true
user=> (nil? false)
false
user=> (nil? true)
false
```

也可以通过=来判断两个值是否相等：

```lisp
user=> (= 1 1)
true
user=> (= 1 2)
false
user=> (= nil false)
false
user=> (= false false)
true
```

我们也可以通过`and`和`or`来进行布尔运算，`or`返回第一个为`true`的数据，如果没有，则返回最后一个，而`and`返回第一个为`false`的数据，如果都为`true`，则返回最后一个为`true`的数据，譬如：

```lisp
user=> (or nil 1)
1
user=> (or nil false)
false
user=> (and nil false)
nil
user=> (and 1 false 2)
false
user=> (and 1 2)
2
```

### def

我们可以通过`def`将一个变量命名，便于后续使用，譬如:

```lisp
user=> (def a [1 2 3])
#'user/a
user=> (get a 1)
2
```