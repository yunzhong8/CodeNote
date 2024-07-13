本处记录了我在使用chisel中犯的各种错误和产生的各种疑惑。

### Bundle

#### Bundle是什么关键词-----将变量打包成一个整体，将多个变量捆成一个

这个是有些类似我在verilog中构建的bus

```
var bus = Bundle(
    val = 
)
```

给bundle定义信号，表示这些是一个整体，就是将这个写信号打包成一个新名字B然后使用`B.sign_name`对其进行访问

如何把这些信号变成一个可以重复利用的，就是将创建一个类selfBundle这个来继承Bundle这个类，那样这个

那么使用selfBUndle创建的对象就一定具备你当时定义selfBundle时候的信号

```scala
class Channel() extends Bundle{
    val data = UInt(32.W)
    val valid = Bool()
}

//将一个好多种信号捆绑在一块
val ch = Wire(new Channel() )
ch.data  := 123.U
ch.valid := true.B


val b =ch.valid
```

转成verilog

```verilog
`define BusWidth DataLen+BoolLen
module xx (
    output wire [BusWidth] ch
);
    assign ch[`BoolLen+`DataLen:`BoolLen-1]
    assign ch[`BoolLen-1:0] = true.B;
endmodule
```

#### Bundle 可以将打包的变量类型

##### 可以将固定位宽的变量进行捆绑

```
class Packet extends Bundle {
  val header = UInt(16.W)
  val addr   = UInt(16.W)
  val data   = UInt(32.W)
}
class MyModule extends Module {
  val inPacket = IO(Input(new Packet))
  val outPacket = IO(Output(new Packet))
  val reg = Reg(new Packet)
  reg := inPacket
  outPacket := reg
}
```

##### 可以将`input`，`output`接口信号进行捆绑

```
class MyModule extends Module {
  val io = IO(new Bundle {
    val in = Input(UInt(64.W))
    val out = Output(SInt(128.W))
  })
}
```

#### `:=` 是---------表示信号传递或者信号连接

`接受信号方（consumer）:= 发生信息方（producer)

这个表示信号传入，双方不必须是：模块，也可以是端口（port),或者是信号

```verilog
wire[4:0] alu_op;
assign alu_op = signal[12:8] ;
```

改写成

```scala
aluop := signal[12:8]
```

#### `:=`和`=`的区别是什么

在chisel中`=`用于定义变量的时候比较多，告诉scala，要在这是一个变量要创建，并给定某某初始值

`:=`用于两个变量传递信号，这个用于描述verilog如何连线的即表示要使用`assign`来生成

eg:

```scala
val x = a *b 
//因为x是val类型，所以x不能被重新赋值，
//x代表a*b这个组合逻辑表达式的输出，
//所以x可以传递给别人带上不能在重新指定它代表的组合逻辑，再次指定=就是重新指定他的代表的表达式
x = b & c //这行代码会报错
y := x //可以把x的信号传递给
```

而且使用`=`的逻辑也不对，verilog不存在一个变量可以被assign语句让两个不同表达式斗个给它赋值,等同生成如下代码

```verilog
wire a,b;
wire y;
wire x = a *b;
assign x = b&C;//这行是不对
assign y = x;
```

这种代码在verilog上是不对的，所以chisel的全部信号都是val类型的，因为信号指定的表达式是不允许修改的，var类型就可以不断修改信号的输入，这样就导致一个信号被多个输入源赋值，导致冲突

#### Wire()，Reg()是什么函数

#### Input(),Output（）是什么函数

`Input()`和`Output()`是用于设置接口信号的方向的

#### Module是什么类

#### App是什么类

#### 模块和模块间如何快速连线

#### chisel中的所有语句都写在一个Module中的，这一点和Verilog是一致的

#### 对var变量`:=`两次

```scala
var x = Wire(UInt)
x := 1.U
x := 2.U
```

x的取值是最后一次的值即2.U

#### Chisel中的隐式参数

#### Valid函数

