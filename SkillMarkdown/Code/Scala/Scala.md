###### 代码规范

+ 运算符两侧习惯各加入一个控制

+ 一行不超80个字符

#### 两种属性

`var`和`val`，被`val`修饰的变量不允许在后续修改，`var`修饰的变量可以在后续修改

```scala
var var_v =12
val val_v = 11
val_v = 12 //这个会报错
var_v = 11 //不会报错
```

#### 变量定义格式

```scala
属性 变量名 [:变量属性] = [初始值|表达式]
```

scala先写$变量名称$在$写类型$，这样设计的的原因是因为：类型可以通过解释推到出来，所以个类型漏写也不会咋样

能使用常量属性，就不要使用变量属性

###### 定义变量必须初始值

```scala
val x :Int
```

这样会报错的，因为没有指定初始值

#### 标识符的命名方式

标识符指的是：变量名、函数名等等

可以用操作符（`+，-，*，/#！`），字母，下划线开头

#### 字符串可以使用`+`进行运算

```scala
var name:String = "ja"+"jin"
```

#### `""" """`可以包含多行字符串

在每行开头添加`|`可以保证每行都对齐

```
val s = """
    |select
    | name,
    |    age
"""
```

## 类型结构

所有类型都继承`Any`类,`Any`的上一层是两种类型

+ `AnyVal`

+ `AnyRef`

即再高层的类，要么继承`AnyVal`要继承`AnyRef`，而这两个类都继承`Any`

#### AnyVal----数值类型

##### Byte

##### Short

##### Int

##### Long

##### Float

##### Double

##### Char

##### Boolean

#### AnyRef-----引用类型

#### Uint,Null,Noting特殊类型

###### Uint等同其他语言的void

表示没有值

##### Noting

可以说是任何类型

#### 数值类型转换

##### 隐式转换（自动转换）

转换规则：

+ 小精度可以按照下面规则自动转成大精度

+ 大精度赋值给小精度会报出语法错误

+ `byte`，`short`和`char`之间不会相互自动转换
  
  + 因为`byte`，`shor`，`char`他们可以相互计算，可以都临时转成int类型，然后计算完成后转为原来类型，在赋值回去

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240517175222.png)

##### 强制类型转换

要想使用大精度转为小精度则要使用强制类型转换函数

eg:

```scala
var num :Int = 2.4.toInt
```

将`float`类型强制转为`int`类型

##### 数值类型和字符串类型转换

###### 基本类型转为String类型

将基本数值类型的值+””即可

```scala
var str1 : String = true + ""
var str1 : String = 4.5 + ""
```

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240517180311.png)

###### 将String类型转为数值类型

使用`s1.toInt`,`s1.toFloat`,`s1.toDouble`

```scala
]object TestStringTransfer {
 def main(args: Array[String]): Unit = {
 //（1）基本类型转 String 类型（语法：将基本类型的值+"" 即可）
 var str1 : String = true + ""
 var str2 : String = 4.5 + ""
 var str3 : String = 100 +""
 //（2）String 类型转基本数值类型（语法：调用相关 API）
 var s1 : String = "12"
 var n1 : Byte = s1.toByte
 var n2 : Short = s1.toShort
 var n3 : Int = s1.toInt
 var n4 : Long = s1.toLong
 }
}
```

在将 String 类型转成基本数值类型时，要确保 String 类型能够转成有效的数据，比如我
们可以把"123"，转成一个整数，但是不能把"hello"转成一个整数。
var n5:Int = "12.6".toInt 会出现 NumberFormatException 异常。

## 运算符

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240517180524.png)

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240517180627.png)

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240517180642.png)

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240517180657.png)

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240517180724.png)

#### Scala 运算符本质

在 Scala 中其实是没有运算符的，所有运算符都是方法。
1）当调用对象的方法时，点.可以省略
2）如果函数参数只有一个，或者没有参数，()可以省略

```scala
object TestOpt {
 def main(args: Array[String]): Unit = {
 // 标准的加法运算
 val i:Int = 1.+(1)
 // （1）当调用对象的方法时，.可以省略
 val j:Int = 1 + (1)
 // （2）如果函数参数只有一个，或者没有参数，()可以省略
 val k:Int = 1 + 1

 println(1.toString())
 println(1 toString())
 println(1 toString)
 }
}
```

