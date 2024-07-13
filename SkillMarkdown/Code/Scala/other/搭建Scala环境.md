# 搭建环境

真的是被网上的无良教程给骗了，学生终究只是学生，写的东西永远是不完备的，就是写跟个狗屎，且没有结构，也不介绍为什么？？

[Chisel简介 杨烨  第三期“一生一芯”计划 - P4，环境安装视频] (https://www.bilibili.com/video/BV1p14y1W7Xn/?share_source=copy_web)

### 安装Scala环境

#### 1.安装jdk

```shell
sudo apt-get install scala
```

#### 2.安装scala

```shell
sudo apt-get install scala
```

#### 3.查看scala

```shell
scala -version
```

#### 4.进入scala的shell

```shell
scala
```

进入scala中的shell就好像在控制台上运行python一样，进入python

#### 5.编写scala测试程序

1.创建一个`test.scala`的文件，写入内容：

```
object HelloWorld {
  def main(args: Array[String]): Unit = {
    println("Hello, world!")
  }
}
```

2. 运行该文件
   
   ```shell
   scala test.scala
   ```

这里的`scala test.scala`就是正常进入到终端让后输入这个命令，不是要先使用`scala`进入到scala控制台

 ![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240526213826.png)

# 安装sdb

1. 安装JDK、git、make和gtkwave等基本环境
   
   ```
   sudo apt-get install openjdk-8-jdk 
   sudo apt-get install git
   sudo apt-get install make
   sudo apt-get install gtkwave
   ```
   
   上面命令可以写成一句
   
   ```shell
   sudo apt install openjdk-8-jdk git make gtkwave
   ```

2. 安装sbt
   
   ```shell
   echo "deb https://repo.scala-sbt.org/scalasbt/debian all main" | sudo tee /etc/apt/sources.list.d/sbt.list
   echo "deb https://repo.scala-sbt.org/scalasbt/debian /" | sudo tee /etc/apt/sources.list.d/sbt_old.list
   curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | sudo apt-key add
   sudo apt-get update
   sudo apt-get install sbt
   ```

这个sbt只是一个引导程序，需要后续运行`sbt run`才能正常安装

3. 安装sbt
   
   3.1 编写一个测试文件
   
   创建一个名称为`test.scala`的文件，写入内容
   
   ```scala
   object HelloScala extends App{
       println("Hello Scala")
   }
   ```

    3.2 使用`sdt run`运行上面的代码

```shell
sdt run
```

如果sdt软件没有下载，会进行下载sbt这个软件，让后默认以当前文件夹作为项目目录然后运行代码

## 如何安装mill

1. j进入nemu的init.sh文件中
   
   ![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240515120718.png)
   
   这里定义了如何初始化chisel版本npc，本质上就是删除当前的npc，让后到`OpenXiangShan/chisel-playgroud`去拉取项目到本地，这个项目就是一个chisel的模板工程而已
   
   2.
   
   先安装mill，然后运行
   
   3. 安装mill
   
   4. 先安装millw这个安装脚本
   
   5. 执行安装脚本

# 教学视频

【尚硅谷大数据技术之Scala入门到精通教程（小白快速上手scala）】 https://www.bilibili.com/video/BV1Xh411S7bP/?share_source=copy_web

# 官方网站

[The Scala Programming Language (scala-lang.org)](https://www.scala-lang.org/)

总算看民百这里面的什么了：

脚本mill为什么会取名mill，因为它不是mill的下载的引导程序，而是在mill软件上的上一层封装，给它传入命令，它会检查mill的开源仓库的发行版是否安装了，

if没有安装，则创建固定的安装目录，让后使用curl命令从relse的路径中下载版本，你个它传入操作的命令，脚本会执行发行版程序，并将命令传给它，同时在附带一些额外的参数

太秒了，我使用mill官方提供的一些example的项目，结果运行就报错

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240516172112.png)

目前来看这个报错信息是因为项目使用到了一些依赖，然后自动下载，下载错误了，也就是我的wsl的网络有问题，md，好烦啊，如何给我这个sb的wsl配置网络，我这个vpn如何让这个sb用上，这个sb拉去github的仓库的数据也会tmd报错，这个sb的wsl md

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240516200456.png)

我把wsl的网络配置好，运行的时候还是报错，md，这个报错的输出我根本无法理解，尼玛的sb老外，错误写也就你这些sb老外看的懂，报错提示是说还需要传入参数

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240516200739.png)