在 Chisel 中，`Valid` 是一个用于封装数据的有效性信号的容器。它通常用于表示一个有效的数据元素，其中包含一个布尔值 `valid` 信号和一个实际的数据 `bits`。`Valid` 的主要作用是简化传输数据时的有效性检查，使得硬件模块之间的数据传输更加明确和易于管理。

##### `Valid` 的定义和使用

valid就是将数据打包成一个名叫bit的变量，即如同bundle，让后再添加一个valid的bool类型的数据，再将两者打包成一个整体，这里不表明打包的数据的是输入还是输出类型

###### 定义

`Valid` 是一个参数化的类，它接受一个类型参数 `T`，表示实际数据的类型。

```scala
case class Valid[+T](bits: T) {
  val valid = Bool()
}
```

###### 作用

- **`valid` 信号**：一个布尔值，表示当前数据是否有效。如果 `valid` 为 `true`，则 `bits` 中的数据是有效的。
- **`bits` 数据**：实际的数据负载，类型为 `T`。

##### 示例

下面是一个简单的例子，展示如何使用 `Valid` 封装一个数据负载：

```scala
import chisel3._
import chisel3.util._

class MyModule extends Module {
  val io = IO(new Bundle {
    val in = Input(Valid(UInt(32.W)))
    val out = Output(Valid(UInt(32.W)))
  })

  io.out.valid := io.in.valid
  io.out.bits := io.in.bits
}
```

在这个例子中，`MyModule` 模块接收一个 `Valid` 类型的输入信号 `in`，并将其传递到输出信号 `out`。`Valid` 信号确保在传输过程中能够明确表示数据的有效性。

##### 结合上下文解释

回到你的代码：

```scala
object WritePort {
  def apply(enq: DecoupledIO[ExeUnitResp], addrWidth: Int, dataWidth: Int, rtype: UInt)
           (implicit p: Parameters): Valid[RegisterFileWritePort] = {
    val wport = Wire(Valid(new RegisterFileWritePort(addrWidth, dataWidth)))

    wport.valid     := enq.valid && enq.bits.uop.dst_rtype === rtype
    wport.bits.addr := enq.bits.uop.pdst
    wport.bits.data := enq.bits.data
    enq.ready       := true.B
    wport
  }
}
```

##### 具体解释

1. **创建一个 `Valid` 信号**
   
   ```scala
   val wport = Wire(Valid(new RegisterFileWritePort(addrWidth, dataWidth)))
   ```
   
    这里使用 `Wire` 创建了一个 `Valid` 类型的信号 `wport`，其中包含一个 `RegisterFileWritePort` 类型的数据。

2. **设置 `wport` 的有效性信号**
   
   ```scala
   wport.valid := enq.valid && enq.bits.uop.dst_rtype === rtype
   ```
   
    将 `wport` 的 `valid` 信号设置为当 `enq` 有效且 `uop.dst_rtype` 与 `rtype` 匹配时为 `true`。

3. **设置 `wport` 的数据字段**
   
   ```scala
   wport.bits.addr := enq.bits.uop.pdst
   wport.bits.data := enq.bits.data
   ```
   
    从 `enq` 中提取地址和数据，设置到 `wport.bits` 中。

4. **设置 `enq.ready`**
   
   ```scala
   enq.ready := true.B
   ```
   
    表示可以接受新的输入。

5. **返回 `wport`**
   
   ```scala
   wport
   ```
   
    返回封装好的 `Valid[RegisterFileWritePort]` 信号。

##### 总结

`Valid` 类型在 Chisel 中用于封装数据及其有效性信号，使得数据传输更加规范和明确。在你的代码中，`Valid` 被用来封装 `RegisterFileWritePort` 类型的数据，并且通过设置 `valid` 信号来指示数据何时有效。这种模式在硬件设计中非常常见，用于确保数据在传输过程中始终是有效且可用的。

# Vecs类

Vecs可以创建一个接口变量，这个接口变量，由n个相同类型的接口信号构成