## 分支和循环

#### if的分支和C语言的语法一样

#### Java 中的三元运算符可以用 if else 实现

如果大括号{}内的逻辑代码只有一行，大括号可以省略。如果省略大括号，if 只对最近
的一行逻辑代码起作用。

```scala
object TestIfElse {
 def main(args: Array[String]): Unit = {
 // Java
// int result = flag?1:0
 // Scala
 println("input age")
 var age = StdIn.readInt()
 val res:Any = if (age < 18) "童年" else "成年"
"不起作用"
 println(res)
 }
}
```

#### switch语法没有

#### for由很多高级不同用法

Scala 也为 for 循环这一常见的控制结构提供了非常多的特性，这些 for 循环的特性被称
为 for 推导式或 for 表达式。
4.4.1 范围数据循环（To）
1）基本语法

```scala
for(i <- 1 to 3){
 print(i + " ")
}
println()
```

（1）i 表示循环的变量，<- 规定 to

（2）i 将会从 1-3 循环，前后闭合
2）案例实操
需求：输出 5 句 "宋宋，告别海狗人参丸吧"

```scala
object TestFor {
 def main(args: Array[String]): Unit = {
 for(i <- 1 to 5){
 println("宋宋，告别海狗人参丸吧"+i)
 }
 }
}
4.4.2 范围数据循环（Until）
1）基本语法
for(i <- 1 until 3) {
 print(i + " ")
}
println()
```

（1）这种方式和前面的区别在于 i 是从 1 到 3-1
（2）即使前闭合后开的范围
2）案例实操
需求：输出 5 句 "宋宋，告别海狗人参丸吧"

```scala
object TestFor {
 def main(args: Array[String]): Unit = {
 for(i <- 1 until 5 + 1){
 println("宋宋，告别海狗人参丸吧" + i)
 }
 }
}
4.4.3 循环守卫
1）基本语法
for(i <- 1 to 3 if i != 2) {
 print(i + " ")
}
println()
```

说明：

##### Match/Case 语句

## 函数的语法也是很大区别

## 包

为什么要引入包这个概念呢？ 是因为出现了层级关系，因为面向对象就是一层包含一层的，一层继承一层的，这里的包的含义也有些类似于c语言的头文件的含义，

c语言中：一个同文件中可以包含多个函数，同文件可以包含头文件，引入头文件就可以使用头文件中的方法，

这里的包:可以包含多个类，多个方法，或则包，引入包就可以调用包中的类型，方法，等等

##### 命名规则

只能包含数字，字母，下划线

#### 如何创建包

##### way1(java方式)

```scala
package 包名1

object Test13_Lazy{
    def main(args :Array[String]) :Uint ={
        val result :Int = sum(13,47)
        println("1.函数调用")
        println("2.result = " + result)
        println("4.result = " + result)
    }

    def sum(a :Int, b :Int) :Int ={
        println("3.sum调用")
        a + b
    }
}
```

这个包里面就有一个名叫：Test13_Lazy的类，这个类的定义了两个方法

只要申明的包名是一样的就在同一个包里面,即不同文件中的

`package 包名`这行代码的包名是相同的则表示这里文件中的定义都是在同一个的包里面

这个方法，一个源文件只能定义一次，即文件中只能有一句

`package 包名`

表示包的递进关系

```scala
package 包名1.包名2
```

##### way2(花括号递进关系)

将`package com.atguigu.scala`展开就是这样的

```scala
package com{
    package atguigu{
        package scala{

        }
    }
}
```

这种方法可以在一个文件中定义多个包，子包可以直接访问父包中的内容，不用导包，即

eg:

```scala
//用嵌套风格定义包
package com{
    //在外层包中定义单例对象
    object Outer{
        var out:String = "out"
    }    
    //这里是无法调用子包scala中的Inner这个对象的下


    package atguigu{
        package scala{
            //内层包中定义单例对象
            // scala这包中代码可以直接调用父包中的Outer对象
            object Inner{
                def main(args: Array[String]) :Uint = {
                    println(Outer.out)
                    Outer.out = "outer"
                    println(Outer.out)
                }       
            }            
        }        
    }
}
```

