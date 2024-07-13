# Chsel

chsel语言是在scala语言中构建了Verilog生成器的类，即使用基本scala语言定义了类，这些类的是些基础模块，使用这些基础模块可以编写出scala代码，这些代码可以生成verilog的代码，这就是chsel 

# 变量与常量

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/chisel变量类型.png)

## 数据类型

- `SInt`：表示有符号类型

- `UInt`：表示无符号类型

- `Bits`：表示`bool`类型

- 如果`chisel`不指定数据位宽的话，会默认会选中能表示该数据的最小位宽
  
  如果不指定进制默认是采用10进制表示
  
  如果指定位宽大于数据表示所需的最小位宽，则无符号数据采用无符号扩展，有符号采用有符号扩展，如此扩展到指定位宽
  
  ```scala
  5.asSInt(8.W) // 对b1001进行扩展为b0000_0101
  5.asUInt(8.W) // 对b1011进行扩展为b1111_1011
  ```
  
  if指定位宽不足够表示指定数据，则会报错

## 常量

#### 如何定义常量数据

##### 定义指定位宽和类型的数据

###### 定义个数

```scala
{digital|string}.{U|S|B}[(digital.W)]
{数字|字符串}.{无符号类型|有符号类型|bool类型}[(数字.W)]
```

```scala
8.U(4.W)//定义了一个4bit宽的值为8的无符号数据
-152.S(32.W)//定义了一个32bit宽度的值为-152的有符号数
```

定义数据的way1----定义指定类型的数据

```scala
true.B//定义了一个bool类型的值为1的数据
false.B//定义一个bool类型的值为0的数据
5.S//定义了一个值为
-8.S
"ha".U
"o.12".U
"b1010".U
```

##### 定义数据支持使用`_`进行划分数据

```scala
"h_dead_beef".U//定义了一个只是：0xdeadbeff的32位数据
```

可以使用`_`对数据进行分隔便于阅读，

##### 定义数据不指定位宽

如果`chisel`不指定数据位宽的话，会默认会选中能表示该数据的最小位宽

```scala
-5.S//这个位宽默认就是4位，即：1011,这是使用补码表示的形式
//最高位是符号位
```

##### 定义数据的way2-----使用强制类型转换的方式

```scala
"ha".asUInt(8.w) //定义了一个位宽为8的，值为16进制a的无符号数据“
“o12".asUInt(6.w)//定义了一个位宽为6的，值为8进制12的无符号数据
”b1010”.asUInt(12.W)//定义了一个位宽为12的，值为2进制1010的无符号数据
5.asSInt(7.W) //定义了一个位宽为7的值为10进制5的有符号数
5.asUInt(8.W) //定义了一个位宽为8的值为10进制5的无符号数
```

##### 定义数据的way3-----使用函数进行创建

+ `Bit()`，eg:`Bit(8.W)`

+ `UInt`,eg:`UInt(8.W)`

+ `SInt`,eg:`SInt(10.W)`

##### 如何实现转换数据的类型

这里转换数据类型的方法统称为：cast方法

使用`.asUInt`是不会修改原变量中数据的格式的

```scala
val sint = 3.S(4.W) 
val uint = sint.asUInt //这里sint中数据仍然是有符号，位宽为4的类型
```

```scala
//定义了一个位宽为4，值为3的由符号数
//并将其赋值给val属性的变量名称为：sint的变量
val sint = 3.S(4.W) 

//使用asUInt方法将sint转为无符号数据并赋值给 变量Uint
//因为所有变量其实都是一个类的实例，每个类具有一些操作方法，
//所以可以使用.访问sint的方法asUint
//特别的是这里的asUint方法不能在指定宽度了
val uint = sint.asUInt

val uint = sint.asUint(8.W) //这行就会报错
```

也可以将数据转为时钟（这种特别危险）

```scala
// false.B表示类型为bool的，值为0的常量
//将其赋值给类型为Bool的名称为bool的变量
val bool: Bool = false.B   

//将bool转成时钟类型赋值给clock     
val clock = bool.asClock        // always-low clock

clock.asUInt                    // convert clock to UInt (width 1)
clock.asUInt.asBool             // convert clock to Bool (Chisel 3.2+)
clock.asUInt.toBool             // convert clock to Bool (Chisel 3.0 and 3.1
```

## 变量

### 基本变量

创建方式

```
val base_variable = UInt(32.W)
```

### Wire,Reg,IO------ verilog3大组件（有数字电路属性的变量）

UInt, SInt和Bits是Chisel类型，它们本身不代表硬件。只有将它们包装到Wire、Reg或IO中才能生成硬件。Wire表示组合逻辑，Reg表示寄存器(D个触发器的集合)，IO表示模块的连接(如具体集成电路(IC)的引脚)。任何Chisel类型都可以包裹在Wire、Reg或IO中。

通过将硬件组件赋值给一个Scala不可变变量，你可以给它起一个名字:

```scala
val number = Wire(UInt ())
val reg_i = Reg(SInt ())
```

稍后，您可以使用Chisel操作符:=将值或表达式赋值(或重新赋值)给Wire、Reg或IO

```scala
number := 10.U
reg_i := value - 3.U
```

这里的使用Wire就会生成一个`wire number;`,使用Reg就会生成一个`reg reg_i;`

如何定义verilog中寄存器在chisel

#### Wire()----创建一个verilog的wire类型的变量

##### 定义一个wire变量

```scala
val number = Wire(UInt())
```

翻译成verilog

```verilog
wire [31:0]number; 
```

或者： 

```scala
//创建一个verilog中的wire类型的变量，
//它的属性是无符号，位宽是32,没有初始值
val w = Wire(UInt())

//将a&b的组合逻辑传给w
w := a&b
```

转成verilog

```verilog
wire w;
assign w = a&b;
```

##### 定义一个有初始值的

```scala
val number = WireDefault(10.U(4.W))
```

#### Reg

##### 定义具有初始值的寄存器

```scala
val reg = RegInit(8.U(8.w))
```

##### 定义不具有初始值的寄存器

```scala
class RegisterModule extends Module{
    val io = IO(
                 new Bundle{
                             val in  = Input ( UInt(12.W) )
                             val out = Output( UInt(12.W) )
                          }
               )
    val register = Reg( UInt(12.W) )
    register := io.in + 1.U
    io.out := register
}


test(new RegisterModule ) { c =>
    for( i <- 0 until 100){
        c.io.in.poke(i.U)
        c.clock.step(1)
        c.io.out.expect( (i + 1).U)
    }   
}
println("SUCESS!!")
```