```scala
import chisel3._
class MyFloat extends Bundle{
    val sign        = Bool()
    val exponent    = UInt(8.W)
    val significand = UInt(23.W)
}

//创建了一个接口类型
//5个类型都是SInt(23.W)的类型接口组
//1个类型是Bool的接口
//1个类型是MyFloat的接口
class BigBundle extends Bundle{

    //5个类型是23bit的有符号整型构成的Vector
    val myVec = Vec(5 , SInt(23.W) )

    val flag = Bool()

    val f = new MyFloat

}
```

#### ` Vec类`和`继承Bundle的接口的类`的区别

`Vec`用于创建n个相同类型的接口信号构成的实例，Vec不用使用new，因为：Vec类由伴生实例，伴生实例中编写apply方法

`继承Bundle的接口的类`可以构建的实例中的中的成员的类型是可以不同，所以使用该类去创建一个成员是不同类型的接口信号的实例，如果没有定义apply方法，则要使用new去创建实例

#### Vec类的使用----创建n个同类型接口

```scala
val Vec_object1 = Vec(元素个数，元素类型)
```

这里的元素类型可以是你自己创建的接口类

eg:

```scala
val  Vec_object1 = Vec(3,MyFloat)
```

#### Vec的map成员函数

#### Flipped类 --反转IO接口类

Flipped()这个函数使用用于反转一个Bundle/Record类型的全部成员，这对于构建相互连接的双向接口(eg:Decoupled)非常有用。请参见下面的示例。

```scala
class AB_Bundle extends Bundle{
    val a = Input ( Bool() )
    val b = Output( Bool() )
}

class MyFlippedModule extends RawModule{
    // bundle的普通的实例化
    //'a'是一个输入信号，b是一个输出信号
    val normalBundle = IO(new AB_Bundle)
    normalBundle.b := normalBundle.a

    //翻转Bundle中所有信号的输入输出方向
    //现在'a'是输出，'b'是输入
    val flippedBundle = IO( Flipped(new AA_Bundle) )
    //Fillpped(new AABundle)就等同创建了一个：
    //class AB_Bundle extends Bundle{
    //    val a = Output( Bool() )
    //    val b = Input ( Bool() )
    //}
    filppedBundle.a := flippedBundle.b
}
```

生成verilog代码

```verilog
module MyFlippedModule(
    input  normalBundle_a,
    output normalBundle_b,
    output flippedBundle_a,
    input flippedBundle_b
)
    assign normalBundle_b  = normalBundle_a ;
    assign flippedBundle_a = flippedBundle_b;
```

#### chisel中的保存输入输出接口的变量可以不止一个

```scala
class zzqModule extends Module{
    val reg_io = IO (new regBundle)
    val mem_io = IO (new memBundle)
}
```

#### md为什么这么烦人------ 版本不匹配

`scala`和`chisel`这种新垃圾语言，真他妈烦啊，动不动就是版本迭代的很厉害，很容易出现版本不匹配的情况，我为什么要用这种垃圾语言啊，md，sb开芯园，真的tmd很烦人啊，

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240622114117.png)

这个错误是说版本不匹配，md我都是按照教程写的build.sbt让后你他妈更我说版本不匹配，真的tm就是个垃圾语言，还有就是这个垃圾的同一种功能经常换函数名，换尼玛的，垃圾东西

chisel-bootcamp也是一个垃圾，tm的，只给test，和src的代码，tmd不给build的代码，md我完全不知道我的build应该如何编写，tmd

#### 如何导入其他包-----import 包名

```scala
import zzq_create_package 


class xx_d{

}
```

#### 设置文件归属那个`package 包名`

```scala
package zzq_create_package
```

#### chisel:`Fill(15,inst) `   等同 verilog:`{15{inst}}`

#### verilog用到常数`15'h5`则chisel`5.U(15.W)`

#### 如何在Cat中拼接常数

```verilog
assign veriable = {inst[12:0],5'd0}
```

等同chisel