```scala
//用嵌套风格定义包
package com{
    //导入子包
    import com.atguigu.scala.Inner

    //在外层包中定义单例对象
    object Outer{
        var out:String = "out"
        def main(args :Array[String]): Uint ={
            println(Inner)
        }
    }    
    package atguigu{
        package scala{
            //内层包中定义单例对象
            // scala这包中代码可以直接调用父包中的Outer对象
            object Inner{
                val in: String = "in"
                def main(args: Array[String]) :Uint = {
                    println(Outer.out)
                    Outer.out = "outer"
                    println(Outer.out)
                }       
            }              
        }        
    }
}


//同文件定义多个包
package aaa {

    package bbb{
        //在当前包中导入其他包中的实例
        import com.atguigu.scala.Inner
        object Test01_Package{
            def main(args :Array[String]) :Unit = {
                println(Inner) //调用其他包的实例
            }
        }
    }

}
```

#### 导入包

##### 将包的内容全导入

格式：`import 包名._`

```scala
import chisel3._
```

#### 导入包的一个元素

格式：`import 包名.元素名`

```scala
import chisel3.
```

##### 导入包的多个元素

格式：`import 包名.{元素1，元素2}

```scala
import chisel3.{}
```

## 类

```
一个Scala源文件可以包含多个类，一个类没有设置修饰符，则默认是`publi`,表示这个类具有公有可见性

#### 类属性的访问权限

1. `scala`中属性和方法的默认访问权限为`public`,但`Scala`中无`public`关键字

2. `private`为私有权限，只在类的内部和伴生对象中使用

3. `protected`为受保护权限，`Scala`中受保护权限比`java`更为严格，同类、子类可以访问，同包无法访问

4. private [包名]增加包访问权限，包名下的其他类也可以使用





#### 类的定义格式

##### 类的最基本的定义（重要）

```scala
[修饰符] class 类名{
    类体
}

//这种是创建类的实例是需要传入参数的类型
[修饰符] class 类名 [(parameter1_name:parameter_type,
    parameter2_name:parameter2_type)]{
    类体
}


[修饰符] class 类名 extern 父类名{
    类体
}

对类体进行展开

[修饰符] class 类名{
   [修饰符] var|val 属性1名称 [:类型] = 属性
   [修饰符] var|val 属性2名称 [:类型] = 属性

   def 方法名(参数1名：参数类型，参数1名：参数类型) ：返回类型{
        方法体
    }
}
```

修饰符：public，private

`Scala`的类继承只能继承一个，即类只能由一个父亲，不能继承多个父类

如果类没有申明父类的话，则默认父类是`AnyRef`

#### 创建对象的

##### 创建对象的格式

```scala
val|var 对象名 [:类型] = new 类型（）
```

```
class Person{
    var name : Sting = "canglaoshi"
}

object Person {
    def main(args:Array[String]:Uint) = {
        val person = new Person()
        person.name = "bobo"
        println(person.name)
    }
}
```

这里好奇怪啊，val修饰的`对象`(new一个类创建出的一个实例)，是可以先修改里面属性的值的，不能修改对象的内存地址，这tm是什么意思，

```scala
val v1 = 1 
//这种和new一个对象有什么区别吗，vl会被推导成Int类型
//所以个Int类型是什么意思？？？，=1的本质又是什么意思，
// 所以v1不能修改，但是直接定义的类创建出的val 对象可以修改属性
// 官方指定的类使用val修饰创建出的变量，不能修改        
```

#### 支持方法重载-----和c++，java类似

方法名相同，参数和返回值不同就是重载，

#### class 和 object的区别

#### 类的主构造器和辅助构造器

辅助构造器：

```scala
def this([parameter1_name:parameter1_type,parameter2_name:parameter2_type]){
    方法体
}
```

整个类就是一个主构造器，这些名为`this`就是辅助构造器，这些辅助构造器可以由多个，

为什么要由多个呢，因为你可以根据创建一个instance of classs时候传入不同的参数然后调用不同的this是初始化这个实例，说白了就是对构造函数进行重载，构造函数的名称固定为：`this`