寄存器是通过调用Reg(tpe)创建的，其中tpe是一个变量，用于编码我们想要的寄存器类型。在这个例子中，类型是一个12位的无符号数据类型。

看看上面的测试人员在做什么。在调用poke()和expect（）之间，有一个对step(1)的调用。这将告诉测试工具时钟执行一个时钟周期，这将导致寄存器将其输入传递到其输出。

调用step(n)将使时钟滴答n次。

机敏的观察者会注意到以前测试组合逻辑的测试人员没有调用step()。这是因为在输入上调用poke()会立即通过组合逻辑传播更新的值。调用step()用于更新顺序逻辑中的状态元素。

note:

- 这个模块具有一个隐式的clock和reset信号，所以你不用添加这个信号

- 变量register，对应的verilog中是`reg [11:0]register`

- register是在时钟上升沿更新的

##### RegNext----将next值直接传入代码更短

chisel有一个方便的寄存器对象与简单的输入连接寄存器。前面的模块可以缩短为下面的模块。注意，这次我们不需要指定寄存器位宽。它从寄存器的输出连接推断出来，在本例中是io.out。

```scala
class RegNextModule extends Module{
    val io = IO(
                 new Bundle{
                             val in  = Input (UInt(12.W))
                             val out = Output(UInt(12.W))
                           }
               )
    //寄存器的位宽是可以从io.out的位宽推导出
    io.out := RegNext(io.in + 1.U)
}


test(new RegNextModule){ c =>
    for(i <- 0 until 100){
        c.io.in.poke(i.U)
        c.clock.step(1)
        c.io.out.expect( (i + 1).U )
    }

}

println("Sucess!!!")
```

这里的`io.out := RegNext(io.in+1.U)`翻译成

```verilog
always@(posedge clock)begin
    if(reset)begin
        register <= ;
    end else begin
        register <= in +1;
    end
end

assign out = register
```

##### 使用寄存器

```scala
val reg = RegInit(0.8(8.w))
//寄存器的输入要使用 :=,这里d表示的是寄存器的数据输入
reg := d
//使用寄存器的输出数据，使用=即可
val q = reg
```

#### verilog的inout类型-------Analog

`Analog`类型等同于`verilog`的`inout`类型

```scala
//定义了一个位宽为1类型是Analog的常量，赋值给变量
val a = IO(Analog(1.w)) 
val b = IO(Analog(1.W))
val c = IO(Analog(1.W))
```

##### 如何拼接Analog------attach

```scala
val a = IO(Analog(1.W))
val b = IO(Analog(1.W))
val c = IO(Analog(1.W))

// Legal 
// 将a和b进行连接
attach(a, b)
// 将a和c进行连接
attach(a, c)

// Legal
a <> b

// Illegal - connects 'a' multiple times
a <> b
a <> c
```

`<>`和attach是相同功能，但是`<>`只能使用一次，这也难了，谁知道，这个变量在之前使用过几次`<>`

##### Cat-----verilog中的{}

```scala
class MyOperatorsTow extends Module{
    val io = IO(new Bundle{
         val in      = Input ( UInt(4.W) )
         val out_mux = Output( UInt(4.W) )
         val out_cat = Output( UInt(4.W) )
                })
    val s = true.B
    io.out_mux := Mux(s,3.U,0.U)
    io.out_cat := Cat(2.U,1.U)

}

println( getVerilog(new MyOperatorsTwo) )


test(new MyOperatorsTwo){ c =>
    c.io.out_mux.expect(3.U)
    c.io.out_cat.expect(5.U)
}

println("SUCCESS!!")
```

运行结果

```
Elaborating design...
Done elaborating.
module MyOperatorsTwo(
  input        clock,
  input        reset,
  input  [3:0] io_in,
  output [3:0] io_out_mux,
  output [3:0] io_out_cat
);
  assign io_out_mux = 4'h3; // @[cmd6.sc 9:20]
   
  // 5的二进制是4'b0101
  assign io_out_cat = 4'h5; // @[Cat.scala 30:58] 
endmodule

Elaborating design...
Done elaborating.
test MyOperatorsTwo Success: 0 tests passed in 2 cycles in 0.003108 seconds 643.46 Hz
SUCCESS!!
```

#### 黑盒技术-------（black Boxes)

模块被定义成black Boxes，这个模块将在生成verilog的代码中被实例化，chisel编译器不会在生成其他代码来定义这个黑盒模块的行为，

black Box 和模块的不同之处是在于：module具有隐式的时钟和复位信号，而black box必须显示指定时钟和复位信号，即必须在black box 中`input wire clk,input wire rst`

这个black box 才有时钟和复位信号 

在IO Bundle中申明端口，则生成的verilog代码，会携带请的名称在io_前

样例代码真是垃圾，如果我写样例代码，针对一个初学者，我会详细介绍，这里每一行代码

这样可以避免如果从中间开始阅读，阅读困难

使用黑盒样例代码

```scala
// 使用import导入包，这里_符号什么含义
import chisel3._  
import chisel3.util._
import chisel3.experimental._

//通过继承的方式表明这个类是BlackBox
//类的参数是Map("DIFF_TRM" -> "TRUE","IOSTANDARD"->"DEFAULT") 是什么意思
//"DIFF_TRM" -> "TRUE"表示黑盒由一个自定义参数“DIFF_TRM",
//它的初始值是”TRUE"
class IBUFD extends BlackBox( Map("DIFF_TRM" -> "TRUE",
    "IOSTANDARD"->"DEFAULT") ){
    // IO(new Bundle)这又是什么神奇的操作，这是那个语法可以导致编写这样的代码
    // IO....表示是创建方法吗，IO是方法名，new Bundle 是参数可是val O、 I、IB
    //这里的定义又是什么意思
    //这里的Output()又是什么意思，你知道了吗TM从中间开始看的时候这么多基础点都不知道
    //你个sb，为什么就必须让别人顺着往下看，只要一点没看过就无法阅读你的代码
    val io = IO(new Bundle{
                             val O  = Output( Clock() )
                             val I  = Input ( Clock() )
                             val IB = Input ( Clock() )
                           }  
                )


}

 class Top extends Module{
    val io     = IO(new Bundle{})
    // Moduel(new IBUFDS) 这是什么语法，完全没见过
    val ibufds = Module(new IBUFDS)
    // := 这种又是什么语法，也是没有概念
    ibufds.io.I := clock  
}
```

这里 `val ibufds = Module(new  IBUFDS) `表示在verilog代码中，调用了`IBUFD`模块，这个这个模块的调用名为：ibufds，展开的rtl代码是：