```chisel
val veriable = Cat( inst(12:0),0.U(5.W) )
```

#### `def`函数返回忘记加`=`

错误写法

```scala
def functionName(pc :UInt) :UInt{

}
```

正确写法

```scala
def functionName(pc :UInt) :UInt={

}
```

#### `chisel`中`module`中的最后一个参数不用加`,`

#### chisel `when`语法忘记了

基础判断语句when, else when, 和 otherwise
Chisel的基础判断语句为when, else when, and otherwise, 具体用例：

```scala
when(someBooleanCondition) {
 // 当someBooleanCondition判断语句为真，执行该句
}.elsewhen(someOtherBooleanCondition) {
 // things to do on this condition
}.otherwise {
 // 当以上的判断语句都为假，执行该句
}
//注意这里的 < . >
```

注意:

`w h e n` → `else when` →` otherwise  使用顺序不能打乱。

可以对等C的 i f → e l s e i f → e l s e if\rightarrow elseif \rightarrow elseif→elseif→else , 中间想用多少个elsewhen都无所谓。

与Scala中的if不同的是, when, elsewhen 和 otherwise语句并不返回值。因此以下使用是非法的：

```scala
val result = when(squareIt) { x * x }.otherwise { x }
```

实例

```scala
// Max3 返回三个值中的最大值
class Max3 extends Module {
 val io = IO(new Bundle {
 val in1 = Input(UInt(16.W))
 val in2 = Input(UInt(16.W))
 val in3 = Input(UInt(16.W))
 val out = Output(UInt(16.W))
 })

when(io.in1 >= io.in2 && io.in1 >= io.in3) {
 io.out := io.in1  
 }.elsewhen(io.in2 >= io.in3) {
 io.out := io.in2 
}.otherwise {
 io.out := io.in3
 }
}

// Test Max3
test(new Max3) { c =>
 // verify that the max of the three inputs is correct
 c.io.in1.poke(6.U)
 c.io.in2.poke(4.U)
 c.io.in3.poke(2.U)
 c.io.out.expect(6.U) // input 1 should be biggest
 c.io.in2.poke(7.U)
 c.io.out.expect(7.U) // now input 2 is
 c.io.in3.poke(11.U)
 c.io.out.expect(11.U) // and now input 3
 c.io.in3.poke(3.U)
 c.io.out.expect(7.U) // show that decreasing an input works as well
 c.io.in1.poke(9.U)
 c.io.in2.poke(9.U)
 c.io.in3.poke(6.U)
 c.io.out.expect(9.U) // still get max with tie
}

println("SUCCESS!!") // Scala Code: if we get here, our tests passed!
```

       原文链接：https://blog.csdn.net/weixin_42119585/article/details/123520052

#### `chisel`中源码（你编写项目的代码）的文件要放在`src/main`中，如果放在`src`就会报错

```bash
.
├── main
│   ├── DecoupledGCD.scala
│   ├── Elaborate.scala
│   ├── GCD.scala
│   └── module
└── test
    └── GCDSpec.scala

3 directories, 4 files
```

错误放工程的方式如下：

```shell
.
├── main
│   ├── DecoupledGCD.scala
│   ├── Elaborate.scala
│   └── GCD.scala
├── module
│   └── alu.scala
└── test
    └── GCDSpec.scala