#### 类型检查和转换

+ obj.isInstanceOf[T]:判断obj是不是T类型,这个T可以是自己定义的类，obj是一个类的实例

+ obj.asInstanceOf[T]:将obj强转成T类型，将实例obj强制转为类型T,

+ classOf 获取对象的类名

eg:

```scala
class Person{}

object Person{
    def main(args:Array[String]) :Uint = {
        val person = new Person

        //(1)判断对象是否为某个类型的实例   
        val ball : Boolean = person.isInstanceOf[Person]

        if ( bool ){
            //(2)将对想转换为某个类型的实例
            val p1 :Person = person.asInstanceOf[Person]
            println(p1)
        }

        //(3)获取类的信息
        val pClass :Class[Person] = ClassOf[Person]
        println(PClass)

    }
}
```

#### 枚举类（继承Enumeration)和应用类（继承App）

枚举类：需要继承`Enumeration`

应用类：需要继承`App`

 eg:

```scala
object Test{
    def main(args :Array[String] ) :Uint = {
        println(Color.RED)
    }
}

//枚举类
object Color extends Enumeration{
    val RED    = Value(1,"red")
    val YELLOW = Value(2,"yellow")
    val BLUE   = Value(3,"bule")
}


//应用类
object Test20 extends App{
    println("xxxx");
}
```

## 抽象类

在 Scala 中，抽象类（abstract class）是一种可以包含未实现方法和字段的类。与 Java 中的抽象类类似，Scala 的抽象类不能被实例化，必须通过继承来使用。

### 抽象类的定义

抽象类的定义使用 `abstract` 关键字。抽象类可以包含具体的（已实现的）方法和抽象的（未实现的）方法。

#### 代码示例

以下是一个简单的抽象类示例

```scala
abstract class Animal {
  def sound(): String  // 抽象方法，没有实现

  def sleep(): Unit = {  // 具体方法，有实现
    println("Sleeping...")
  }
}
```

在这个示例中：

- `Animal` 是一个抽象类。
- `sound` 是一个抽象方法，没有实现。
- `sleep` 是一个具体方法，有实现。

### 继承抽象类

要使用抽象类，必须定义一个子类并实现所有抽象方法。

```scala
class Dog extends Animal {
  def sound(): String = "Woof"  // 实现抽象方法
}

object Test {
  def main(args: Array[String]): Unit = {
    val dog = new Dog()
    println(dog.sound())  // 输出: Woof
    dog.sleep()           // 输出: Sleeping...
  }
}
```

在这个示例中：

- `Dog` 类继承了 `Animal` 抽象类，并实现了 `sound` 抽象方法。
- 创建了一个 `Dog` 类的实例，并调用了 `sound` 和 `sleep` 方法。

### 抽象类的特点

- **不能被实例化**：抽象类不能直接创建实例。
- **可以包含具体方法和字段**：抽象类可以包含具体的方法和字段，实现一些通用的功能。
- **必须实现抽象方法**：子类必须实现所有抽象方法，除非子类也是抽象类。
- **可以有构造参数**：抽象类可以有构造参数，这一点与 Java 不同。

### 带构造参数的抽象类

```scala
abstract class Animal(name: String) {
  def sound(): String

  def sleep(): Unit = {
    println(s"$name is sleeping...")
  }
}

class Dog(name: String) extends Animal(name) {
  def sound(): String = "Woof"
}

object Test {
  def main(args: Array[String]): Unit = {
    val dog = new Dog("Buddy")
    println(dog.sound())  // 输出: Woof
    dog.sleep()           // 输出: Buddy is sleeping...
  }
}
```

在这个示例中：

- `Animal` 抽象类有一个构造参数 `name`。
- `Dog` 类继承了 `Animal` 抽象类，并在构造函数中传递 `name` 参数

### 抽象类与特质（Trait）的比较

在 Scala 中，抽象类和特质（trait）都有类似的功能，但它们有一些重要的区别：

- **多重继承**：Scala 允许一个类扩展多个特质，但只能继承一个抽象类。这使得特质在需要多重继承时更加灵活。
- **构造参数**：抽象类可以有构造参数，而特质不能直接有构造参数（可以通过 self-type 来间接实现）。
- **使用场景**：抽象类适用于层次结构明确、关系紧密的类，特质适用于混入特性和行为。