```verilog
IBUFDS # (.DIFF_TERM("TRUE"), .IOSTANDARD("DEFAULT")) ibufds(
    .IB(ibufds_IB),
    .I(ibufds_I),
    .O(ibufds_O)
)
```

默认信号的构建方式是：`模块的调用名_模块内该信号的名称`

```scala
class IBUFDS extends BlackBox(Map("DIFF_TERM" -> "TRUE",
                                  "IOSTANDARD" -> "DEFAULT")) {
    val io = IO(new Bundle {
                            val O  = Output(Clock())
                            val I  = Input(Clock())
                            val IB = Input(Clock())
                           }
               )
}
```

这段代码就是相当于verilog中创建一个文件名为`IBUFDS.v`的文件，然后使用在里面定义了一个名叫`IBUFDS`的模块

```verilog
module IBUFDS #(parameter DIFF_TEAM="TRUE",
parameter IOSTANDARD = "DEFAULT")(
    output wire  O,
    input  wire  I,
    input  wire  IB
);

verilog逻辑

endmodule
```

黑盒的名称和要接进去的verilog代码的模块名成一样，

创建一个io用于保存这个verilog模块的输入输出信号

chisel不需要声明这个verilog的位置，因为它两名字是一样的，到时候使用这个chisel代码生成的也只是这个verilog调用代码，verilog会根据这个模块去找到它的.v文件的

#### 用于信号连接的符号

p是信号的提供者，即辅助产生信号的producer,c是信号的接收者即辅助使用信号的cosumer

##### `c:=p`

##### `c :#= p`

直接将p的信号全部连接到c上，即将p的信号全部合并成一个bus，c也是，然后两者相连

##### `c :<= p`

找到c中所有和p同名的信号，让c这些同名信号和p对应的信号进行连接

##### `c :>= p`

在c由哪些是p的信号反转，就将这些信号和p对反转的信号进行连接

反转信号就是指：相同信号p是输出给外界使用的，进行反转这个信号就是需要外界输入到内部使用的，类似：verilog有两个模块A，B，这两个要相互传递数据a，那数据a在模块A的端口和在模块B的段两者就属于反转信号的关系

##### `c:<>=p`

将反转信号和对其信号进行连接，我只能说这个描述跟个sb一样的。属实没想到，它说的书内容少，原来是对知识解释性的描述少啊，难绷住

#### 从信号中如何提取某些字段的信号---等同verilog的`[]`

##### 提取一个bit

```scala
//singal_name（index）
//信号名（信号所在索引），信号索引从0开始
val x = "b1010".U(4.W)
//表示获取x信号第0位，值为：0
//从右往左数数，序号从0开始，
val x_signal_0 = x(0)

val x_siganl_1 = x(1)
```

##### 提取一个字段

```scala
//singal_name（high_index,low_index）
//信号名（信号所在索引），信号索引从0开始到3
val x = "b1010".U(4.W)
//表示获取x信号第0位到第2位，用集合表示范围就是[0,2]
//值为”b010".U(3.W)
val x_signal_0 = x(2，0)
```

##### `##` verilog中的`{}`进行信号拼接

```
val x = "hbee".U(32.W)

//high_byte的值是"he.U(8.W)"
val high_byte = x(15,8)

//low_byte的值是"he.U(8.W)"
val low_btye  = x(7,0)

//bee_half_word的值是"hee.U(16.W)"
val bee_half_word  = high_byte ## low_byte
```

#### Mux-----verilog多路选中器

```scala
//Mux(select_signal,index1_sign,index0_sign)
//Mux(选择的控制信号，控制信号为1的时候选中的信号，控制信号为1的时候选中的信号)
val result = MUx(sel,a,b)
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240520105822.png)

翻译成Verilog代码

```verilog
wire result = sel ? a :b;
或者使用门级表示
wire result = (a & sel) |(b & !sel)
```

#### when-----verilog中的always语句

```scala
val w = Wire(Uint())
w := 0.U
when (cond){
    w : 3.U
}
```

感觉大概会这样翻译`chisel`代码，

```verilog
//这里的位宽规定不太能够清楚
reg w = $unsign 1'h0
always @(cond)begin
    w = $unsign 2'h3
end
或者这样翻译
wire w = cond ? 2'h3 ：2’h0
```

#### #### .otherwise----verilog中的else

```scala
val w = Wire(UInt () )


when(cond){
    w := 1.U
} .otherwise{
    w := 2.U
}
```

多重选择

```scala
val w = Wire (UInt() )
when (cond){
    w := 1.U
}.elsewhen(cond2){
    w := 2.U
}.otherwise{
    w :=3.U
}
```

翻译成的verilog

```verilog
wire val ;
assign val = cond  ? 2'd1 :
             cond2 ? 2'd2 : 2'd3;
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240520151440.png)

#### switch 语句

```scala
result := 0.U
switch(sel){
    is(0.U) { result := 1.U }
    is(1.U) { result := 2.U }
    is(2.U) { result := 4.U }
    is(3.U) { result := 8.U}
}
```

翻译的verilog

```verilog
reg result
always @(*)    begin
    switch(sel)begin
        0: result = 1
        1: result = 2
        2: result = 4
        3: result = 8
        default: result = 0
    end
end
```

或者

```verilog
wire result
assign result = sel == 0 ? 1 :
                sel == 1 ? 2 :
                sel == 2 ? 4 :
                sel == 3 ? 8 : 0 ; 
```

#### `Vec`-----表示verilog中的二维数组

```scala
//Vec(unit_num,uint_width)
//Vec(数字中有多少个单元，每个单元的宽度)
val v = Wire( Vec(3,UInt(4.W)) )
v(0) := 1.U
v(1) := 3.U
v(2) := 5.U

//使用变量作为二位数字的索引
val index = 1.U(2.W)
val a = v(index)
```

##### 定义二维寄存器组

```scala
val registerFile = Reg( Vec(32, UInt(32.W)) )
registerFile(index) := dIn
val dOut = registerFile(index)
```

转为verilog

```verilog
reg [31:0] registerFile [32];
always @(*)begin
    registerFile[index] = dIn 
end
assign dOut = registerFile [index]
```

# 打包函数或者打包类:将多个变量构成一个整体，类似于结构体

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/chisel的打包函数.png)

## Bundle

将变量、bundle打包的变量、接口变量打包成一个整体

```
val = Bundle()
```

### 类似于C语言结构体方式的打包

```scala

```

## Valid

将多个基本变量构成的bundle或者一个基本变量打包成bit，再添加一个bool类型valid

# chise如何创建一个模块

