#### ZZQ的仓库模板

[yunzhong8/zzqChisel: zzq个人的chisel仓库 (github.com)](https://github.com/yunzhong8/zzqChisel)

#### hello word in Chisel了

```scala
class Hello extends Module{
    //定义输入输出信号，使用IO进行构造
    //有一个输入出信号，信号名称是led，使用Output指定它是输出信号
    //使用UInt指示它无符号类型，在verilog中所有变量默认都是无符号类型，
    //在verilog中如果要强调什么类型则要使用$signde和$unsigned来指示
    //使用1.W表示位宽是1
    val io = IO ( new Bundle{
                              val led = Output(UInt(1.W)  

                            }
                )
    //创建一个常量
    val CNT_MAX = (50000000 /2 -1).U


    val cntReg = RegInit(0.U(32.W))//创建一个初始值为0位宽为32的寄存器
    val blkReg = RegInit(0.U(1.W))//创建一个初始值为0位宽为1的寄存器

    // 计数器+1
    cntReg := cntReg + 1.U

    当计数器达到最大值则清空计数器，并且反转led的信号
    when(cntReg === CNT_MAX){
        cntReg := 0.U
        blkReg := ~blkReg
    }

    io.led :=blkReg
}
```

翻译成verilog代码

```verilog
module Hello(
    output wire led
);
parameter CNT_MAX = 4999_9999
reg cntReg = 32'h0;
reg blkReg = 1'b0;

always @()begin
    if(cntReg == CNT_MAX)begin
          cntReg = 32'h0
    end else begin
         cntReg = cntReg + 32'h1;
    end
end

always@()begin
    if(cntReg == CNT_MAX)begin

        blkReg = ~blkReg
    end
end


endmodule
```

编译翻译出verilog

```verilog
module Hello(
    input clock,
    input reset,
    output io_led,
);
reg [31:0] cntReg;
reg blkReg;

wire [31:0] _cntReg_T_1 = cntReg + 32'h1;
assign io_led = blkReg;

//when是使用always@(posedge)begin end进行翻译
//定义了初始值，这根据reset进行初始化
always @(posedge clk)begin
    if(reset)begin
        cntReg <= 32'h0;
    end else if(cntReg == 32'h2faf07f)begin
        cntReg <= 32'h0;
    end else begin
        cntReg <= _cntReg_T_1;
    end

    if(reset)begin
        blkReg <= 1'h0;
    end else if(cntReg == 32'h2faf07f)begin
        blkReg <= ~blkReg;
    end
end
```

hellochise的build.sbt如何写

```scala
//设置使用的scala的版本
scalaVersion := "2.12.13"

//scalac是一个将scala代码编译成.class的解释
//设置scalac解释scala代码的参数
scalacOption ++= Seq(
    "-feature",
    "-language:reflectiveCalls"
)


// Chisel 3.5
//添加chisel编译从插件
addCompilerPlugin ("edu.berkeley.cs" %                    
                   "chisel3 -plugin" % "3.5.0" cross
                    CrossVersion .full
                  )

//指定chisel依赖的库
libraryDependencies += "edu. berkeley .cs" %%
                        "chisel3" % "3.5.0"

libraryDependencies += "edu. berkeley .cs" %%
                       " chiseltest " % "0.5.0"
```

#### 编码器

```scala
b := "b00".U
switch(a) {
    is ("b0001".U) { b := "b00".U }
    is ("b0010".U) { b := "b01".U }
    is ("b0100".U) { b := "b10".U }
    is ("b1000".U) { b := "b11".U }
}
```

#### 计数器

```scala
//定义一个加法器
class Adder extends Module{
    val io = IO(new Bundle{
                            val a = Input(UInt(8.W))
                            val b = Input(UInt(8.W))
                            val y = Output(UInt(8.W))
                           }
               )

    io.y := io.a + io.b
}

//定义一个寄存器模块
class Register extends Module{
    val io = IO(
                new Bundle{
                           val d = Input(UInt(8.W))
                           val q = Output(UInt(8.W))
                          }
               )
    val reg = RegInit(0.U)
    reg  := io.d
    io.q := reg
}
//定义一个计数器
class Count10 extends Module{
        val io = IO(
                    new Bundle{
                                val dout = Output(UInt(8.W))
                              }
                  )


        val add = Module(new Adder() )
        val reg = Module(new Register() )

        //获取寄存器的输出
        val count = reg.io.q

        //给加法器设置操作数
        add.io.a := 1.U
        add.io.b := count
        val result = add.io.y
        // 循环设置
        val next  = Mux(count === 9.U, 0.U,result)
        //寄存器设置输入值
        reg.io.d := next
        //输出计数器的值
        io.dout := count
}
```

#### ALU模块

```scala
class Alu extends Module{
    val io = IO(new Bundle{
                            val a  = Input(UInt(16.W))
                            val b  = Input(UInt(16.W))
                            val fn = Input(UInt(2.W))
                            val y  = Output(U)
                          }
                )
    //设置默认输出    
    io.y := 0.U

    //alu计算
    switch(io.fn){
        is(0.U) { io.y := io.a + io.b }
        is(1.U) { io.y := io.a - io.b }
        is(2.U) { io.y := io.a | io.b }
        is(3.U) { io.y := io.a & io.b }
    }
}
```

#### 状态机

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240521160700.png)