#### 抽象类示例

```scala
abstract class Animal {
  def sound(): String
}

class Dog extends Animal {
  def sound(): String = "Woof"
}
```

#### #### 特质示例

```scala
trait Animal {
  def sound(): String
}

class Dog extends Animal {
  def sound(): String = "Woof"
}
```

### 总结

- 抽象类提供了一种定义抽象方法和具体方法的方式。
- 抽象类不能被实例化，必须通过继承来使用。
- 抽象类可以有构造参数。
- 与特质相比，抽象类适用于需要单一继承和有构造参数的情况，而特质更灵活，可以用于多重继承。

通过这些示例和解释，可以更好地理解 Scala 中抽象类的使用及其特点。

#### Traits、Enum、Case classes、Object 使用环境

Trait:

traits是用于用于创建小的、逻辑分组的行为单元，它们通常写成def方法，但如果你愿意，也可以写成val函数无论哪种方式，它们都被写成纯函数，这traits稍后将组合成具体的对象

## 集合（如果要写更简单的代码这个必须了解）

#### Scala常见集合类型

+ List---- 有序的对象集合

+ Set-----无序的集合

+ Map----键值对字典

如果不指定使用的包名，那么在默认情况下：scala会使用不可变集合（不可变集合可以便于在多线程山运行）

##### 可变集合和不可变集合

+ scala不可变集合：即集合对象不可修改，每次修改就会返回一个新对象，而不会在原对象进行修改，类似于java中的String对象

+ 可变集合：这个集合可以直接对原对象进行修改，而不会返回新的对象，类型是java中的StringBuilder对象

#### 不可变集合

##### 不可变集合继承图

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240527200038.png)

##### IndexedSeq和LinearSeq的区别

`IndexedSeq`就是类似数字，`LinearSeq`就是类型链表

#### 可变集合

##### 可变集合继承图

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240527200334.png)

#### Array----- 数组

#####1. 指定数字元素类型和元素个数方式定义数组

```scala
val arral = new Array[Int](10)
```

+ `new` 是关键字表示：创建

+ `[Int]` 表示：数组的元素类型是`Int`，如果希望存放任意数据类型则指定`Any`

+ `(10)` 表示：数组的大小，确定后就不可以变化

eg

```scala
object TestArray{
    def main(args:Array[String]) :Unit ={
    //数组定义
    val arr01 = new Array [Int] (4)
    println(arr01.length) //4

    //数组赋值
    //修改某个元素的值
    arr01(3) = 10

    //采用方法的形式给数组赋值
    arr01.update(0,1)

    //遍历数组
    //查看数组
    println( arr01.mkString(",") )

    //普通遍历
    for(i <- arr01 ){//类似于python中for i in arr01 :
        println(i) 
    }

    //增加元素（由于创建的是不可以变数组，增加元素，其实是产生新的数组）
    //要采用：原数组 = 增加元素（原数组，新元素）这样的方式才行
    println(arr01)
    val ints :Array[Int] = arr01 :+ 5
    println(ints)
}
```

##### 2.直接定义数组的初始赋值的方式创建数组

+ 在定义数组时，直接赋初始值

+ 使用apply方法创建数组对象

```scala
val arr1 = Array(1,2)
```

eg：

```scala
object TestArray{
    def main(args :Array[String]) :Unit = {
        var arr02 = Array(1,3,"bobo")
        println(arr02.length)
        for( i <- arr02 ){
            println(i)
        }

    }
}
```

#### Seq

##### Vector

vector，中每一个元素是由对应的序列的，Vector解决了随机访问序列中数据的速度问题，对Vector的变量进行方随机访问它的元素是比较快的，  `indexedSeq`和`Vector`是同样的东西

```scala
scala> val x = IndexedSeq(1,2,3)
x: IndexedSeq[Int] = Vector(1,2,3)
```

###### 创建一个Vector序列