`chisel`中的`Module`类等同于`verilog`中的`module`一样

##### Module类 scala内部实现原理

```scala
import chisel3._

class Mux2 extends Module {
  val io = IO(new Bundle {
    val sel = Input(Bool())
    val in0 = Input(UInt())
    val in1 = Input(UInt())
    val out = Output(UInt())
  })
  io.out := Mux(io.sel, io.in0, i
o.in1)
}
//通过伴生对象实现构造函数
object Mux2 {
  def apply(sel: UInt, in0: UInt, in1: UInt) = {
    val m = Module(new Mux2)
    m.io.in0 := in0
    m.io.in1 := in1
    m.io.sel := sel
    m.io.out
  }
}
```

Scala中的对象有一个预先存在的创建函数(方法)，叫做apply。当一个对象被用作表达式中的值时(这基本上意味着调用了构造函数)，这个方法决定返回的值。在处理硬件模块时，人们希望模块输出能够代表硬件模块的功能。因此，我们有时希望模块输出是在表达式中使用对象作为值时返回的值。由于硬件模块被表示为Scala对象，因此可以通过定义对象的apply方法来实现

如何使用这样创建出的类

```scala
class Mux4 extends Module {
  val io = IO(new Bundle {
    val in0 = Input(UInt(1.W))
    val in1 = Input(UInt(1.W))
    val in2 = Input(UInt(1.W))
    val in3 = Input(UInt(1.W))
    val sel = Input(UInt(2.W))
    val out = Output(UInt(1.W))
  })
  val m0 = Module(new Mux2)
  m0.io.sel := io.sel(0)
  m0.io.in0 := io.in0
  m0.io.in1 := io.in1

  val m1 = Module(new Mux2)
  m1.io.sel := io.sel(0)
  m1.io.in0 := io.in2
  m1.io.in1 := io.in3

  val m3 = Module(new Mux2)
  m3.io.sel := io.sel(1)
  m3.io.in0 := m0.io.out
  m3.io.in1 := m1.io.out

  io.out := m3.io.out
}
```

##### 使用具有隐式时钟和复位的Module

`Module`这个类具有隐式的`clock`，`reset`两个端口，需要里类里面定义输入输出信号，然后就可以定义实现逻辑了

###### way1使用类的实例定义输入输出

给IO类传入的参数是一个接口类的实例

```scala
import chisel3._
class Mux2IO extends Bundle{
    val sel = Input(UInt(1.W))
    val in0 = Input(UInt(1.W))
    val in1 = Input(UInt(1.W))
    val out = Output(UInt(1.W))
}


class Mux2 extends Module{
    //通过new根据一个接口类创建一个实例传给IO这个函数
    //指定创建的模块的输入输出端口
    val io  = IO(new Mux2IO) 
    io.out := (io.sel & io.in1) | (~io.sel & io.in0)
}

class Mux4IO extends Bundle{
    val in0 = Input(UInt(1.W))
    val in1 = Input(UInt(1.W))
    val in2 = Input(UInt(1.w))
    val in3 = Input(UInt(1.W))
    val sel = Input(UInt(2.W))
    val out = Output(UInt(1.W))
}

class Mux4 extends Module{
    val io = IO(new Mux4IO)

    val m0 = Module(new Mux2)
    m0.io.sel := io.sel(0)
    m0.io.in0 := io.in0
    m0.io.in1 := io.in1

    val m1 = Module(new Mux2)
    m1.io.sel := io.sel(0)
    m1.io.in0 := io.in2
    m1.io.in1 := io.in3

    val m3 = Module(new Mux2)
    m3.io.sel := io.sel(1)
    m3.io.in0 := m0.io.out
    m3.io.in1 := m1.io.out

    io.out := m3.io.out

}
```

###### way2直接定义

```scala
import chisel3._
class Mux2IO extends Bundle{

}


class Mux2 extends Module{
    //通过new根据一个接口类创建一个实例传给IO这个函数
    //指定创建的模块的输入输出端口
    val io  = IO(new Bundle{
                            val sel = Input(UInt(1.W))
                            val in0 = Input(UInt(1.W))
                            val in1 = Input(UInt(1.W))
                            val out = Output(UInt(1.W))
                            }
                ) 
    io.out := (io.sel & io.in1) | (~io.sel & io.in0)
}
```

##### RawModule----------不带时钟和复位的类

`RawModule`这个模块不提供隐式的`clock`和`reset`，由于复位信号默认是高电平复位，如果想修改为低电平的可以使用`RawModule`

```scala
import chisel3.{RawModuel,withClockAndReset}


class Foo extends Module{
    val io = IO(
                new Bundle{
                            val a = Input(Bool())
                            val b = Output(Bool())
                          }
               )
    io.b := !io.a
}
class FooWrapper extends RawModule{
    val a_i  = IO( Input(Bool())  )
    val b_o  = IO( Output(Bool()) )
    val clk  = IO( Input(Clock()) )
    val rstn = IO( Input(Bool())  )

    val foo  = withClockAndReset(clk,!rstn){Module(new Foo)}

    foo.io.a := a_i
    b_o  := foo.io.b
}
```

##### Module如何调用

```scala
import chisel3._
class Mux2IO extends Bundle{
    val sel = Input(UInt(1.W))
    val in0 = Input(UInt(1.W))
    val in1 = Input(UInt(1.W))
    val out = Output(UInt(1.W))
}


class Mux2 extends Module{
    //通过new根据一个接口类创建一个实例传给IO这个函数
    //指定创建的模块的输入输出端口
    val io  = IO(new Mux2IO) 
    io.out := (io.sel & io.in1) | (~io.sel & io.in0)
}

//val instance_name = Module(new module_name)
//val 模块实例名称  = Module(new 模块名称)
val m0 = Module(new Mux2)
    m0.io.sel := io.sel(0)
    m0.io.in0 := io.in0
    m0.io.in1 := io.in1
```

##### 创建一个指定位宽的模块

```scala
class PassthroughGenerator(width :Int) extends Module{
    val io = IO(new Bundle{
                            val in  = Input ( UInt(Width.W) )
                            val out = Output( UInt(width.w) )
                          }
               )
    io.out := io.in
}


println(getVerilog(new PassthroughGenerator(10))
println(getVerilog(new PassthroughGenerator(20))
```

```verilog
Done elaborating.
module PassthroughGenerator(
  input        clock,
  input        reset,
  input  [9:0] io_in,
  output [9:0] io_out
);
  assign io_out = io_in; // @[cmd7.sc 6:10]
endmodule

Elaborating design...
Done elaborating.
module PassthroughGenerator(
  input         clock,
  input         reset,
  input  [19:0] io_in,
  output [19:0] io_out
);
  assign io_out = io_in; // @[cmd7.sc 6:10]
endmodule
```

