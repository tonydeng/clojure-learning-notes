# 并发

`Clojure`是一门支持并发编程的语言，它提供了很多特性让我们非常的方便进行并发程序的开发。

## Future

`Future`可以让我们在另一个线程去执行任务，它是异步执行的，所以调用了`future`之后会立即返回。譬如：

```lisp
user=> (future (Thread/sleep 3000) (println "I'am back")) 
#object[clojure.core$future_call$reify__6736 0x7118d905 {:status :pending, :val nil}]
user=> (println "I'am here")
I'am here
nil
user=> I'am back
```

在上面的例子中，`sleep`会阻塞当前线程的执行，但是因为我们用了`future`，所以`clojure`将其放到了另一个线程中，然后继续执行下面的语句。

有时候，我们使用了`future`之后，还需要知道`future`的任务执行的结果，知识后就需要用`defer`来实现了。我们可以使用`defer`或者`@`来获取`future`的`result`，譬如：

```lisp
user=> (let [result (future (println "run only once") (+ 1 1))]
  #_=> (println (deref result))
  #_=> (println @result))
run only once
2
2
nil
```

`deref`还可以支持`timeout`设置，如果超过了等待时间，就返回一个默认值。

```lisp
user=> (deref (future (Thread/sleep 100) 0) 10 5)
5
user=> (deref (future (Thread/sleep 100) 0) 1000 5)
0
```

我们也可以使用`realized`?来判断一个`future`是否完成

```lisp
user=> (realized? (future (Thread/sleep 1000)))
false
user=> (let [f (future)]
  #_=> @f
  #_=> (realized? f))
true
```

## Delay

`Delay`可以让我们定义一个稍后执行的任务，并不需要现在立刻执行。

```lisp
user=> (def my-delay
  #_=> (delay (let [msg "hello world"]
  #_=> (println msg)
  #_=> msg)))
#'user/my-delay
```

我们可以通过`@`或者`force`来执行`delay`的任务

```lisp
user=> @my-delay
hello world
"hello world"
user=> (force my-delay)
"hello world"
```

`Clojure`会将`delay`的任务结果缓存，所以第二次`delay`的调用我们直接获取的是缓存结果。

我们可以将`delay`和`future`一起使用，定义一个`delay`操作，在`future`完成之后，调用`delay`，譬如：

```lisp
user=> (let [notify (delay (println "hello world"))]
  #_=> (future ((Thread/sleep 1000) (force notify))))
#object[clojure.core$future_call$reify__6736 0x2de625f3 {:status :pending, :val nil}]
user=> hello world
```

## Promise

`Promise`是一个承诺，我们定义了这个`promise`，就预期后续会得到相应的`result`。我们通过`deliver`来将`result`发送给对应的`promise`，如下：

```lisp
user=> (def my-promise (promise))
#'user/my-promise
user=> (deliver my-promise (+ 1 1))
#object[clojure.core$promise$reify__6779 0x30dc687a {:status :ready, :val 2}]
user=> @my-promise
2
```

`promise`也跟`delay`一样，会缓存`deliver`的`result`

```lisp
user=> (deliver my-promise (+ 1 2))
nil
user=> @my-promise
2
```

我们也可以将`promise`和`future`一起使用

```lisp
user=> (let [hello-promise (promise)]
  #_=> (future (println "Hello" @hello-promise))
  #_=> (Thread/sleep 1000)
  #_=> (deliver hello-promise "world"))
Hello world
```

## Atom

在并发编程里面，`a = a + 1`这条语句并不是安全的，在`clojure`里面，我们可以使用`atom`完成一些原子操作。如果大家熟悉c语言里面的`compare and swap`那一套原子操作函数，其实对`Clojure`的`atom`也不会陌生了。

我们使用`atom`创建一个`atom`，然后使用`@`来获取这个`atom`当前引用的值:

```lisp
user=> (def n (atom 1))
#'user/n
user=> @n
1
```