```scala
//通过设置初始值创建vec
val v = Vector("a","b","c") 
v(0) //"a" 通过索引进行访问vec的元素
v(1) //"b"


// 通过定义的方式创建
val a = Vector[String]() //等同 val a :Vector[Sting] = Vector()

//给vector添加元素
//因为Vector是不可以变数组，所以旧值+新值给一个新变量
val b = a ++ List("a","b") //等同value b= Vector("a","b")


val a = Vector(1,2,3)
val b = a ++ List(4,5) // b:Vector(1,2,3,4,5)
val c = b ++ Seq(6) // c:Vector(1,2,3,4,5,6)
```

#### Vec添加元素

+ `+：`是prepended的别名

+ `++:`是 prependedAll的别名

+ `:+`是appened的别名

+ `:++`是appednedAll的别名

`+`面对的是待添加的数据，`:`面对的是旧队列,数据在队列前，就表示在队列头部添加数据，`++`表示添加多个数据，`+`表示添加单个数据

```
var a = Vector(6)

//在头部加入1个元素
a = 5 +: a          // a:Vector(5,6)
a = a.prepended(4) // a:Vector(4,5,6)

//在头部加入多个元素
a = List(2,3) ++: a            // a:Vector(2,3,4,5,6)
a = a.prependedAll( Seq(0,1) ) // a:Vector(0,1,2,3,4,5,6)

var b = Vector(1)

// 在尾部加入1个元素
b = b :+ 2
b = b.appended(3)

//在尾部添加多个元素
b = b :++ List(4,5)
b = b.appendedAll( List(6,7) )
```

##### 修改Vec中的元素

###### update-----一次替换一个元素

可以使用Vector中的update方法去替换Vector中的元素，

```scala
val a = Vector(1,2,3)
//使用index直接表示那个参数是index，使用elem直接指示那个是元素
val b = a.updated(index = 0, elem = 10) //b:Vector(10,2,3)
//默认第一个为要修改的元素的序列，第二个元素为修改的元素值
val c = b.updated(1,20) // c: Vector(10,20,3)
```

###### patch------一次替换多个元素

使用`patch`方法去一次性替换多个元素

```scala
val a = Vector(1,2,3,4,5,6)

// a.pathc(要替换的Vector的起始地址，新的元素列表，替换元素个数)
val b = a.patch(0,List(10,20),2) //b:Vector(10,20,3,4,5,6)

//添加的时候同时删除元素
//这里等同是val b = a.patch(0,List(10,20,NULL),3)所以b就少了一个元素
val b = a.patch(0,List(10,20) , 3) // b:Vector(10,20,4,5,6)
val b = a.patch(0,List(10,20) , 4) // b:Vector(10,20,5,6)


val b = a.patch(2, List(30,40) , 2) //b:Vector(1,2,30,40,5,6)

//新添加元素
val b = Vector(10,20,30)
val b = a.patch(1,List(15) , 0) //b:Vector(10,15,20,30)
val b = a.patch(2,List(25) , 0) //b:Vector(10,20,25,30)
```

#### 列表 List

说明：

+ List 默认为不可变集合

+ 创建一个List(数据又顺序，可重复)

+ 遍历List

+ List增加数据

+ 空集合`Nil`

```scala
object TestList{
    def main(args: Array[String] ): Unit = {

        //创建一个List（数据有顺序，可重复）
        val list: List[Int] = List(1,2,3,4,3)

        //空集合Nil
        val list5 = 1::2::3::4::Nil

    //添加到第一个元素位置
    val list2 = list .+ :(5)

    //去指定数据
    println( list(0) )
    }
}
```

#### Set集合

说明

+ Set默认是不可变集合，数据无序

+ 数据不可以重复

 

```scala
object TestSet{
    def main(args :Array[String] ) :Unit ={
        //Set默认是不可变集合数据无序
        val set = Set(1,2,3,4,5,6)

        //数据不可重复
        val set = Set(1,2,3,4,3)//这个代码或报错

        //遍历集合
        for(x <- set1 ){
            println(x)
    }
    }
}
```

## printf函数的使用和C语言的几乎一样

## 方法的定义格式

#### def定义原则（重要）

+ return 可以省略，Scala会使用函数体最后一行代码作为返回值

+ if 函数体只有一行代码，可以省略花括号`{}`