```

vscode无法检测到这个包，编译器也无法检测到

1. ![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240624095154.png)

#### chisel的所有io变量的output变量都必须赋值不然报错

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240624144108.png)

### `def` vs `val` 的对比

总结：

+ 使用def是为了让编译更快些，但是技术不好容易写错，

+ 使用val定义的变量会占内存，导致编译的时候更加占内存

在Chisel中，`def`用于定义一个方法或一个惰性求值的值，而`val`用于定义一个立即求值的值。使用`def`和`val`的选择取决于具体的需求和设计意图。

在您的代码中，`def FN_ADD = 0.U`的意义是定义一个方法，它返回一个32位宽度的无符号整数常量`0`。这里使用`def`而不是`val`的主要原因可能是为了保持灵活性和延迟求值。在某些情况下，使用`def`可能更适合，因为它可以减少内存占用，并且在调用时才会进行求值。

1. **`def`（方法/惰性求值）**:
   
   - 每次调用时重新计算。
   
   - 延迟求值，只有在实际使用时才计算。
   
   - 在类实例化时不会立即分配内存。
     
     ```scala
     def FN_ADD = 0.U
     ```
     
     这种方式定义的`FN_ADD`在每次调用时都会返回一个新的`0.U`，适用于需要延迟求值或不需要频繁调用的常量。

2. **`val`（立即求值）**:
   
   - 只计算一次，并且在定义时立即计算。
   
   - 在类实例化时分配内存，并且在整个实例生命周期内保持不变。
     
     ```scala
     val FN_ADD = 0.U
     ```
     
     这种方式定义的`FN_ADD`在类实例化时就已经计算并分配好，在以后调用时不会重新计算，适用于需要频繁调用且值不会变化的常量。

### 示例说明

假设我们有如下的Chisel代码：

```scala
class Example extends Module {
  val io = IO(new Bundle {
    val out1 = Output(UInt(32.W))
    val out2 = Output(UInt(32.W))
  })

  def FN_ADD = 0.U
  val FN_SUB = 1.U

  io.out1 := FN_ADD
  io.out2 := FN_SUB
}
```

在这个示例中：

- `FN_ADD`是一个方法，每次调用时返回一个新的`0.U`。这种定义方式较为灵活，可以用于不常用的常量或需要在特定条件下计算的值。
- `FN_SUB`是一个立即求值的常量，在实例化`Example`时就已经计算并分配好，适用于频繁使用且不会改变的常量。

### 总结

在您的`ALUFN`类中，选择使用`def`来定义操作码常量可能是为了保持代码的一致性和简洁性，特别是在定义多个类似常量时。实际上，对于大多数常量来说，使用`val`可能更为合适，因为它们不需要在每次访问时重新计算。除非您有特别的需求来延迟求值，否则建议使用`val`来定义常量：

```scala
val FN_ADD = 0.U
val FN_SUB = 10.U
// 其他操作码常量
```

这样可以提高代码的可读性和性能。

#### 特质中定义具有位宽的变量引用会报错

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240626165553.png)

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240626165616.png)

# IO接口如何写

#### 最基本的IO接口写法

```scala
class Alu extends Module{
    val io = IO( new Bundle{
        val a = Input( UInt(32.W) )
        val b = Input( UInt(32.W) )
        val out = Output( UInt(32.W) )
            })
    io.out := io.a+ io.b
}
```

#### 将IO的定义在外部

##### 捆绑的是输入输出类型 且IO要捆绑多个变量

```scala
import chisel3._
import chisel3.util._

class AluI extends Bundle{
    val a = Input( UInt(32.W) )
    val b = Input( UInt(32.W) )
}
class AluO extends Bundle{
     val out = Output( UInt(32.W) )
}

class Alu extends Module{
    val io = IO( new Bundle{
                  val i = new AluI() 
                  val o  = new AluO()})
      io.o.out := io.i.a + io.i.b
}
```

生成verilog

```verilog
// Generated by CIRCT firtool-1.66.0
module Alu(    // @[src/main/module/alu.scala:13:7]
  input         clock,    // @[src/main/module/alu.scala:13:7]
                reset,    // @[src/main/module/alu.scala:13:7]
  input  [31:0] io_i_a,    // @[src/main/module/alu.scala:14:16]
                io_i_b,    // @[src/main/module/alu.scala:14:16]
  output [31:0] io_o_out    // @[src/main/module/alu.scala:14:16]
);

  assign io_o_out = io_i_a + io_i_b;    // @[src/main/module/alu.scala:13:7, :17:26]
endmodule
```

```scala
import chisel3._
import chisel3.util._