如何调用这种可以指定位宽的模块

使用`数字.U`这种参数格式是会报错的，只能使用`数字`作为参数

```scala
println(getVerilog(new PassthroughGenerator(10.U))//会报错
```

##### 多个指定参数的模块

```visual-basic
class ParameterizedWidthAdder(in0Width:Int,in1Width:Int,sumWidth:Int) extends Module{
    require (in0Width >= 0)
    require (in1Width >= 0)
    require (sumWidth >= 0)

    val io = IO(new Bundle{
                            val in0 = Input ( UInt(in0Width.W) )
                            val in1 = Input ( UInt(in1Width.W) )
                            val sum = Output( UInt(sumWidth.W) )
                          }

               )
    io.sum := io.in0 +& io.in1
} 

//直接使用数字进行初始化
println( gerVerilog(new ParameterizedWidthAdder(1,4,6)) )
```

##### 使用Map构造参数具有默认值的模块

```scala

```

##### 设置参数的类型的上下界`class AbstractALU [T <: ALUFN](val aluFn: T) extends Module`

在这段代码中，`[T <: ALUFN]` 使用了Scala的类型参数和上下界约束。具体来说，它的作用和意义如下：

### 泛型类型参数

`T` 是一个类型参数，允许 `AbstractALU` 类在定义时接受不同的类型。这样可以使类更加通用和灵活。

### 上下界约束 `<: ALUFN`

`T <: ALUFN` 表示类型参数 `T` 必须是 `ALUFN` 类的子类型或 `ALUFN` 本身。也就是说，传递给 `AbstractALU` 的类型参数 `T` 必须继承自 `ALUFN`。

### `AbstractALU` 类的定义

结合上下文，以下是对这段代码的解释：

```scala
abstract class AbstractALU[T <: ALUFN](val aluFn: T)(implicit p: Parameters) extends CoreModule()(p) {
  val io = IO(new Bundle {
    val dw = Input(UInt(SZ_DW.W))
    val fn = Input(UInt(aluFn.SZ_ALU_FN.W))
    val in2 = Input(UInt(xLen.W))
    val in1 = Input(UInt(xLen.W))
    val out = Output(UInt(xLen.W))
    val adder_out = Output(UInt(xLen.W))
    val cmp_out = Output(Bool())
  })
}
```

### 详细解释

1. **泛型参数 `T`**：
   
   - `T` 是一个类型参数，用于定义 `AbstractALU` 可以接受的类型。

2. **上下界约束 `T <: ALUFN`**：
   
   - `T` 必须是 `ALUFN` 的子类型或 `ALUFN` 本身。这确保了传递给 `AbstractALU` 的类型具有 `ALUFN` 的所有属性和方法。

3. **构造参数 `aluFn`**：
   
   - `val aluFn: T` 是一个类型为 `T` 的构造参数，表示 ALU 的功能，这个功能由 `ALUFN` 类及其子类定义。

4. **隐式参数 `p`**：
   
   - `implicit p: Parameters` 是一个隐式参数，表示参数配置，用于硬件生成中的配置机制。

5. **`CoreModule` 的扩展**：
   
   - `AbstractALU` 继承自 `CoreModule`，并在构造函数中传递参数 `p`。

6. **`io` 接口**：
   
   - 定义了 `AbstractALU` 的输入输出接口，包括 `dw`、`fn`、`in2`、`in1`、`out`、`adder_out` 和 `cmp_out`。

### 作用和意义

这种泛型类型参数和上下界约束的使用有以下几个好处：

- **通用性**：使 `AbstractALU` 类能够适应不同的 ALU 功能实现，而不局限于某一种具体的实现。
- **类型安全**：确保传递给 `AbstractALU` 的类型具有 `ALUFN` 的属性和方法，保证类型安全。
- **代码重用**：通过使用泛型，可以避免重复代码，提高代码的可重用性。

### 示例

假设有一个 `ALUFN` 类定义如下：

```scala
abstract class ALUFN {
  val SZ_ALU_FN: Int
  // 定义 ALUFN 的方法和属性
}
```

那么，您可以定义不同的 `ALUFN` 子类，并将其作为类型参数传递给 `AbstractALU`：

```scala
class MyALUFN extends ALUFN {
  val SZ_ALU_FN = 4
  // 实现 ALUFN 的方法和属性
}

val myAluFn = new MyALUFN()
val myALU = new AbstractALU[MyALUFN](myAluFn) {
  // 实现 AbstractALU 的抽象方法和属性
}
```

这样，`AbstractALU` 就可以使用 `MyALUFN` 的实现，同时保持类型安全和通用性。

##### 使用Option作为默认参数

当对象或函数有很多参数时，全部列出来是很容易出现错误的，有时候参数可以设置默认值，有些时候参数不好设置默认值，`Option`可以使用`None`作为默认值

如果`resetValue = None`，表示默认，寄存器将不会复位值和将被初始化没用信息，这避免了使用正常范围之外的值来表示“none”的常见但丑陋的情况，例如使用-1作为重置值来表示此寄存器未重置。

```scala
class DelayBy1(resetValue:Option[UInt] = None ) extends Module{
    val io = IO(new Bundle{
                    val in  = Input ( UInt(16.W) ) 
                    val out = Output( UInt(16.W) )
                })
    //isDefined有tm是什么方法啊，这tm是scala的类啊，md，有tm引入奇怪的东西了
    val reg = if(resetValue.isDefined){
        RegInit(resetValue.get)
    }else{
        Reg( UInt() ) 
    }
    reg    := io.in
    in.out := reg
}

println( getVerilog(new DelayBy1) )

//Some是上面类型啊？？？？
println( getVerilog(new DelayBy1(Some(3.U) )
```

##### 生成输入输出端口个数可配置的模块

有时候我们希望输入输出端口能够被选择性地包含或排除在外。

有时候某些端口是想在调试debug的时候才可以看到，生成模块的时候不包含这些端口

因为有一个合理的默认值。也许您的生成器有一些不需要在每种情况下都连接的输入，

可配置IO选项的模块举例：

可选的bundle字段是获得此功能的一种方法。在下面的例子中，我们展示了一个可选的进位加法器。如果包含进位，则`io.carryIn`的类型是`Some[UInt]`，并且包含在IO bundle中。如果不包括进位，则io。carryIn将具有None类型，并且将从IO bundle中排除。

