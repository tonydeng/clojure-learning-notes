# 多态性

`Clojure`虽然是一门函数式编程语言，当也能很容易支持类似`OOP`那种`polymorphism`，能让我们写出更好的抽象代码。

## Multimethods

使用`multimethod`是一种快速在代码里面引入`polymorphism`的方法，我们可以定义一个`dispatching function`，然后指定一个`dispatching value`，通过它来确定调用哪一个函数。

譬如，我们需要计算一个图形的面积，我们知道，如果是一个长方形，那么方法就是`with * heigth`，如果是圆形，那么就是 `PI * radius * radius`。

```lisp
; define a multimethod for area with :Shape keyword.
(defmulti area :Shape)
(defn rect [wd ht] {:Shape :Rect :wd wd :ht ht})
(defn circle [radius] {:Shape :Circle :radius radius})
(defmethod area :Rect [r]
    (* (:wd r) (:ht r)))
(defmethod area :Circle [c]
    (* (. Math PI) (* (:radius c) (:radius c))))
(defmethod area :default [x] :oops)
(def r (rect 4 13))
(def c (circle 12))
```

我们在`repl`里面执行:

```lisp
user=> (area r)
52
user=> (area c)
452.3893421169302
user=> (area {})
:oops
```

## Protocol

从上面`multimethod`的实现可以，`multimethod`只是一个`polymorphic`操作，如果我们想实现多个，那么`multimethod`就不能满足了，这时候，我们就可以使用`protocol`。

`protocol`其实更类似其他语言里面`interface`，我们定义一个`protocol`，然后用不同的类型去特化实现，我们以`jepsen`的代码为例，因为`jepsen`可以测试很多`db`，所以它定义了一个`db`的`protocol`。

```lisp
(defprotocol DB
  (setup!     [db test node] "Set up the database on this particular node.")
  (teardown!  [db test node] "Tear down the database on this particular node."))
```

上面的代码定义了一个`DB`的`protocol`，然后有两个函数接口，用来`setup`和`teardown`对应的db，所以我们只需要在自己的`db`上面实现这两个函数就能让`jepsen`调用了，伪代码如下:

```lisp
(def my-db 
  (reify DB
    (setup! [db test node] "hello db")
    (teardown! [db test node] "goodbye db")))
```

然后就能直接使用`DB` `protocol`了。

```lisp
user=> (setup! my-db :test :node)
"hello db"
user=> (teardown! my-db :test :node)
"goodbye db"
```

## Record

有些时候，我们还想在`clojure`中实现`OOP`语言中`class`的效果，用`record`就能很方便的实现。`record`类似于`map`，这点就有点类似于`C++` `class`中的`field`，然后还能实现特定的`protocol`，这就类似于`C++` `class`的`member function`了。

`record`的定义很简单，我们使用`defrecord`来定义：

```lisp
user=> (defrecord person [name age])
user.person
```

这里，我们定义了一个`person`的`record`，它含有`name`和`age`两个字段，然后我们可以通过下面的方法来具体创建一个`person`:

```lisp
; 使用类似java的 . 操作符创建
user=> (person. "siddon" 30)
#user.person{:name "siddon", :age 30}
; 通过 ->person 函数创建
user=> (->person "siddon" 30)
#user.person{:name "siddon", :age 30}
; 通过 map->persion 函数创建，参数是map
user=> (map->person {:name "siddontang" :age 30)
```

因为`record`其实可以认为是一个`map`，所以很多`map`的操作，我们也同样可以用于`record`上面。

```lisp
user=> (def siddon (->person "siddon" 30))
#'user/siddon
user=> (assoc siddon :name "tang")
#user.person{:name "tang", :age 30}
user=> (dissoc siddon :name)
{:age 30}
```

`record`可以实现特定的`protocol`，譬如:

```lisp
(defprotocol SayP 
  (say [this]))

(defrecord person [name age]
  SayP
  (say [this] 
    (str "hello " name)))
```

上面我们定义了`SayP`这个`protocol`，并且让`person`这个`record`实现了相关的函数，然后我们就可以直接使用了。

```lisp
user=> (say (->person "siddon" 30))
"hello siddon"
```



