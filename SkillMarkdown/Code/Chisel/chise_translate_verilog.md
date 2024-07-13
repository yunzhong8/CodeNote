#### helloWord

1. 创建一个文件

2. 写入下面内容
   
   ```scala
   package intro
   
   import chisel3._
   
   class HelloWorld extends Module{
       val io = IO (new Bundle{})
       printf ("hello world \n")
   }
   ```
   
   import circt.stage.ChiselStage 
   
   object VerilogMain extends App{
   
       ChiselStage.emitSystemVerilog(new HelloWorld)
   
   }

3. 在linux终端输入`sbt`

```bash
sbt
run-main intro.VerilogMain
```

    或者一条命令

    

```shell
sbt 'runMain intro.VerilogMain'
```

然后就可以在当前目录看到HelloWord.v这个文件

```verilog
`ifndef PRINT_COND_
    `ifdef PRINT_COND
        `define PRINT_COND_ (`PRINT_COND)
    `else
        `define PRINT_COND_ 1
    `endif 
        `define 
`endif


module HelloWorld(
    input clock,
    input reset
);
    `ifndef SYNTHESIS
        always@(posedge clock)begin
            if( (`PRINF_COND_) & ~rest )
                $fwrite(32'h8000_0002,"hello world\n");
        end
    `endif
endmodule
```

###### 指定输入目录

```bash
sbt 'runMain' intro.HelloWorld --target-dir  buildstaff --top-name HelloWord'
```

`--target-dir`指定生成的.v文件存放的目录是：buildstaff

`--top-name`   生成的.v文件的名称是HelloWord
