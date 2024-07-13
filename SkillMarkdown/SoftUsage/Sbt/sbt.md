## 如何编写sbt

## sbt的命令

**按照惯例，我们将使用sbt:…>或>提示符表示我们在SBT交互shell中。**

终端直接运行`sbt`这进入`sbt的交互模式`，如果是运行`sbt 命令1 命令2`就类似进入sbt的交互模式，在其中执行：`命令1`，`命令2`，然后退出`sbt的交互模式`

eg:

你也可以用批处理模式来运行 sbt，可以以空格为分隔符指定参数。对于接受参数的 sbt 命令，将命令和参数用引号引起来一起传给 sbt。例如：

```shell
$ sbt clean compile "testOnly TestA TestB "
```

等同于

在这个例子中，`testOnly` 有两个参数 `TestA` 和 `TestB`。这个命令会按顺序执行（`clean`， `compile`， 然后 `testOnly`）。

```shell
$ sbt
sbt:hello> clean
sbt:hello> compile 
sbt:hello> "testOnly TestA TestB"
sbt:hello> exit
```

#### 一些常见 sbt命令

##### clean

 删除所有生成的文件 （在 target 目录下）

```
sbt clean
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531211923.png)

##### compile

编译源文件（当前目录下或在 src/main/scala 或 src/main/java 目录下）。

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531212207.png)

#### 编译和运行所有测试-----test

```shell
sbt test
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240621194204.png)

编译和运行所有测试。

如果

如果我们写一个测试，没有遵循`ChiselTest`的约定且包含了一个`main`函数，但是又把它放在了源代码树的`test`文件夹下，我们可以这么执行：

```shell
sbt "test:runMain mypacket.MyMainTest"
```

#### console

进入到一个包含所有编译的文件和所有依赖的 classpath 的 Scala 解析器。输入 :quit， Ctrl+D （Unix），或者 Ctrl+Z （Windows） 返回到 sbt。

#### `run<参数>*`

在和 sbt 所处的同一个虚拟机上执行项目的 main class。

```shell
sbt run
```

在和 sbt 所处的同一个虚拟机上执行项目的 main class。

#### package

创建一个 jar 包其中包含 `src/main/resources` 和编译 `src/main/scala` 或 `src/main/java` 目录的 `class` 文件

#### help <command>

显示指定命令的帮助信息，如果没有指定命令将显示所有的命名的摘要信息。

#### reload

重新加载配置文件(build.sbt， project/.scala 和 project/.sbt 文件)， 当修改配置文件的时候需要执行

#### `sbt new` zzq/gyy

从github仓库中拉去项目，一般这个项目都是一个模板的空白项目，

eg：

```shell
$ sbt new scala/scala-seed.g8
$ sbt new playframework/play-scala-seed.g8
$ sbt new akka/akka-http-quickstart-scala.g8
$ sbt new http4s/http4s.g8
$ sbt new holdenk/sparkProjectTemplate.g8
```

#### 运行程序--使用`sbt run`

进入项目后，在终端中执行`sbt run`就可以运行项目，可以不一定要进入shell模式才能运行项目

```shell
$ sbt run
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531200932.png)

会自动查找当前目录的文件

这个run命令会在当前目录中创建出一个`target`目录和`project`目录

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531205125.png)

#### sbt直接指定允许APP对象

如果有多个APP对象（即主函数）则可以指定APP对象执行

```shell
sbt "runMain mypacket.MyObject"
sbt "runMain package_name.app_module_name"
```

```scala
package mypacket
class Myobject extends APP{

}
```

#### #### 进入sbt的shell模式

在终端上输入`sbt`即可

```shell
$ sbt
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531172616.png)

1. 这里创建了一个空目录，

2. 然后创建一个空的build.sbt的文件，

3. 然后进入sbt的交互模式（即shell模式）

#### 退出shell模式

输入exit即可,或者使用ctrl+D（Unitx）或者ctrl+Z(windows)

```shell
sbt:foo-build> exit
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531172856.png)

#### 在shell模式中：编译一个项目

```shell
$ sbt
sbt:foo-build> compile
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531181653.png)

#### `~`前缀

在`compile`命令，或者其他名前加上`~`,当一个源文件发生变化后，这个命令都会被再次执行

```shell
sbt:foo-build> ~compile
[success] Total time: 0 s, completed 28 Jul 2023, 13:32:35
[info] 1. Monitoring source files for foo-build/compile...
[info]    Press <enter> to interrupt or '?' for more options.
```

这里提示：.监视foo-build/compile的源文件，应该是表示这个源文件发生变化就会自动执行compile这个命令

#### 显示任务的描述信息

```shell
sbt:foo-build> help run
Runs a main class, passing along arguments provided on the command line.
```

#### 在shell模式中：如何运行项目---使用run命令

再终端进入一个项目，让后使用sbt进入sbt的shell交互模式，然后运行

`run`命令

```shell
sbt:foo-build> run
[info] running example.Hello
Hello
[success] Total time: 0 s, completed 28 Jul 2023, 13:40:31
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531183151.png)

#### 从sbt的shell模式中：设置`ThisBuild`和`scalaVersion`版本

```shell
sbt:foo-build> set ThisBuild / scalaVersion := "2.13.12"
[info] Defining ThisBuild / scalaVersion
[info] The new value will be used by Compile / bspBuildTarget, Compile / dependencyTreeCrossProjectId and 50 others.
[info]  Run `last` for details.
[info] Reapplying settings...
[info] set current project to foo-build (in build file:/tmp/foo-build/)
```

#### 在sbt的shell模式中：查看`scalaVerison`

```scala
sbt:foo-build> scalaVersion
[info] 2.13.12
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240531183423.png)

#### 将sbt中shell模式中：设置的参数保存到`build.sbt`中去

```shell
sbt:foo-build> session save
[info] Reapplying settings...
[info] set current project to foo-build (in build file:/tmp/foo-build/)
[warn] build source files have changed
[warn] modified files:
[warn]   /tmp/foo-build/build.sbt
[warn] Apply these changes by running `reload`.
[warn] Automatically reload the build when source changes are detected by setting `Global / onChangedBuildSource := ReloadOnSourceChanges`.
[warn] Disable this warning by setting `Global / onChangedBuildSource := IgnoreSourceChanges`.
```

## sbt默认的项目结构

### project_name(项目名称)

#### project

这里写的是一些 SBT 特定的配置文件

这里主要是一些以sbt为后缀的文件eg:`a.sbt`，`b.sbt`

#### build.sbt

这里描述了整个项目的结构

#### src

##### main

这个文件包含一个项目的实现的代码

##### resources

要包含在主jar中的文件>

###### scala

主要Scala源代码>

###### scala-2.12

###### java

主要的Java源代码>

##### test

这个文件包含着测试项目的测试代码

###### resource

要包含在测试jar中的文件

###### scala

测试scala的源代码

###### scala-2.12

测试scala 2.12 的源代码

###### java

测试java的源代码

#### sbt的自动执行过程

比如这样一个hello项目

在终端进入hello项目

```shell
$ cd hello
```

执行`sbt`命令

```shell
$ sbt
```

然后再sbt的shell模式中运行run

这时候run就会会进行一些默认搜索

1. 会在项目的根目录下搜索源文件（即在hello目录下搜索所有.scala文件）

2. 会在src/main/scala 或 src/main/java 中搜索源文件（即在hello/src/main/scala和hello/src/main/java中搜索所有.scala文件）

3. `src/test/scala` 或 `src/test/java` 中的测试文件

4. `src/main/resources` 或 `src/test/resources` 中的数据文件

5. `lib` 中的 jar 文件
