# 这里介绍chise的api即库函数

api的链接：[chisel 6.3.0 (chisel-lang.org)](https://www.chisel-lang.org/api/latest/index.html)

[API Docs | Chisel (chisel-lang.org)](https://www.chisel-lang.org/api)

#### 目前的6.30有5类包

+ chisel3

+ circt

+ firrtl

+ logger

+ svsim
  
  ![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240521144521.png)

![](C:\Users\zzq\AppData\Roaming\marktext\images\2024-05-21-14-49-03-image.png)

左侧是一些功能介绍，只能说相当简陋

右侧列出当时包`chisel3`所有的子包和类，实例对象，eg:

`aop`就是chisel下的子包，`Mux`就是`chisel3`下的一个对象，`Bundle`就是该包下的一个类，

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240521145134.png)

点击Mux.scala可以查看Mux的scala源码

#### Mem

组合/异步读、顺序/同步写内存。

写操作在请求后的上升时钟沿上生效。读取是组合的(请求将在同一周期返回数据)。写后读危险不是问题。

# `chisel3.util`

#### Reverse

将一般变量或者常量的位进行逆转，用于小端转大端

```scala
Reverse("b1101".U)  // equivalent to "b1011".U
Reverse("b1101".U(8.W))  // equivalent to "b10110000".U
Reverse(myUIntWire)  // 变量的方式进行动态逆转，dynamic reverse
```