```scala
class HalfFullAddr(val hasCarry:Boolean ) extends Module{
    val io = IO(new Bundle{
        val a         = Input( UInt(1.W) )
        val b         = Input( UInt(1.W) )
        val carryIn   = if(hasCarry) Some( Input(UInt(1.W)) ) else None
        val s         = Output( UInt(1.W) )
        val carryOut  = Output( UInt(1.W) )
    })
    val sum = io.a +& io.b +& io.carryIn.getOrElse(0.U)
    io.s := sum(0)
    io.carryOut := sum(1)
}
```

###### 配置端口的宽度

实现与Options类似功能的另一种方法是使用宽度为0的接口wire。chisel中类型允许宽度为零。宽度为零的IO将从生成的Verilog中修剪，任何试图使用宽度为0wire类型的值都将获得恒定的零。如果零是一个合理的默认值，那么宽度零连接可能会很好，因为它们避免了匹配选项或调用getOrElse的需要。

```scala
class HalfFullAdder(val hasCarry: Boolean) extends Module {
  val io = IO(new Bundle {
    val a = Input(UInt(1.W))
    val b = Input(UInt(1.W))
    val carryIn = Input(if (hasCarry) UInt(1.W) else UInt(0.W))
    val s = Output(UInt(1.W))
    val carryOut = Output(UInt(1.W))
  })
  val sum = io.a +& io.b +& io.carryIn
  io.s := sum(0)
  io.carryOut := sum(1)
}
println("Half Adder:")
println(getVerilog(new HalfFullAdder(false)))
println("\n\nFull Adder:")
println(getVerilog(new HalfFullAdder(true)))
```

##### 设置隐式端口

在编程过程中，经常会遇到需要大量样板代码的情况。为了处理这个用例，Scala引入了隐式的概念，它允许编译器为您做一些语法糖。因为很多事情都是在幕后发生的，所以暗示会显得非常神奇。本节将分解一些基本示例来解释它们是什么以及它们通常在哪里使用。

###### 设置隐式参数

有时，您的代码将需要从一系列函数调用的深处访问某种顶级变量。您可以使用隐式参数，而不是在每个函数调用中手动执行此变量。

例子:隐式Cat

在下面的示例中，我们可以隐式或显式地传递Cat的数量。

```scala
object CatDog {
  implicit val numberOfCats: Int = 3
  //implicit val numberOfDogs: Int = 5

  def tooManyCats(nDogs: Int)(implicit nCats: Int): Boolean = nCats > nDogs

  val imp = tooManyCats(2)    // Argument passed implicitly!
  val exp = tooManyCats(2)(1) // Argument passed explicitly!
}
CatDog.imp
CatDog.exp
```

###### 设置隐式转换

# 运算间的宽度推理

| operation                     | bit width                    |
| ----------------------------- | ---------------------------- |
| `z = x + y` *or* `z = x +% y` | `w(z) = max(w(x), w(y))`     |
| `z = x +& y`                  | `w(z) = max(w(x), w(y)) + 1` |
| `z = x - y` *or* `z = x -% y` | `w(z) = max(w(x), w(y))`     |
| `z = x -& y`                  | `w(z) = max(w(x), w(y)) + 1` |
| `z = x & y`                   | `w(z) = max(w(x), w(y))`     |
| `z = Mux(c, x, y)`            | `w(z) = max(w(x), w(y))`     |
| `z = w * y`                   | `w(z) = w(x) + w(y)`         |
| `z = x << n`                  | `w(z) = w(x) + maxNum(n)`    |
| `z = x >> n`                  | `w(z) = w(x) - minNum(n)`    |
| `z = Cat(x, y)`               | `w(z) = w(x) + w(y)`         |
| `z = Fill(n, x)`              | `w(z) = w(x) * maxNum(n)`    |

##### chisel的`+`和scala的`+`的区别

```scala
class MyModule extends Module{
    val io = IO(
                 new Boundle{
                               val in  = Input(UInt(4.W))
                               val out = Output(UInt(4.W)) 
                            }    
               )
    //scala的加法    
    val scala_two = 1 + 1 
    println(scala_two)   

    //chisel的加法
    val chise_u_two = 1.U + 1.U
    println(chisel_u_two)
    io.out := io.in
  }
println( getVerilog(new MyModule) )
```

执行结果

```
2 //这是scala_two的值
UInt<1>(OpResult in MyModule) //这是chisel_u_two的值
Done elaborating.
module MyModule(
  input        clock,
  input        reset,
  input  [3:0] io_in,
  output [3:0] io_out
);
  assign io_out = io_in; // @[cmd3.sc 12:10]
endmodule
```

造成这样的原因是`scala_two`的类型是scala中的Int，它的范围是完全可以表示`1+1`的结果的，

但是：`chisel_u_two`的类型是chisel中的1bit位宽的无符号类型，它的表示范围是{0,1},无法表示`2`，2的二进制表示是：`0b1

emmmm，好像我理解有问题了，tm不合理，不应该采用高位截断的方式吗？？？？

不能将chisel中的常数和scala中常数进行运算，因为这两者不是相同类型，chisel中的是`hardware wire`类型，而scala的类型是一个`Int`的类,他们两是无法进行自动的类型转换的

```scala
class MyModuleTwo extends Module {
  val io = IO(new Bundle {
    val in  = Input(UInt(4.W))
    val out = Output(UInt(4.W))
  })

  val twotwo = 1.U + 1 //会报错
  println(twotwo)

