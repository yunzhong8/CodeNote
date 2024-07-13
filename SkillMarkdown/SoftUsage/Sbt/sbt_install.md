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