class AluIO extends Bundle{
    val a = Input( UInt(32.W) )
    val b = Input( UInt(32.W) )
    val out = Output( UInt(32.W) )
}


class Alu extends Module{
    val io = IO( new AluIO)
    io.out := io.a + io.b
}
```

生成verilog

```verilog
import chisel3._
import chisel3.util._
// Generated by CIRCT firtool-1.66.0
module Alu(    // @[src/main/module/alu.scala:12:7]
  input         clock,    // @[src/main/module/alu.scala:12:7]
                reset,    // @[src/main/module/alu.scala:12:7]
  input  [31:0] io_a,    // @[src/main/module/alu.scala:13:16]
                io_b,    // @[src/main/module/alu.scala:13:16]
  output [31:0] io_out    // @[src/main/module/alu.scala:13:16]
);

  assign io_out = io_a + io_b;    // @[src/main/module/alu.scala:12:7, :14:20]
endmodule
```

##### 捆绑的是普通类型 且IO要捆绑多个变量

```scala
class AluI extends Bundle{
    val a =  UInt(32.W) 
    val b =  UInt(32.W) 
}
class AluO extends Bundle{
     val out = UInt(32.W) 
}

class Alu extends Module{
    val io = IO( new Bundle{
                  val i = Input(new AluI)
                  val o  = Output(new AluO)
               })
    io.o.out := io.i.a + io.i.b
}
```

生成verilog

```verilog
// Generated by CIRCT firtool-1.66.0
module Alu(    // @[src/main/module/alu.scala:13:7]
  input         clock,    // @[src/main/module/alu.scala:13:7]
                reset,    // @[src/main/module/alu.scala:13:7]
  input  [31:0] io_i_a,    // @[src/main/module/alu.scala:14:16]
                io_i_b,    // @[src/main/module/alu.scala:14:16]
  output [31:0] io_o_out    // @[src/main/module/alu.scala:14:16]
);

  assign io_o_out = io_i_a + io_i_b;    // @[src/main/module/alu.scala:13:7, :18:24]
endmodule
```

```scala
//这种类写法不适合，输入输出混合在一个bundle中
class AluIO extends Bundle{
    val a = UInt(32.W) )
    val b = UInt(32.W) )
    val out = UInt(32.W) )
}

//下面没法写了
class Alu extends Module{
    val io = IO( new AluIO)
    io.out := io.a + io.b
}
```

#### chisel如何调用不同Module

```scala
class Alu (val width :Int) extends ZzqModule {
    val io = IO ( new Bundle{
            val i = new AluI( X_Len )
            val o = new AluO( X_Len )
    } )


    val shamt = io.i.B(4,0).asUInt

    io.o.out := MuxLookup(io.i.alu_fn,io.i.B)(
        Seq(
            AluParamters.Alu_Add_Fn  -> ( io.i.A + io.i.B ),
            AluParamters.Alu_Sub_Fn  -> ( io.i.A - io.i.B ),
            AluParamters.Alu_Sra_Fn  -> ( io.i.A.asSInt >> shamt ).asUInt,
            AluParamters.Alu_Srl_Fn  -> ( io.i.A >> shamt ),
            AluParamters.Alu_Sll_Fn  -> ( io.i.A << shamt ),
            AluParamters.Alu_Slt_Fn  -> ( io.i.A.asSInt < io.i.B.asSInt).asUInt,
            AluParamters.Alu_Sltu_Fn -> ( io.i.A < io.i.B),
            AluParamters.Alu_And_Fn  -> ( io.i.A & io.i.B),
            AluParamters.Alu_Or_Fn   -> ( io.i.A | io.i.B),
            AluParamters.Alu_Xor_Fn  -> ( io.i.A ^ io.i.B),
            AluParamters.Alu_Copy_A  -> io.i.A ,
            AluParamters.Alu_Xxx     -> io.i.A
        )
    )
} 

class Exe extends ZzqModule{
    val io = IO( new ExeIO() )