+ 返回值类型 if 能够推断出来，那么可以省略
  
  + `:`和返回值类型一起省略

+ if有return，则不能省略返回值类型，必须指定

+ 如果函数明确声明`unit`，那么即使函数体使用`return`关键字也不其作用

+ Scala如果期望是无返回值类型，可以省略等号

+ 如果函数无参数，但是申明了参数列表，那么调用时，小括号，可加可不加

+ 如果函数没有参数列表，那么小括号可以省略，调用时小括号必须省略

+ 如果不关心名称，只关心逻辑处理，那么函数名（def)可以省略

```scala
object TestFunction{
    def main(args :Array[String]) :Uint={
        //(0) 函数标准写法
        def f (s :String) :String ={
            return s + "jinlian"
        }
        println("Hello")

        //至简原则：

        //(1)return 可以省略
        //scala会使用函数体的最后一行代码作为返回值
        def f1(s :String) :String = {
            s +"jinlian"
        }
        println(f1("Hello"))

       //(2)如果函数体只有一行代码，可以省略花括号
        def f2(s :String) :String = s +"jinlian"

       //(3)返回值类型如能推断出来，
       //那么可以省略（：和返回值类型一起省略）
        def f3(s :String) = s+ "jinlian"
        println(f3("Hello3"))

      // 如果有return,则不能省略返回值类型，必须指定
      def f4():String = {
            return "ximenqing4"
      }
      println(f4())
      //（5）如果函数明确声明uint
      // 那么即使函数体中使用return关键字也不起作用
      def f5():Uint = {
        return "dalan5"    
    }
    println(f5())

    //（6）Scala 如果期望是无返回值类型，可以省略等号
    //将返回值的函数称为过程
    def f6(){
        "dalang6"
    }
    println(f6())

    //(7) 如果函数没有参数，但是声明了参数列表，
    //那么调用时，小括号可以加也可以不加
    def f7() = "dalang7"
    println(f7())
    println(f7)    
    }
}
```

运行结果：

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240518110606.png)

我只能说6，这些语法精简的方式全爆错

```scala
def 方法名（参数列表）[:返回值类型]={
    方法体
}


def 方法名（[parameter1_name:parameter_type,
            parameter2_name:parameter2_type]）[:返回值类型]={
    方法体
}
```

这里`[:返回值类型]`表示可选项

eg:

```scala
class Person{
    def sum(n1:Int,n2:Int): Int = {
        n1 + n2
    }
}
def main(args:Array[String]):Unit = {
    val person = new Person()
}
```

#### 常见函数定义（重要）

```scala
package com.atguigu.chapter05


object TestFunctionDeclare{
    def main(args :Array[String]) :Unit = {
        // 函数1：无参 ，无返回值
        def test1() :Unit = {
            println("无参，无返回值")
        }
        test1()

        //函数2：无参数，有返回值
        def test2():String={
            return "无参数，有返回值"
        }
        test2();
        //函数3：有参数，无返回值
        def test3(s :String) :Unit={
            println(s) //这里直接写s也是可以的
        }
        test3("jinlina")

        //函数4：有参，有返回值
        def test4(s :String) :String={
            return s+"有参，有返回值"
        }
        println(test4()

        //函数5：
        def test5(name :String ,age :Int):Unit = {
            println(s"$name,$name")
        }
        test5("dalang",40)
    }
}
```

#### scala支持`,`之后换行

```scala
class ButtonWithCallbacks(val label :String,
    val clickedCallbacks: List[() => Unit]) extends Widget{

        require(clickedCallbacks != null,"Callback list can't be null")

    def this(label :String,clickedCallback:() => Uint)=this(label,List(clikedCallback))

    def this(label:String) = {
        this(label,Nil)
        println("Warning:button has no click callbacks!")
    }

    def click() = {
        clickedCallback.foreach(f => f())
        }

}
```

#### 特质 trait

##### Trait特质的作用

这样比喻：

可以用一个类来表示人类的基本属性，但是人与人之间还是又区别的，如果让表示人类的这个类包含这是特殊群体才有的特性是很不合理的，我们将人类按照特性划分成不同群体，用一个Trait的东西描述同一类群体的特点，

