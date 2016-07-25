# Clojure使用Java方法

Clojure有一个很强大的功能，就是你可以使用Lisp语言风格无缝的调用java api(java interop)。这无疑是如虎添翼。

## 调用一个Java对象的方法

语法：

```lisp
.method-name object-value args ...
```

例如：

```lisp
(.toUpperCase "hello,clojure")
"HELLO,CLOJURE"

(.indexOf "hello,clojure" "j")
9

(. "hello,clojure" indexOf "j")
9

(.. System (getProperties)(get "os.name"))
"Mac OS X"

(.substring "hello,clojure" 6)
"clojure"
```

其实都是使用`dot operator`

```lisp
（. object-expr-ot-classname-symbol method-or-member-symob optional-args*）
```

## 设置一个Java对象的共有成员变量

语法如下：

```lisp
(set! (.member-variable object-variable) new-value)
```

比如：

```lisp
(let [pt (Point. 0 10)]
               #_=> (set! (.y pt) 100)
               #_=> (.y pt))
100
```

## 调用静态共有成员变量/函数

用/分开类和成员

```lisp
(java.lang.Math/abs -3)
3

(java.lang.Math/pow 2 10)
1024.0
```

## 创建一个对象

两种方法

```lisp
(Class-name. arg1 arg2 ...)
```
注意，类名后面有个.(a dot)这个最常用;

还有就是

```lisp
(new Class-name arg1 arg2 ...)
```

new的后面类名,构造函数里面的参数不需要使用括号()

```lisp
user=> (String. "Clojure!")
"Clojure!"
user=> (new String("Clojure"))

ClassCastException java.lang.String cannot be cast to clojure.lang.IFn  user/eval1266 (form-init7793881567968869401.clj:1)
user=> (new String "Clojure")
"Clojure"
```

## 连续调用一个对象的方法

将多个操作（多行代码）通过点号"."链接在一起成为一句代码,我们称之为"链式编程风格"。 链式代码通常要求操作有返回值， 但对于很多操作大都是void型，什么也不返回，这样就很难链起来了.

我们在Clojure中这样来写

```lisp
user=> (doto (java.util.Stack.)
  #_=> (.push "Hello!")
  #_=> (.push "Clojure.")
  #_=> )
["Hello!" "Clojure."]

com.lightsword=> (doto (java.util.HashMap.)(.put "k" 1)(.put "v" 2))
{"v" 2, "k" 1}
```

## import java package

语法

```lisp
(import [package.name1 ClassName1 ClassName2]  
        [package.name2 ClassName3 ClassName4])
```

实例

```lisp
user=> (import [java.util Date Stack]
  #_=> [java.net Proxy URI])
java.net.URI
user=> (Date.)
#inst "2016-06-28T15:19:05.923-00:00"
```

放入namespace中是推荐的写法，也就是前面加上ns:

```lisp
user=> (ns com.lightsword
  #_=> (:import [java.util Date Stack]
  #_=>  [java.net Proxy URI])
  #_=> )
nil
com.lightsword=> (Date.)
#inst "2016-06-28T15:21:43.048-00:00"
```

## 访问一个类的内部类

用如下形式：

```lisp
package.class-name$inner-class
```