#### log2Ceil---log2函数

[chisel 6.4.0 - chisel3.util.log2Ceil (chisel-lang.org)](https://www.chisel-lang.org/api/latest/chisel3/util/log2Ceil$.html)

这个函数是用来计算`log2(参数)`

所在库：`chisel3.util`

```scala
log2Ceil(1)  // returns 0
log2Ceil(2)  // returns 1
log2Ceil(3)  // returns 2
log2Ceil(4)  // returns 2
```

#### ListLookup

用于从列表中查找对应的值

`chisel3.util`

useage

```
ListLookup(键，查找的默认返回值，查找的键值对数组)
```

```scala
ListLookup(2.U, //address for comparison
           List(10.U,11.U,12.U),
           Array(BitPat(2.U) -> List(20.U,21.U,22.U)
                 BitPat(3.U) -> List(30.U,31.U,32.U)
        )
```

#### BitPat

用于进行匹配，这是可以忽略某些位的匹配

```scala
"b10101".U === BitPat("b101??")//evaluates to true.B
"b10111".U === BitPat("b101??")//evaluates to true.B
"b10001".U === BitPat("b101??")//evaluates to false.B
```