输入的信号中：coffee>idea>pressure,即如果3个信号同时来则采用coffee的刺激进行状态转移

```scala
// 计算下一个状态的类
class GradLife extends Module {
  val io = IO(new Bundle {
    val state = Input(UInt(2.W))
    val coffee = Input(Bool())
    val idea = Input(Bool())
    val pressure = Input(Bool())
    val nextState = Output(UInt(2.W))
  })

  //这个枚举我不会
  val idle :: coding :: writing :: grad :: Nil = Enuio.nextState := idle
  io.nextState := idle

  when (io.state === idle) {
    when      (io.coffee) { io.nextState := coding } 
    .elsewhen (io.idea) { io.nextState := idle }
    .elsewhen (io.pressure) { io.nextState := writing }
  } .elsewhen (io.state === coding) {
    when      (io.coffee) { io.nextState := coding } 
    .elsewhen (io.idea || io.pressure) { io.nextState := writing }
  } .elsewhen (io.state === writing) {
    when      (io.coffee || io.idea) { io.nextState := writing }
    .elsewhen (io.pressure) { io.nextState := grad }
  })


}


// Test
test(new GradLife) { c =>
  // verify that the hardware matches the golden model
  for (state <- 0 to 3) {
    for (coffee <- List(true, false)) {
      for (idea <- List(true, false)) {
        for (pressure <- List(true, false)) {
          c.io.state.poke(state.U)
          c.io.coffee.poke(coffee.B)
          c.io.idea.poke(idea.B)
          c.io.pressure.poke(pressure.B)
          c.io.nextState.expect(gradLife(state, coffee, idea, pressure).U)
        }
      }
    }
  }
}
println("SUCCESS!!") // Scala Code: if we get here, our tests passed!
```

#### 可配置端口数的模块

```scala
class MyRoutingArbiter(numChannels: Int) extends Module {
    val io = IO(new Bundle {
                          val in = Vec(numChannels, Flipped(Decoupled(UInt(8.W))))
                          val out = Decoupled(UInt(8.W))
                         }
             )

// YOUR CODE BELOW
    io.out.valid := io.in.map(_.valid).reduce(_ || _)
    val channel = PriorityMux(
                              io.in.map(_.valid).zipWithIndex.map { 
                                                                    case (valid, index) => (valid, index.U)
                                                                  }
                              )
    io.out.bits := io.in(channel).bits
    io.in.map(_.ready).zipWithIndex.foreach { 
                                                case (ready, index) =>
                                                ready := io.out.ready && channel === index.U
                                            }
}
```

#### 多`.scala`文件项目

创建一个module.scala文件，这个文件是属于module的包里面的文件

```scala
package module
import chisel3._ 

class CompA extends Module{
    val io = IO(new Bundle{
                            val a = Input( UInt(8.W) )
                            val b = Input( UInt(8.W) )
                            val x = Output( UInt(8.W) )
                            val y = Output( UInt(8.W) )
    })
    io.x := io.a + io.b
    io.y := io.a - io.b

}

class CompB extends Module{
    val io = IO( new Bundle{
        val in1 = Input ( UInt(8.W) )
        val in2 = Input ( UInt(8.W) )
        val out = Output( UInt(8.W) )
    })
    io.out := io.in1 & io.in2
}
```

创建一个`ComPCM.scala`文件，该文件也是属于`moudle`的包里面的文件

```scala
package  module
import chisel3._ 

class CompC extends Module{
    val io = IO(new Bundle{
        val in_a = Input( UInt(8.W) )
        val in_b = Input( UInt(8.W) )
        val in_c = Input( UInt(8.W) )
        val out_x = Output( UInt(8.W) )
        val out_y = Output( UInt(8.W) )
    })

    //创建模块A和模块B
    val compA = Module( new CompA() )
    val compB = Module( new CompB() )

    //链接A
    compA.io.a :=  io.in_a
    compA.io.b :=  io.in_b
    io.out_x   := compA.io.x

    //连接B
    compB.io.in1 := compA.io.y 
    compB.io.in2 := compA.io.x 
    io.out_y := compB.io.out 
}
```

创建一个`main.scala`文件，引入自己写的`module`的包,调用该包里面的`CompC`模块，然后生成调用`emitVerilog`生成`verilog`代码

```scala
import module._
import chisel3._

object MyModule extends App{
    emitVerilog( new CompC(), Array("--target-dir", "generated") )
}
```
