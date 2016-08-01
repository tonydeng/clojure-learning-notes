# Leiningen

在`clojure`，很多都是使用[Leiningen](https://github.com/technomancy/leiningen)来进行工程的构建，包括`jepsen`，自然我也会使用`leiningen`了。因为`leiningen`的命令行是`lein`，所以后续我们都会以`lein`简写。

在`mac`下面，可以直接使用`brew install leiningen`来安装`lein`，当然`lein`官方也提供了其他平台的安装方式，这里不再累述。

首先，我们建立一个工程，`lein new clojure-stuff`，进入`clojure-stuff`目录，我们可以看到项目的目录结构：

```lisp
➜  clojure-stuff git:(master) ✗ find .
.
./.gitignore
./.hgignore
./CHANGELOG.md
./doc
./doc/intro.md
./LICENSE
./project.clj
./README.md
./resources
./src
./src/clojure_stuff
./src/clojure_stuff/core.clj
./test
./test/clojure_stuff
./test/clojure_stuff/core_test.clj
```

首先我们关注的就是`project.clj`文件，我们在这个文件里面定义整个项目的一些基本属性:

```lisp
(defproject clojure-stuff "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :url "http://example.com/FIXME"
  :license {:name "Eclipse Public License"
            :url "http://www.eclipse.org/legal/epl-v10.html"}
  :dependencies [[org.clojure/clojure "1.7.0"]])
```

"`0.1.0-SNAPSHOT`"是该项目现在的版本情况，在`clojure`里面，如果一个项目版本以“`-SNAPSHOT`”结尾，通常表明改项目还处于开发阶段，还没有正式`release`。

`description`是该项目的简要描述，`url`是可选的网址，`license`则是该项目使用的License。这里我们重点关注一下`dependencies`，如果我们的项目需要依赖其他的工程，就需要在`dependencies`里面设置，当然，`clojure`的项目一定会依赖一个`clojure`版本的，这里我们使用的是`1.7.0`。

`src`目录下面就是我们项目文件了，我们更改`clojure_stuff/core.clj`文件:

```lisp
(ns clojure-stuff.core)

(defn my-plus
  "I don't do a whole lot."
  [a b]
  (+ a b))
```

`(ns clojure-stuff.core)`声明了一个`namespace`，然后我们定义了一个`my-plus`函数，简单进行两个数相加，通常，如果我们定义了一个函数，最好在`test`里面进行测试，所以我们在`test/clojure_stuff/core_test.clj`里面编写如下代码：

```lisp
(ns clojure-stuff.core-test
  (:require [clojure.test :refer :all]
            [clojure-stuff.core :refer :all]))

(deftest my-plus-test
  (testing "Test my plus."
    (is (= (my-plus 1 1) 2))))
```

然后执行`lein test`，输出：

```bash
➜  clojure-stuff git:(master) ✗ lein test

lein test clojure-stuff.core-test

Ran 1 tests containing 1 assertions.
0 failures, 0 errors.
```

我们可以改动`test`，让其报错:

```lisp
(deftest my-plus-test
  (testing "Test my plus."
    (is (= (my-plus 1 2) 2))))
```

再次运行`lein test`，我们会得到错误的信息：

```lisp
FAIL in (my-plus-test) (core_test.clj:7)
Test my plus.
expected: (= (my-plus 1 2) 2)
  actual: (not (= 3 2))

Ran 1 tests containing 1 assertions.
1 failures, 0 errors.
```

如果项目需要能够直接运行，我们需要编写`main`函数，在`src/clojure_stuff/core.clj`里面，我们编写：

```lisp
(defn -main 
  [& args]
  (println "Hello Clojure"))
```

同时在`project.clj`里面设置：

```lisp
:main ^:skip-aot clojure-stuff.core
```

然后执行`lein run`，得到：

```bash
➜  clojure-stuff git:(master) ✗ lein run
Hello Clojure
```

我们可以使用`lein unberjar`将项目生成一个`jar`供其他工程使用：

```bash
➜  clojure-stuff git:(master) ✗ lein uberjar
Warning: The Main-Class specified does not exist within the jar. It may not be executable as expected. A gen-class directive may be missing in the namespace which contains the main method.
Created $(PATH)/src/clojure-stuff/target/clojure-stuff-0.1.0-SNAPSHOT.jar
Created $(PATH)/src/clojure-stuff/target/clojure-stuff-0.1.0-SNAPSHOT-standalone.jar
```

可以看到，用`lein`来进行`clojure`的项目开发是非常方便的，这也就是为什么很多`clojure`项目采用它的原因，本文仅仅是简单介绍，详细的可以参考`lein`的文档。

