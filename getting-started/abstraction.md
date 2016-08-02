# 抽象

在了解`sequence`之前，我们可以先了解下`abstraction`，`abstraction`的概念在很多语言里面都有，譬如`Go`，`interface`就是`abstraction`:

```golang
type IA interface {
    DoFunc()
}

type A struct {}

func (a *A) DoFunc() {

}
```
在上面这个例子中，`struct A`实现了`DoFunc`函数，我们就可以认为，`A`实现了`IA`。

## Sequence Abstraction

`Clojure`也提供了`abstraction`的概念，这里我们主要来了解下`sequence abstraction`。

在`Clojur`e里面，如果这些`core` `sequence` `function` `first`，`rest`和`cons`能够用于某个`data structure`，我们就可以认为这个`data structure`实现了`sequence abstraction`，就能被相关的`sequence function`进行操作，譬如`map`，`reduce`等。

## First

`first`返回`collection`里面的第一个元素，譬如：

```lisp
user=> (first [1 2 3])
1
user=> (first '(1 2 3))
1
user=> (first #{1 2 3})
1
user=> (first {:a 1 :b 2})
[:a 1]
```

## Rest

`rest`返回`collection`里面，第一个元素后面的`sequence`，譬如：

```lisp
user=> (rest [1 2 3])
(2 3)
user=> (rest [1])
()
user=> (rest '(1 2 3))
(2 3)
user=> (rest #{1 2 3})
(3 2)
user=> (rest {:a 1 :b 2})
([:b 2])
```

## Cons

`Cons`则是将一个元素添加到`collection`的开头，譬如：

```lisp
user=> (cons 1 [1 2 3])
(1 1 2 3)
user=> (cons 1 '(1 2 3))
(1 1 2 3)
user=> (cons 1 #{1 2 3})
(1 1 3 2)
user=> (cons 1 {:a 1 :b 2})
(1 [:a 1] [:b 2])
user=> (cons {:c 3} {:a 1 :b 2})
({:c 3} [:a 1] [:b 2])
```

从上面的例子可以看出，`Clojure`自身的`vector`，`list`等都实现了`sequence abstraction`，所以他们也能够被一些`sequence function`调用：

```lisp
user=> (defn say [name] (str "hello " name))
#'user/say
user=> (map say [1 2])
("hello 1" "hello 2")
user=> (map say '(1 2))
("hello 1" "hello 2")
user=> (map say #{1 2})
("hello 1" "hello 2")
user=> (map say {:a 1 :b 2})
("hello [:a 1]" "hello [:b 2]")
user=> (map #(say (second %)) {:a 1 :b 2})
("hello 1" "hello 2")
```

## Collection Abstraction

跟`sequence abstraction`类似，`Clojure`里面的`core data structure`，譬如`vector`，`list`等，都实现了`collection abstraction`。

`Collection abstraction`通常是用于处理整个`data structure`的，譬如:

```lisp
user=> (count [1 2 3])
3
user=> (empty? [])
true
user=> (every? #(< % 3) [1 2 3])
false
user=> (every? #(< % 4) [1 2 3])
true
```

## into

一个重要的`collection function`就是`into`，`sequence function`通常会返回一个`seq`，而`into`会将返回的`seq`转换成原始的`data structure`，譬如:

```lisp
user=> (into [] [1 2 3])
[1 2 3]
user=> (into [] (map inc [1 2 3]))
[2 3 4]
```