如果我们需要更新该`atom`引用的值，我们需要通过一些原子操作来完成，譬如：

```lisp
user=> (swap! n inc)
2
user=> @n
2
user=> (reset! n 0)
0
user=> @n
0
```

## Watch

对于一些数据的状态变化，我们可以使用`watch`来监控，一个`watch function`包括4个参数，关注的`key`，需要`watch`的`reference`，譬如`atom`等，以及该`reference`之前的`state`以及新的`state`，譬如：

```lisp
user=> (defn watch-n
  #_=> [key watched old-state new-state]
  #_=> (if (> new-state 1)
  #_=> (println "new" new-state)
  #_=> (println "old" old-state)))
user=> (def wn (atom 1))
#'user/wn
user=> @wn
1
user=> (add-watch wn :a watch-n)
#object[clojure.lang.Atom 0x4b28dcf8 {:status :ready, :val 1}]
user=> (reset! wn 1)
old 1
1
user=> (reset! wn 2)
new 2
2
```

## Validator

我们可以使用`validator`来验证某个`reference`状态改变是不是合法的，譬如：

```lisp
user=> (defn validate-n
  #_=> [n]
  #_=> (> n 1))
#'user/validate-n
user=> (def vn (atom 2 :validator validate-n))
#'user/vn
user=> (reset! vn 10)
10
user=> (reset! vn 0)

IllegalStateException Invalid reference state  clojure.lang.ARef.validate (ARef.java:33)
```

在上面的例子里面，我们建立了一个`validator`，参数`n`必须大于1，否则就是非法的。

## Ref

在前面，我们知道使用`atom`，能够原子操作某一个`reference`，但是如果要操作一批`atom`，就不行了，这时候我们就要使用`ref`。

```lisp
user=> (def x (ref 0))
#'user/x
user=> (def y (ref 0))
#'user/y
```

我们创建了两个`ref`对象`x`和`y`，然后在`dosync`里面对其原子更新

```lisp
user=> (dosync 
  #_=> (ref-set x 1)
  #_=> (ref-set y 2)
  #_=> )
2
user=> [@x @y]
[1 2]
user=> (dosync 
  #_=> (alter x inc)
  #_=> (alter y inc))
user=> (dosync 
  #_=> (commute x inc)
  #_=> (commute y inc))
```

`ref-set`就类似于`atom`里面的`reset!`，`alter`就类似`swap!`，我们可以看到，还有一个`commute`也能进行`reference`的更新，它类似于`alter`，但是稍微有一点不一样。

在一个事务开始之后，如果使用`alter`，那么在修改这个`reference`的时候，`alter`会去看有没有新的修改，如果有，就会重试当前事务，而`commute`则没有这样的检查。所以如果通常为了性能考量，并且我们知道程序没有并发的副作用，那就使用`commute`，如果有，就老老实实使用`alter`了。

我们通过`@`来获取`reference`当前的值，在一个事务里面，我们通过`ensure`来保证获取的值一定是最新的:

```lisp
user=> (dosync 
  #_=> (ref-set x (ensure y)))
```

## Dynamic var

通常我们通过`def`定义一个变量之后，最好就不要更改这个`var`了，但是有时候，我们又需要在某些`context`里面去更新这个`var`的值，这时候，最好就使用`dynamic var`了。

我们通过如下方式定义一个`dynamic var`:

```lisp
user=> (def ^:dynamic *my-var* "hello")
#'user/*my-var*
```

`dynamic var`必须用`*`包裹，在`lisp`里面，这个叫做`earmuffs`，然后我们就能够通过`binding`来动态改变这个`var`了:

```lisp
user=> (binding [*my-var* "world"] *my-var*)
"world"
user=> (println *my-var*)
hello
user=> (binding [*my-var* "world"] (println *my-var*)
  #_=> (binding [*my-var* "clojure"] (println *my-var*)) (println *my-var*))
world
clojure
world
```

可以看到，`binding`只会影响当前的`stack binding`。