一个人是可能属于多个群体的，所以可用类表示它是人类，再用with混入多个群体的特质，这样就可以完美的描述一个人了。这就是为什么我们要引入特质这个概念

```scala
trait 特质名{
    trait主体
}
```

##### 类如何混入特质Trait

特质中如果有没有定义完成的抽象方法和抽象变量，在继承特质时候需要定义这些方法和属性

###### 类没有继承父类

没有父类的类可以直接使用extend混入第一个特质

```scala
class 类名 extends 特质1 with 特质2 with 特质3
```

###### 类有继承父类

```scala
class 类名 extends 父类名 with 特质1 with 特质2
```

```scala
trait PersonTrait{
    //(1)特质可以同时拥有抽象方法和具体方法
    //声明属性
    var name: String = _

    //抽象属性
    var age:Int

    //声明方法
    def eat(): Unit = {
        println("eat")
    }

    //抽象方法
    def say(): Uint
}

trait SexTrait{
    var sex :String
}


//(2)一个类可以实现/继承多个特质
//(3)所有java接口都可以当作scala特质使用]
class Teacher extends PersonTrait with java.io.Serializable{
    override def say(): Uint = {
        println("say")
    }
    override var age: Int = _
}


object TestTrait{
    def main(args:Array[String]): Unit = {
        val teacher = new Teacher
        teacher.say()
        teacher.eat()

        //(4)动态混入：可灵活的扩展类的功能
        //通过new创建变量的时候使用with混入特质
        val t2 = new Teacher With SexTrait{
            override var sex :String = "男"
        }

        //调用混入的trait的属性
        println(t2.sex)
    }
}
```

###### 动态混入特质

通过new创建变量的时候使用with混入特质就叫动态混入特质

```scala
object TestTrait{
    def main(args:Array[String]): Unit = {
        val teacher = new Teacher
        teacher.say()
        teacher.eat()

        //(4)动态混入：可灵活的扩展类的功能
        //通过new创建变量的时候使用with混入特质
        val t2 = new Teacher With SexTrait{
            override var sex :String = "男"
        }

        //调用混入的trait的属性
        println(t2.sex)
    }
}
```

#### 特质叠加

由于一个类可以混入多个trait，且trait中可以有具体的属性和方法。

如果混入的特质中具有相同的方法（方法名，参数列表，返回值均相同），比如会出现继承冲突问题，

冲突分为以下两种：

+ 一个类（Sub)混入的两trait（TraitA,TraitB)中具有相同的具体方法，且两个trait之间没有任何关系，解决这个冲突问题，直接在类（Sub)中重写冲突方法

+ 一个类（Sub）混入的连个trait(TraitA,TraitB)中具有相同的具体方法，且两个trait继承相同的trait(TraitC),即所谓的`砖石问题`，解决这类冲突问题，Scala采用`特质叠加`的策略

```scala
trait Ball{
    def describe(): String = {
        "ball"
    }
}

// Color 和 Category 都继承了Ball这个特质
trait Color extends Ball{
    override def describe():String = {
        "blue-" + super.describe()
    }
}


trait Category extends Ball{
    override def describe(): String = {
        "foot-" + super.describe()
    }
}

// MyBall继承了Category和Color两个特质，出现了describe（）方法冲突
class MyBall extends Category with Color{
    override def describe(): String = {
        "my ball is a " + super.describe()
    }
}


object TestTrait{
    def main(args:Arrray[String]) :Uint = {
        println(new MyBall().describe() )
    }
}
```

执行结果

```
my ball is a bule-foot-ball
```

##### 特质叠加的执行顺序

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240526204921.png)

就是按照继承属性执行，先继承的后执行

##### 特质叠加中指定继承的相同属性中继承是谁的特质

如果想调用某个指定的混入特质中的方法，可以增加约束：`super[]`,列如：

```scala
super[Category].describe() 表示专门调用的是Category的方法
```

#### 特质和抽线类的区别

1. 优先使用特质，一个类扩展·多个特质是很方便的，单却只能扩展一个抽象类

2. 如果你需要构造函数参数，使用抽象类，因为：抽象类可以定义带参数的构造函数，而特质步行（有无参构造）