    // alu 进行运算
    val alu = new Alu( 32) //这种写法是错误的
    alu.io.i := io.pre_to.alu

    //输出
    io.to_next.rf_w.en   := io.pre_to.rf_w.en
    io.to_next.rf_w.addr := io.pre_to.rf_w.addr
    io.to_next.rf_w.data := alu.io.o.out
    // io.to_next.rf_w.data := 0.U(X_Len.W)
}
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240627211018.png)

正确写法：

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240627211104.png)

#### chisel如何将verilog代码生成到不同文件去

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240627220258.png)

```bash
>> make help 
mill -i playground.runMain Elaborate --help
[47/47] playground.runMain 
Usage: circt [options] [<arg>...]

  --help                   prints this usage text
Shell Options
  <arg>...                 optional unbounded args
  -td, --target-dir <directory>
                           Work directory (default: '.')
  -faf, --annotation-file <file>
                           An input annotation file
  -foaf, --output-annotation-file <file>
                           An output annotation file
  --show-registrations     print discovered registered libraries and transforms
Logging Options
  -ll, --log-level {error|warn|info|debug|trace}
                           Set global logging verbosity (default: None
  -cll, --class-log-level <FullClassName:{error|warn|info|debug|trace}>...
                           Set per-class logging verbosity
  --log-file <file>        Log to a file instead of STDOUT
  -lcn, --log-class-names  Show class names and log level in logging output
CIRCT (MLIR FIRRTL Compiler) options
  --target {chirrtl|firrtl|hw|verilog|systemverilog}
                           The CIRCT
  --preserve-aggregate <value>
                           Do not lower aggregate types to ground types
  --module <package>.<module>
                           The name of a Chisel module to elaborate (module must be in the classpath)
  --full-stacktrace        Show full stack trace when an exception is thrown
  --throw-on-first-error   Throw an exception on the first error instead of continuing
  --use-legacy-shift-right-width
                           Use legacy (buggy) shift right width behavior (pre Chisel 7.0.0)
  --warnings-as-errors     Treat warnings as errors
  --warn-conf <value>      Warning configuration
  --warn-conf-file <value>
                           Warning configuration
  --source-root <file>     Root directory for source files, used for enhanced error reporting
  --split-verilog          Indicates that "firtool" should emit one-file-per-module and write separate outputs to separate files
  --firtool-binary-path path
                           Specifies the path to the "firtool" binary Chisel should use.
  --dump-fir               Write the intermediate .fir file
AspectLibrary
  --with-aspect <package>.<aspect>
                           The name/class of an aspect to compile with (must be a class/object without arguments!)
```

这里介绍了一个划分参数`-split-verilog Indicates that "firtool" should emit one-file-per-module and write separate outputs to separate files`

#### chisel 优化模块中的信号

[How to keep all variable name In chisel when generate Verilog code - Stack Overflow](https://stackoverflow.com/questions/55401525/how-to-keep-all-variable-name-in-chisel-when-generate-verilog-code)

社区：[Gitter * | Chisel Lang](https://app.gitter.im/#/room/#chisel:matrix.org/$xzDaO22wkfLzXY01CLJDG7MHuLf-fSNcr6KO5fapYNc)

### chisel生成verilog代码

[chisel生成Verilog与基本测试(更新)-CSDN博客](https://blog.csdn.net/qq_39507748/article/details/118087886)

[chisel - How do you split modules into individual files using ChiselStage? - Stack Overflow](https://stackoverflow.com/questions/66419204/how-do-you-split-modules-into-individual-files-using-chiselstage)

[chisel - How do you split modules into individual files using ChiselStage? - Stack Overflow](https://stackoverflow.com/questions/66419204/how-do-you-split-modules-into-individual-files-using-chiselstage)

#### Array每一项要加`,`

```scala
Array(
    member1,member2
)
```

#### val创建的变量用的符号是`=`

#### 1.U(1.W) 和true.Bool是不同类型的常量，会报错

#### Mux(这个位置是bool变量不能是1.U(1.W)，)
