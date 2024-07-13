（2）自动创建伴生对象，同时在里面实现子apply方法，使得在使用的时候可以不直接显示地new对象

（3）伴生对象中同样会实现unapply方法，从而可以将case class应用于模式匹配

（4）实现自己的toString、hashCode、copy、equals方法

除此之外，cass class与其它普通的scala类没有区别

case class CaseDemo(name:String)
object demo {
  def main(args: Array[String]): Unit = {
    val caseDemo1 = new CaseDemo("case")
    val caseDemo2 = CaseDemo("case")
    println(caseDemo1 == caseDemo2) //true
    println(caseDemo1.hashCode()) //612386822
    println(caseDemo2.hashCode()) //612386822
  }

————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：https://blog.csdn.net/LINBE_blazers/article/details/89042635

#### 当一个类被声明为case class的时候，

#### Scala会自动做下面几件事情

#### 缩减报错

tmd，这个玩意缩进不对也会错误

![](https://gitee.com/zz--yy/mark-down-image/raw/master/DdsFirFft/20240517222257.png)

## def的报错

定义在的等号是必须写的，经常错误

返回值不用写return，直接一行将变量就表示返回了，因为这里`=`就表示返回

```scala
package com.atguigu.scala.test

import scala.beans.BeanProperty
import scala.compiletime.uninitialized

class Person{
    var name :String = "bobo"
    //var age : Int = _ //_表示给属性一个默认值
    var age : Int =  uninitialized
    //@BeanProperty var sex :String = "男"
    var sex :String = "男"
    def setSex(sex_i :String) ={
        sex = sex_i
    }
    def getSex():String ={
        sex //直接写要返回值的值，就可以表示返回的是sex
    }
}

object Person{
    def main(args :Array[String] ) :Unit = {
        var person = new Person()
        println(person.name)
        //这个调用必须带()即person.getSex()格式，如果是：person.getSex会报错
        println(person.getSex()) 

        person.setSex("女")
        println(person.getSex())
    }
}
```

#### Object 和Class的区别

Class就是C++和python和java中类的概念，是一样的，

Object是一个直接定义了一个实例，这么描述吧：Object也是创建了一个类，但是不用它自己创建实例，即不能

```
Object Ob1{

};
val objetc_instace1 = new Ob1();这样是不可以
```

但是可以使用Class创建实例，

哪Object有什么用呢？？

+ 定义一个Object 就是直接定义了一个实例，你你可以直接使用Ob1,即Ob1就是一个变量，不过它是一个和类的结构相识的变量，所以Object称为单例实例

+ 如果某些方法和变量是一个整体，或者是一块的，或者是一个分类的，你可以创建一个Object实例，让后将方法和变量放在里面，这里你就可以将一些方法变量进行归类

+ Object还有一个好处就是，可以方便使用，你定义一个Class后，只有通过`new`创建一个实例 后，在调用这个实例，才能执行代码，而Obect是一个声明完后，就可以直接调用这个Object然后去执行它

#### 伴生类和伴生对象是什么

当我使用Class创建的类的名称和Object创建的对象（实例）的名称相同，这我们称：Class创建的类为Object创建的实例的伴生类，Object创建的对象（实例）为Class创建的类的伴生对象

eg:

```scala
//这是object创建的Student类的伴生类
class Student(name :String,age :Int){
    def printInfo = println(s "New a student witth
name:${name},age:${age}.")
")
}


//这是class 创建的Student类的伴生对象
object Student{
    def apply(name :String,age :Int) = new Student(name,age)
}


//这调用object的Student中apply方法，创建一个新实例
val stu = Student("zhangsan",20) 
```

这里伴生的好处可以相互访问对方的属性和方法，

#### 方法和函数的区别

方法是：在类里面使用`def`定义的函数

函数是：在类外面使用`def`定义的

#### Trait特质的作用

这样比喻：

可以用一个类来表示人类的基本属性，但是人与人之间还是又区别的，如果让表示人类的这个类包含这是特殊群体才有的特性是很不合理的，我们将人类按照特性划分成不同群体，用一个Trait的东西描述同一类群体的特点，

一个人是可能属于多个群体的，所以可用类表示它是人类，再用with混入多个群体的特质，这样就可以完美的描述一个人了。这就是为什么我们要引入特质这个概念

```scala
trait 特质名{
    trait主体
}
```

#### 类如何混入特质Trait

##### 类没有继承父类

没有父类的类可以直接使用extend混入第一个特质

```scala
class 类名 extends 特质1 with 特质2 with 特质3
```

##### 类有继承父类

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

#### Array方法的使用

#### 枚举中Value方法使用

在 Scala 中，`Enumeration` 类提供了一个简便的方法来定义枚举类型。`Value` 是 `Enumeration` 类中的一个方法，用于创建枚举值。以下是对 `Value` 方法及其作用的详细解释：

### 枚举的定义和 `Value` 的使用

Scala 的 `Enumeration` 类使定义一组命名常量（枚举）变得简单。每个枚举值都是通过调用 `Value` 方法创建的。

#### 代码示例

```
object Color extends Enumeration {
  val RED, YELLOW, BLUE = Value
}
```

在这个例子中：

1. **`object Color extends Enumeration`**:
   
   - 定义一个名为 `Color` 的枚举对象，继承自 `Enumeration` 类。

2. **`val RED = Value(1, "red")`**:
   
   - 定义了一个名为 `RED` 的枚举值。
   - `Value(1, "red")` 创建了一个枚举值，其中 `1` 是枚举值的唯一ID，`"red"` 是枚举值的名称。

#### `Value` 方法的作用

- **创建枚举值**：`Value` 方法用于创建枚举中的单个值。
- **分配唯一ID**：可以给每个枚举值分配一个唯一的整数ID（如 `1`）。
- **设置名称**：可以给每个枚举值分配一个字符串名称（如 `"red"`）。

### `Value` 方法的详细用法

1. **无参数用法**：
   
   - 如果不提供参数，`Value` 方法会自动分配一个从 `0` 开始的唯一ID，并将枚举值的名称设为其变量名。
   
   scala
   
   复制代码
   
   `object Color extends Enumeration {   val RED, YELLOW, BLUE = Value }`
   
   在这种情况下，`RED` 的ID是 `0`，`YELLOW` 是 `1`，`BLUE` 是 `2`，且它们的名称分别为 `"RED"`、`"YELLOW"` 和 `"BLUE"`。

2. **带ID和名称的用法**：
   
   - 提供ID和名称，可以显式地定义每个枚举值的ID和名称。
   
   ```scala
   object Color extends Enumeration {
     val RED = Value(1, "red")
     val YELLOW = Value(2, "yellow")
     val BLUE = Value(3, "blue")
   }
   ```

### 完整示例

结合上面的解释，这里是一个完整的示例，展示了如何使用 `Enumeration` 和 `Value` 方法：

```scala
object Color extends Enumeration {
  val RED = Value(1, "red")
  val YELLOW = Value(2, "yellow")
  val BLUE = Value(3, "blue")
}

object Test {
  def main(args: Array[String]): Unit = {
    println(Color.RED)         // 输出: red
    println(Color.YELLOW)      // 输出: yellow
    println(Color.BLUE)        // 输出: blue

    // 访问枚举值的ID
    println(Color.RED.id)      // 输出: 1
    println(Color.YELLOW.id)   // 输出: 2
    println(Color.BLUE.id)     // 输出: 3

    // 通过ID查找枚举值
    println(Color(1))          // 输出: red
    println(Color(2))          // 输出: yellow
    println(Color(3))          // 输出: blue
  }
}
```

### 总结

`Value` 方法在 Scala 的 `Enumeration` 类中用于定义枚举值。它可以接收一个整数ID和一个字符串名称，从而创建一个枚举值。这样，枚举中的每个值都可以有一个唯一的ID和一个描述性名称，使得代码更加清晰和易于维护。

# Scala 中的应用类（App Trait）

在 Scala 中，应用类（App Trait）是一种简便的方式来创建可执行程序。通过继承 `App` 特质，可以避免显式地定义 `main` 方法。使用 `App` 特质时，所有在对象构造时的代码都会被执行。即:有一个继承了`App`类的实例，就可以不用创建main方法就能运行了，因为App会自己创建一个main方法

## `App` 特质

`App` 特质提供了一个便捷的方法来定义包含 `main` 方法的应用程序。继承 `App` 特质的对象会自动生成 `main` 方法，并且构造对象时的所有代码都会在 `main` 方法中执行。

### 使用示例

以下是一个简单的使用 `App` 特质的示例：

```scala
object HelloWorldApp extends App { println("Hello, world!") }
```

在这个示例中：

- `HelloWorldApp` 对象继承了 `App` 特质。
- `println("Hello, world!")` 语句会在程序启动时自动执行。

### 对比传统的 `main` 方法

通常情况下，我们需要显式地定义 `main` 方法来创建一个可执行的 Scala 程序：

```scala
object HelloWorld { 
    def main(args: Array[String]): Unit = {
         println("Hello, world!")
     } 
}
```

在这个示例中：

- `HelloWorld` 对象包含一个 `main` 方法，这是程序的入口点。
- `main` 方法必须显式地定义参数 `args: Array[String]` 和返回类型 `Unit`。

### 使用 `App` 特质的好处

- **简化代码**：通过继承 `App` 特质，可以减少代码的样板代码，不需要显式定义 `main` 方法。
- **直观明了**：构造对象时的代码会自动执行，使得程序逻辑更加直观。

### 使用 `App` 特质的注意事项

- **适用于简单程序**：`App` 特质适用于简单的程序和脚本。如果程序逻辑复杂，建议显式定义 `main` 方法。
- **执行顺序**：构造对象时的代码会按照书写顺序执行，这与显式定义 `main` 方法的执行顺序一致。

### 示例

下面是一些使用 `App` 特质的示例：

#### 简单示例

```scala
object SimpleApp extends App { 
println("This is a simple Scala application.") 
}
```

运行这个程序时，控制台会输出：

```csharp
This is a simple Scala application.
```

#### 带参数的示例

scala

```scala
object ArgsApp extends App { 
    if (args.length > 0){ 
        println(s"Arguments: ${args.mkString(", ")}")
     } else { 
        println("No arguments provided.") 
    } 
}
```

运行这个程序时，可以传递参数：

```shell
scala ArgsApp.scala arg1 arg2
```

控制台会输出：

```makefile
Arguments: arg1, arg2
```

#### 多行代码示例

```scala
object MultiLineApp extends App {

    println("Starting application...")

    for (i <- 1 to 3) { 
        println(s"Count: $i") 
    }

    println("Application finished.") 
}
```

运行这个程序时，控制台会输出：

```makefile
Starting application... Count: 1 Count: 2 Count: 3 Application finished.
```

### 总结

- `App` 特质提供了一种简便的方式来创建 Scala 应用程序。
- 继承 `App` 特质的对象会自动生成 `main` 方法，并在构造对象时执行所有代码。
- 使用 `App` 特质可以简化代码，特别适用于简单的程序和脚本。
- 对于复杂的程序，建议显式定义 `main` 方法，以保持代码的可读性和可维护性。

#### scala的lazy属性

变量定义的时候添加了lazy属性，表示这个变量在要使用时候，才把定义时候的值给他

```scala
import scala.io._ 
def read = StdIn.readInt() 
lazy val first = read 
lazy val second = read 
if (Math.random() < 0.5) 
 second 
println(first - second)
```

read()函数从控制台读取一个 Int 值。我们调用了该函数两次，但将调用的结果分别
赋值给了 first 和 second 两个变量。因为这两个变量都被声明为 lazy，所以它们此时都不会绑定到它们的值。只有在first被使用的时候才会绑定值

lazy 关键字只能用于修饰不可变变量 val，而不能用于修饰可变变量 var。

#### package object zzq 保对象zzq的定义变量可以供package zzq中的def，class等等使用

#### Class MyList[ T<: ClassName ] 泛型

定义泛型的作用是方便固定传入参数的类型范围，

#### 当一个类被声明为case class的时候

1. 会自动创建半生对象，同时在里面实现子apply
   
   ```
   
   ```

#### `(x:Int) => {函数体}`匿名函数

`x`：表示输入参数类型；

`Int`:表示输入参数类型

`函数体`：表示具体代码逻辑

##### 匿名函数原则

1. 参数类型可以省略，会根据形参进行自动推到



#### val country :String = "China" 这种创建方法忘记了



#### `apply`方法

在伴生类中定义了`apply()`方法可以不用使用`new class_name ( )`方式创建类变量

```scala
class Person private(cName :String){
    var name :String = cName
}

object Person{
    
    //创建了一个不用传入参数的创建类变量的函数
    def apply(): Person ={
        println("apply空参被调用“)
        new person("xx") //返回一个cName是xx的Person类创建的实例
    }

    //创建了一个传入参数为：name的创建类变量的函数
    def apply(name :String) :Person = {
        println("apply 有参被调用")
        new Person(name)
        }
}

object Test{
    def main(args :Array[String]) :Unit={
        val p1 = Person()
        println("p1.name=" + p1.name)
        
        val p2 = Person("bobo")
        println("p2.name=" + p2.name)
    }
}
```