  io.out := io.in
}
println(getVerilog(new MyModuleTwo)) 
```

#### 打印生成的verilog

```scala
class Passthrough extends Module{
    val io = IO(
                new Bundle{
                           val in = Input(UInt(4.W)
                           val out = Output(UInt(4.W)                                         
                           }    
               )
    io.out := io.in
}

//创建一个实例给getVerilog函数，这个函数会将其转为verilog代码
//在使用println进行打印
println(getVerilog(new Passthrough)
```

#### 打印生成FIRRTL文件

```scala
class Passthrough extends Module{
    val io = IO(
                new Bundle{
                           val in = Input(UInt(4.W)
                           val out = Output(UInt(4.W)                                         
                           }    
               )
    io.out := io.in
}

//创建一个实例给(getFirrtl函数，这个函数会将其转为chisel到verilog的中间代码FirrTL
//在使用println进行打印
println(getFirrtl(new Passthrough)
```

```
circuit Passthrough :
  module Passthrough :
    input clock : Clock
    input reset : UInt<1>
    output io : { flip in : UInt<4>, out : UInt<4>}

    io.out <= io.in @[cmd2.sc 6:10]
```

#### chisel的测试

官方文档

[ucb-bar/chiseltest: The batteries-included testing and formal verification library for Chisel-based RTL designs. (github.com)](https://github.com/ucb-bar/chiseltest)

`iotesters`的是旧版的测试工具函数

`ChiselTest`的是目前新版的测试工具函数

|          | iotesters                  | ChiselTest            |
| -------- | -------------------------- | --------------------- |
| poke     | poke(c.io.in1, 6)          | c.io.in1.poke(6.U)    |
| peek     | peek(c.io.out1)            | c.io.out1.peek()      |
| expect   | expect(c.io.out1, 6)       | c.io.out1.expect(6.U) |
| step     | step(1)                    | c.io.clock.step(1)    |
| initiate | Driver.execute(...) { c => | test(...) { c =>      |

```scala
// Chisel Code, but pass in a parameter to set widths of ports
class PassthroughGenerator(width: Int) extends Module { 
  val io = IO(new Bundle {
    val in = Input(UInt(width.W))
    val out = Output(UInt(width.W))
  })
  io.out := io.in
}
```

使用旧版本的测试函数

```scala
val testResult = Driver(() => new
                        Passthrough() )
{
    c => new PeekPokeTester(c){
        poke(c.io.in,0) //设置输入值0
        expect(c.io.out,0)//assert输出值是否是0
        poke( c.io.in,1)
        expect(c.io.out,1)
        poke(c.io.in,2)
        expect(c.io.out,2)
    }

}
    assert(testResult)
    println("Success!!")
```

使用新版本的测试函数

```scala
test(new PassthroughGenerator(16)){ c =>
    c.io.in.poke(0.U)
    c.io.out.expect(0.U)
    c.io.in.poke(1.U)
    c.io.in.expect(1.U)
}
```

使用带时钟驱动的测试函数

```scala
test(new PassthroughGenerator(16)){ c=>
    c.io.in.poke(0.U)
    c.clock.step(1)
    c.io.out.expect(0.U)

}
```

```scala
test (new Passthrough() ){ c => //这里的c就是具有携带上测试信息的Passthrough的实例
    //往测试实例中中io变量的in传入值0.U
    c.io.in.poke(0.U) 
    //这个表示使用模块的输出端口io.out和正确数据0.U进行比较，如果不同就是设计有问题
    //出现不同就会终止程序执行
    c.io.out.expect(0.U)

    c.io.in.poke(1.U)
    c.io.in.poke(1.U)

    c.io.in.poke(2.U)
    c.io.in.poke(2.U)
}println("sucess!") //如果前面没有出现终止，打印了这句话，表示代码通过测试了
```

测试接受Passthrough模块，为模块的输入赋值，并检查其输出。要设置一个输入，我们调用poke。要检查输出，我们调用expect。如果我们不想将输出与预期值进行比较(没有断言)，我们可以查看输出。如果所有的expect语句都为真，那么我们的样板代码将返回pass。

poke传入的数据应该满足模块对该信号的定义，比如不能给bool类型的信号传入`10.U`这种信号

#### 如何使用“printf"对代码进行debug

chisel使用printf的用途主要有3种（3个层次，由顶层到底层排序）

1. 对chisel代码进行测试的时候，进行打印，查看chisel的逻辑是否正确（chisel层次,即在chisel的测试代码中使用printf）

2. 在电路生成期间使用`printf`，查看verilog代码生成，verilog是否符合自己在chisel层次考虑的逻辑（chisel生成verilog层次,即在chisel代码中使用printf）

3. 在电路进行仿真的时候使用`printf`，查看电路信号是否合理（verilog生成电路时候的层次,即在verilog代码中使用printf）

`println`是一个内置的Scala函数，用于打印到控制台。这个函数不能用在电路仿真的时候，因为生成电路使用语言是`FIRRTL`或者`Verilog`而不是`Scala`语言,即不能用于上面描述的序列3

eg:

```scala
class PrintingModule extends Module{
    val io = IO(
                 new Bundle{
                             val in  = Input(UInt(4.W))
                             val out = Output(UInt(4.W))
                           }
               )
    io.out := io.in
    // printf使用在chisel代码中
    printf("Print during simulation :Input is %d \n",io.in)
    // chisel 的 printf 有它自己的 interpolator 这个和python的字符串很像
    printf(p"Print during simulation:IO is $io \n")
    prinln(s"Print during generation :Input is ${io.in}")
}

//print使用chisel的测试代码中
test(new PrintingModule){c =>
    c.io.in.poke(3.U)
    c.clock.step(5)
    println(s"Print during testing:Input is ${c.io.in.peek()}")

}
```

#### 设置自己的时钟和复位，

这里时钟触发的方式和表示复位的电平仍然采用默认方式即：时钟上升沿触发，高电平复位

```scala
// we need to import multi-clock features
import chisel3.experimental.{withClock, withReset, withClockAndReset}

class ClockExamples extends Module {
  val io = IO(new Bundle {
    val in = Input(UInt(10.W))
    val alternateReset    = Input(Bool())
    val alternateClock    = Input(Clock())
    val outImplicit       = Output(UInt())
    val outAlternateReset = Output(UInt())
    val outAlternateClock = Output(UInt())
    val outAlternateBoth  = Output(UInt())
  })

  val imp = RegInit(0.U(10.W))
  imp := io.in
  io.out
  Implicit := imp

 //这个代码块使用io.alternateReset作为复位信号，
//withReset的复位也是高电平复位，
//也是就说io.alternateReset 为高电平就会执行代码块中的代码
  withReset(io.alternateReset) {
    // everything in this scope with have alternateReset as the reset
    val altRst = RegInit(0.U(10.W))
    altRst := io.in
    io.outAlternateReset := altRst
  }

  //这段代码表示在io.alternateClock上升沿触发执行
  withClock(io.alternateClock) {
    val altClk = RegInit(0.U(10.W))
    altClk := io.in
    io.outAlternateClock := altClk
  }

 //这段代码表示在io.alternateClock上升沿触发执行，复位是io.alternateReset为高电平
  withClockAndReset(io.alternateClock, io.alternateReset) {
    val alt = RegInit(0.U(10.W))
    alt := io.in
    io.outAlternateBoth := alt
  }
}
```

verilog翻译

```verilog
module ClockExamples(
  input        clock,
  input        reset,
  input  [9:0] io_in,
  input        io_alternateReset,
  input        io_alternateClock,
  output [9:0] io_outImplicit,
  output [9:0] io_outAlternateReset,
  output [9:0] io_outAlternateClock,
  output [9:0] io_outAlternateBoth
);
`ifdef RANDOMIZE_REG_INIT
  reg [31:0] _RAND_0;
  reg [31:0] _RAND_1;
  reg [31:0] _RAND_2;
  reg [31:0] _RAND_3;
`endif // RANDOMIZE_REG_INIT
  reg [9:0] imp; // @[cmd2.sc 14:20]
  reg [9:0] REG; // @[cmd2.sc 20:25]
  reg [9:0] REG_1; // @[cmd2.sc 26:25]
  reg [9:0] REG_2; // @[cmd2.sc 32:22]
  assign io_outImplicit = imp; // @[cmd2.sc 16:18]
  assign io_outAlternateReset = REG; // @[cmd2.sc 22:26]
  assign io_outAlternateClock = REG_1; // @[cmd2.sc 28:26]
  assign io_outAlternateBoth = REG_2; // @[cmd2.sc 34:25]
  always @(posedge clock) begin
    if (reset) begin // @[cmd2.sc 14:20]
      imp <= 10'h0; // @[cmd2.sc 14:20]
    end else begin
      imp <= io_in; // @[cmd2.sc 15:7]
    end
    if (io_alternateReset) begin // @[cmd2.sc 20:25]
      REG <= 10'h0; // @[cmd2.sc 20:25]
    end else begin
      REG <= io_in; // @[cmd2.sc 21:12]
    end
  end
  always @(posedge io_alternateClock) begin
    if (reset) begin // @[cmd2.sc 26:25]
      REG_1 <= 10'h0; // @[cmd2.sc 26:25]
    end else begin
      REG_1 <= io_in; // @[cmd2.sc 27:12]
    end
    if (io_alternateReset) begin // @[cmd2.sc 32:22]
      REG_2 <= 10'h0; // @[cmd2.sc 32:22]
    end else begin
      REG_2 <= io_in; // @[cmd2.sc 33:9]
    end
  end
```

# 键值访问

#### Map------类似python中的构造字典

使用键去访问Map()创建出的变量，可以获取对应的值，如果键是不存在的则会报错的

##### way1:获取字典的值

```scala
val map = Map("a" -> 1) //构造了一个键值对a是键，1是值
val a = map("a") //使用键a访问获取出值1,所以变量a的值是1
printn(a)
val b = map("b")//使用map中不存在的键进行访问，所以会报错的
println(b)
```

##### way2:使用get方法获取字典的值

```scala
val map = Map("a" -> 1)
val a   = map.get("a")
println(a)
val b   = map.get("b") //这段代码会报错的，因为访问了不存在的键
println(b)
```

#### None和Option

我就是这个bootcamp写的真是垃圾，所有引入新的东西都是不介绍的，简直了

```scala
val some =Some(1)
val none = None
println(some.get) //return 1
//println(none.get) //Errors
println( some.getOrElse(2) ) // Return 1
println( none.getOrElse(2) ) // Return 2
```

# 选择语句

#### Match/Case 语句

scala中的match概念是贯通于整个Chisel的，match这个概念也是每个chisel程序员必须了解的一部分，scala提供的match操作符可以支持

+ 像C语言的`switch`语句一样

+ 可以用于更为复杂的组合测试

+ 当变量类型是未知的或者不清楚的，将基于值的类型进行执行操作，比如
  
  + 比如变量是来自于一个异构的列表`val mixedList = List(1,"string",false)
  
  + 已知变量是超类的成员，但不知道它是哪个特定的子类。

+ 提取用正则表达式指定的字符串的子字符串

下面的例子，根据我们匹配的变量的值，执行不同的case语句:

```scala
// y 是一个定义在某处的整形变量
value
```

#### MuxLookup类型于switch语句

```scala
MuxLookup(idx,default)(
                       Seq( 0.U -> a, 
                            1.U -> b
                            )
                      )
```

module

```scala
val veriable = MuxLookup(键，默认的返回值即不在这里面的case语句中则返回默认值)(
    Seq( 键 -> 值,
         键 -> 值)    
)
```

#### Decoupled类-------自带握手信号的类

Chisel提供的一个常用接口是DecoupledIO，它为传输数据提供了一个现成的有效接口。这个想法是，当有数据要传输时，源驱动要传输的数据和有效信号的位信号。接收器在准备好接受数据时驱动就绪信号，当在一个周期中断言就绪和有效时，数据被认为已传输。

```scala
val myChiselData = UInt(8.W)
// or any Chisel data type, such as Bool(), SInt(...), or even custom Bundles
val myDecoupled = Decoupled(myChiselData)
```

上面的代码创建了一个带有字段的新耦包  

+ valid: Output(Bool) 

+ ready: Input(Bool) 

+ bits: Output(UInt(8 w))

#### Queue-----verilog的FIFO

#### `ReadyValidIO`/`Decoupled`标准的ready-valid接口

chisel提供标准的ready-valid的握手接口，一个ready-valid接口由：

1. 1个ready信号

2. 1个valid信号

3. 1表示握手传递数据的`bits`信号

当双方的信号握手成功则数据进行流动。如果ready和valid握手成功则方法`fire`将被执行

一般我们使用`Decoupled()`将任意数据转成握手接口的形式，即一般不直接采用`ReadyValidIO`这个方法

+ `Decoupled(...)`创建一个握手信号发生方/信号输出方 ready-valid结构的握手接口信号（i.e.bits是一个输出数据）

+ `Flipped( Decouple(...) )` 创建一个握手信号接收方/握手信号输入方，输入一个ready-valid接口信号（i.e.bits是一个输入信号）

eg:

```scala
import chisel3._
import chisel3.util.Decoupled

class ProducingData extedns Module{
    val io = IO(
                new Bundle{
                           val readyValid = Decoupled( UInt(32.W) )
                          }
                )

    io.readyValid.valid := true.B //信号发出方的valid信号
    io.readyValid.bits := 5.U //握手数据
}

class ConsumingData extends Module{
    val io = IO(
                new Bundle{
                             val readyValid = Flipped( DEcoupled(UInt(32.W)) )
                           }

               )
    io.readyValid.ready := false.B
}
```

DecoupledIO是一个ready-valid接口，它的约定是不保证声明ready或valid或比特的稳定性。这意味着ready和valid也可以在没有数据传输的情况下撤销。

evoableio是一个ready-valid接口，其约定是当valid拉高而ready拉低时，bits的值不会改变。

如果当前时钟周期ready是拉高但是valid是拉低的，接受方要持续保存ready是拉高的（）请注意，不可撤销约束只是一种约定，不能由接口强制执行。Chisel不会自动生成检查器或断言来强制执行不可撤销的约定。
