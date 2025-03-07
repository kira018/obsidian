# Java概述

## Java的特点 

- 平台无关性:通过jvm跨语言
- 面向对象
- ==内存管理:Java有自己的垃圾回收机制==
- 支持多线程。C++ 语言没有内置的多线程机制，因此必须调用操作系统的多线程功能来进行多线程程序设计，而 Java 语言却提供了多线程支持；

## JVM、JDK 和 JRE 有什么区别？ 

JVM是 Java 实现**跨平台的关键所在**，针对不同的操作系统，有不同的 JVM 实现。JVM 负责**将 Java 字节码转换为特定平台的机器码**，并执行。

==JRE是 **Java 运行时环境**==，包含了运行 Java 程序所必需的库，以及 Java 虚拟机（JVM）。

JDK是一套完整的 Java SDK（软件开发工具包），包括了 JRE 以及编译器（javac）、Java 文档生成工具（Javadoc）、Java 调试器等开发工具。为开发者提供了开发、编译、调试 Java 程序的一整套环境。

简单来说，JDK 包含 JRE，JRE 包含 JVM。

![img](https://cdn.nlark.com/yuque/0/2024/png/38443033/1716348552751-406f792d-f2b5-4b16-8118-3e2db193f468.png?x-oss-process=image%2Fformat%2Cwebp%2Fresize%2Cw_869%2Climit_0)

## 说说什么是跨平台性？原理是什么*

所谓跨平台性，是指 Java 语言编写的程序，一次编译后，可以在多个系统平台上运行。

实现原理：Java 程序是通过 Java 虚拟机在系统平台上运行的，只要该系统可以安装相应的 Java 虚拟机，该系统就可以运行 java 程序。

# 基础语法

## 数据类型

### 基本数据类型和引用数据类型的区别 *

1. 基本数据类型是**存储数据的简单类型**，而引用数据类型是**存储对象的引用或地址**。 
2. 基本数据类型是直接**存储在栈(stack)中**的，而引用数据类型**在栈中存储的是一个地址**，这个地址指向**堆(heap)中的实际数据**。 
3. 基本数据类型有8种：**byte、short、int、long、float、double、char、boolean**，而引用数据类型有**类(class)、接口(interface)、数组(array)、枚举(enum**)等。 
4. 基本数据类型的默认值是0或false，而引用数据类型的默认值是null。 

### 给Integer最大值+1，是什么结果？ *

当给 Integer.MAX_VALUE 加 1 时，会发生溢出，变成 Integer.MIN_VALUE。

这是因为 Java 的整数类型采用的是二进制补码表示法，溢出时值会循环。

- Integer.MAX_VALUE 的二进制表示是 01111111 11111111 11111111 11111111（32 位）。
- 加 1 后结果变成 10000000 00000000 00000000 00000000，即 -2147483648（Integer.MIN_VALUE）。

### long跟int可以互相转换吗

long->int,需要强转,会出现数据丢失或溢出的情况

int->long,直接转就行了

### 为什么使用bigdecimal不使用double

double容易出现精度丢失,使用bigdecimal可以避免

### 为什么要有包装类?不能直接使用基本数据类型吗

1. Java绝大部分方法都是用来处理类类型的对象的,比如ArrayList集合存储的数据就只能是类作为存储对象,int是存不了的
2. 在泛型种也只能使用引用类型,而不能使用基本数据类型

### 那为什么还要保留基本数据类型?

1. 读写效率:引用数据**对象的引用和本身是分开存储**的,而**基本数据类型直接存储数据本身**,所以基本数据类型效率跟高
2. 存储效率:integer占用16个字节而int只占用四个字节,所以基本数据类型占用更少

总结:基本数据类型在性能和占用空间方面都比引用数据类型优秀

## &和&&有什么区别？

俩个其实都是左右俩边都为true才行,但是&左边false右边还会进去计算而&&左边false右边就不会进去计算了

## 向上向下转型

向上转型就是创建一个子类对象，将其当成父类对象来使用。

```java
//语法格式：父类类型  对象名  =  new  子类类型()
Animal  animal  =  new Cat ()；
```

> 向上转型的优缺点

优点：让代码实现更简单灵活

缺点：不能调用到子类特有的方法

> 向下转型的概念

将一个子[类对象](https://so.csdn.net/so/search?q=类对象&spm=1001.2101.3001.7020)向上转型之后可以当成父类对象使用，若需要调用子类特有的方法，则需要**将父类对象再还原为子类对象**。这就称作向下转型。

> 向下转型的缺点

缺点：向下转型使用的比较少，而且不安全。如果转换失败，运行时就会抛异常。

> instanceof的使用

Java中为了提高向下转型的安全性，引入了**instanceof**。如果表达式为 **true**，则可以安全转换。

## 用最有效率的方法计算 2 乘以 8？ *

2 << 3。**位运算**，数字的二进制位左移三位相当于乘以 2 的三次方。

## 说说自增自减运算？看下这几个代码运行结果？ *

在写代码的过程中，常见的一种情况是需要某个整数类型变量增加 1 或减少 1，Java 提供了一种特殊的运算符，用于这种表达式，叫做自增运算符（++)和自减运算符（--）。

++和--运算符可以放在变量之前，也可以放在变量之后。

当运算符放在变量之前时(前缀)，先自增/减，再赋值；当运算符放在变量之后时(后缀)，先赋值，再自增/减。

例如，当 `b = ++a` 时，先自增（自己增加 1），再赋值（赋值给 b）；当 `b = a++` 时，先赋值(赋值给 b)，再自增（自己增加 1）。也就是，++a 输出的是 a+1 的值，a++输出的是 a 值。

用一句口诀就是：“符号在前就先加/减，符号在后就后加/减”。

> 看一下这段代码运行结果？

```java
int i  = 1;
i = i++;
System.out.println(i);
```

答案是 1。有点离谱对不对。

对于 JVM 而言，它对自增运算的处理，是会先定义一个临时变量来接收 i 的值，然后进行自增运算，最后又将临时变量赋给了值为 2 的 i，所以最后的结果为 1。

相当于这样的代码：

```java
int i = 1；
int temp = i;
i++；
i = temp;
System.out.println(i);
```

> 这段代码会输出什么？

```java 
int count = 0;
for(int i = 0;i < 100;i++)
{
    count = count++;
}
System.out.println("count = "+count);
```

答案是 0。

和上面的题目一样的道理，同样是用了临时变量，count 实际是等于临时变量的值。

```java
int autoAdd(int count)
{
    int temp = count;
    count = count + 1;
    return temp;
}
```

# 面向对象

## ⾯向对象和⾯向过程的区别? *

| 特点     | 面向过程编程                                   | 面向对象编程                                       |
| -------- | ---------------------------------------------- | -------------------------------------------------- |
| 核心思想 | 以过程为核心，函数是基本单位，通过函数完成任务 | 以对象为核心，对象是基本单位，通过对象交互完成任务 |
| 程序结构 | 函数和步骤组成的顺序流程                       | 类和对象组成的模块化结构                           |
| 代码复用 | 通过函数复用代码                               | 通过继承、组合、多态等方式复用代码                 |

## 封装、继承、多态 *

### 封装是什么？

**封装把一个对象的属性私有化，同时提供一些可以被外界访问的属性的方法，如果不想被外界访问，我们不必提供方法给外界访问。**但是如果一个类没有提供给外界任何可以访问的方法，那么这个类也没有什么意义了。

使用封装有 4 大好处：

- 1、**良好的封装能够减少耦合。**
- 2、类内部的结构可以自由修改。
- 3、可以对成员进行更精确的控制。
- 4、隐藏信息，实现细节。

### 继承是什么？

继承允许一个类作为子类继承现有类的属性和方法。以**提高代码的复用性**，建立类之间的层次关系。

同时，子类还可以重写或者扩展从父类继承来的属性和方法，从而实现多态。

### 多态是什么？

> 重载算多态吗

这个问题其实要取决于你对多态圈定的范围

首先我们看看多态的定义

- **允许程序在运行时再确定调用的是子类还是父类的方法。**
- 比如我们在编译期，只能调用父类中声明的方法，但在运行期，我们实际执行的是子类重写父类的方法。

也就是**编译看左边,运行看右边**,那么是不是可以说多态体现的是一种**动态绑定**呢?

再来看看重载,重载虽然是对一个方法的多种实现,宏观上来说他是多态的一种体现,但是微观看待,重载是在编译期间就决定好了具体要执行哪个重载方法,而不是运行时来决定的,所以谈不上这是多态的思想

那么我们在方法中写条件判断呢？这属于一种多态么？这实际上是一种策略模式或条件分支的实现方式，而不是多态性的直接体现。虽然这种方式可以根据不同的条件来执行不同的代码块，但它并没有利用面向对象编程中的多态性特性来简化代码和提高可维护性。

>最后，使用到了重载和重写都应该被视为具有多态性

**多态**：比如一个父类可能有若干子类，各子类实现父类方法有多种多样，调用父类方法时，**父类引用变量指向不同子类实例而执行不同方法**，这就是所谓父类方法是多态的。

优点：多态的目的是为了提高代码的灵活性和可扩展性，使得代码更容易维护和扩展。比如说动态绑定，**允许程序在运行时再确定调用的是子类还是父类的方法。**

## 抽象类

### 普通类和抽象类区别 *

- 实例化：普通类可以直接实例化对象，而抽象类不能被实例化，只能被继承。
- 方法实现：普通类中的方法可以有具体的实现，而抽象类中的方法可以有实现也可以没有实现。
- 继承：一个类可以继承一个普通类，而且可以继承多个接口；而一个类只能继承一个抽象类，但可以同时实现多个接口。
- 实现限制：普通类可以被其他类继承和使用，而抽象类一般用于作为基类，被其他类继承和扩展使用。



### 抽象类可以被实例化吗？ *

在Java中，抽象类本身不能被实例化。

这意味着不能使用`new`关键字直接创建一个抽象类的对象。抽象类的存在主要是为了被继承，它通常包含一个或多个抽象方法（由`abstract`关键字修饰且无方法体的方法），这些方法需要在子类中被实现。

### 抽象类可以定义构造方法吗？

可以，抽象类可以有构造方法

### Java抽象类和接口的区别是什么？

**两者的特点：**

- 抽象类用于描述类的共同特性和行为，可以有成员变量、构造方法和具体方法。适用于有明显继承关系的场景。
- 接口用于定义行为规范，可以多实现，只能有常量和抽象方法（Java 8 以后可以有默认方法和静态方法）。适用于定义类的能力或功能。

**两者的区别：**

- 实现方式：实现接口的关键字为implements，继承抽象类的关键字为extends。一个类可以实现多个接口，但一个类只能继承一个抽象类。所以，使用接口可以间接地实现多重继承。
- 方法方式：接口只有定义，不能有方法的实现，java 1.8中可以定义default方法体，而抽象类可以有定义与实现，方法可在抽象类中实现。
- 访问修饰符：接口成员变量默认为public static final，必须赋初值，不能被修改；其所有的成员方法都是public、abstract的。抽象类中成员变量默认default，可在子类中被重新定义，也可被重新赋值；抽象方法被abstract修饰，不能被private、static、synchronized和native等修饰，必须以分号结尾，不带花括号。
- 变量：抽象类可以包含实例变量和静态变量，而接口只能包含常量（即静态常量）。

### 抽象类能加final修饰吗？

**不能**，Java中的抽象类是用来被继承的，而final修饰符用于禁止类被继承或方法被重写，因此，抽象类和final修饰符是互斥的，不能同时使用。

## 接口

### 接口里面可以定义哪些方法？

- **抽象方法**

抽象方法是接口的核心部分，所有实现接口的类都必须实现这些方法。抽象方法默认是 public 和 abstract，这些修饰符可以省略。

```java
public interface Animal {
    void makeSound();
}
```

- **默认方法**

默认方法是在 Java 8 中引入的，允许接口提供具体实现。实现类可以选择重写默认方法。

```java
public interface Animal {
    void makeSound();
    
    default void sleep() {
        System.out.println("Sleeping...");
    }
}
```

- **静态方法**

静态方法也是在 Java 8 中引入的，它们属于接口本身，可以通过接口名直接调用，而不需要实现类的对象。

```java
public interface Animal {
    void makeSound();
    
    static void staticMethod() {
        System.out.println("Static method in interface");
    }
}
```

- **私有方法**

私有方法是在 Java 9 中引入的，用于在接口中为默认方法或其他私有方法提供辅助功能。这些方法不能被实现类访问，只能在接口内部使用。

```java
public interface Animal {
    void makeSound();
    
    default void sleep() {
        System.out.println("Sleeping...");
        logSleep();
    }
    
    private void logSleep() {
        System.out.println("Logging sleep");
    }
}
public interface Animal {
    void makeSound();
}
```



### 接口可以包含构造函数吗？

在接口中，不可以有构造方法,在接口里写入构造方法时，因为接口不会有自己的实例的，所以不需要有构造函数。

为什么呢？构造函数就是初始化class的属性或者方法，在new的一瞬间自动调用，那么问题来了Java的接口，都不能new 那么要构造函数干嘛呢？根本就没法调用

## 成员变量与局部变量的区别有哪些？ *

1. **从语法形式上看**：成员变量是属于类的，⽽局部变量是在⽅法中定义的变量或是⽅法的参数
2. **从变量在内存中的存储⽅式来看**：如果成员变量是使⽤ static 修饰的，那么这个成员变量是属于类的，如果没有使⽤ static 修饰，这个成员变量是属于实例的。对象存于堆内存，如果局部变量类型为基本数据类型，那么存储在栈内存，如果为引⽤数据类型，那存放的是指向堆内存对象的引⽤或者是指向常量池中的地址。
3. **从变量在内存中的⽣存时间上看**：成员变量是对象的⼀部分，它随着对象的创建⽽存在，⽽局部变量随着⽅法的调⽤⽽⾃动消失。
4. **成员变量如果没有被赋初值**：则会⾃动以类型的默认值⽽赋值（⼀种情况例外:被 final 修饰的成员变量也必须显式地赋值），⽽局部变量则不会⾃动赋值。

## Java 是值传递，还是引用传递？ *

Java 是值传递，不是引用传递。

当一个对象被作为参数传递到方法中时，参数的值就是该对象的引用。引用的值是对象在堆中的地址。

对象是存储在堆中的，所以传递对象的时候，可以理解为把变量存储的对象地址给传递过去。

![三分恶面渣逆袭：Java引用数据值传递示意图](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-14.png)

## 引用类型的变量有什么特点 *

引用类型的变量存储的是对象的地址，而不是对象本身。因此，引用类型的变量在传递时，传递的是对象的地址，也就是说，传递的是引用的值。

## 有一个父类和子类，都有静态的成员变量、静态构造方法和静态方法，在我new一个子类对象的时候，加载顺序是怎么样的？

当你实例化一个子类对象时，静态成员变量、静态构造方法和静态方法的加载顺序遵循以下步骤：

- 在创建子类对象之前，首先会加载父类的静态成员变量和静态代码块（构造方法无法被 `static` 修饰，因此这里是静态代码块）。这个加载是在类首次被加载时进行的，且只会发生一次。
- 接下来，加载子类的静态成员变量和静态代码块。这一过程也只发生一次，即当首次使用子类的相关代码时。
- 之后，执行实例化子类对象的过程。这时会呼叫父类构造方法，然后是子类的构造方法。

具体加载顺序可以简要总结为：

- **父类静态成员变量、静态代码块**（如果有）
- **子类静态成员变量、静态代码块**（如果有）
- **父类构造方法**（实例化对象时）
- **子类构造方法**（实例化对象时）

示例代码

```java
class Parent {
    static {
        System.out.println("Parent static block");
    }
    static int parentStaticVar = 10;

    Parent() {
        System.out.println("Parent constructor");
    }
}

class Child extends Parent {
    static {
        System.out.println("Child static block");
    }
    static int childStaticVar = 20;

    Child() {
        System.out.println("Child constructor");
    }
}

public class Main {
    public static void main(String[] args) {
        Child c = new Child();
    }
}
```

输出结果

```java
Parent static block
Child static block
Parent constructor
Child constructor
```

从输出可以看出，在创建 `Child` 类型对象时，首先执行父类的静态块，然后是子类的静态块，最后才是父类和子类的构造函数。这清晰地展示了加载的顺序。

## 访问修饰符的区别

| 修饰符         | 类内部 | 同一包 | 子类 | 其他包 |
| -------------- | ------ | ------ | ---- | ------ |
| `public`       | ✔️      | ✔️      | ✔️    | ✔️      |
| `protected`    | ✔️      | ✔️      | ✔️    | ❌      |
| 默认（无修饰） | ✔️      | ✔️      | ❌    | ❌      |
| `private`      | ✔️      | ❌      | ❌    | ❌      |

## **final修饰类、方法、属性有什么作用?**

final关键字主要用在三个地方：变量、方法、类。

- 修饰变量:表示**常量**在初始化后不能更改
  - 如果是基本数据类型的变量，其数值一旦在初始化之后就不能更改；如果是引用类型的变量，在对其初始化之后就不能再让其指向另一个对象。（但是引用指向的对象内容可以改变）这里就要好好想想为什么String是不可变的了

- 修饰类:表明这个**类不能被继承**。final类中的所有成员方法都会被隐式地指定为final方法。
- 修饰方法:方法不能被**重载**和**重写**

## 深拷贝浅拷贝 *

**前提**

不管是深拷贝和浅拷贝都需要去实现接口Cloneable，其中深拷贝还可以使用序列化和反序列化的方式实现

### 浅拷贝

当一个对象里面有引用类型的属性，使用浅拷贝出来的对象俩个引用地址一模一样，所以原引用地址对象的数据被修改，浅拷贝出来的对象数据也会被修改

**对象**

```java 
class Writer implements Cloneable{
    private int age;
    private String name;
    private Book book;


    @Override
    public String toString() {
        return super.toString().substring(26) +
                " age=" + age +
                ", name='" + name + '\'' +
                ", book=" + book +
                '}';
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

**测试类**

```java 
class TestClone {
    public static void main(String[] args) throws CloneNotSupportedException {
        Writer writer1 = new Writer(18,"二哥");
        Book book1 = new Book("编译原理",100);
        writer1.setBook(book1);

        Writer writer2 = (Writer) writer1.clone();
        System.out.println("浅拷贝后：");

        System.out.println("writer1：" + writer1);
        System.out.println("writer2：" + writer2);

        Book book2 = writer2.getBook();
        book2.setBookName("永恒的图灵");
        book2.setPrice(70);
        System.out.println("writer2.book 变更后：");

        System.out.println("writer1：" + writer1);
        System.out.println("writer2：" + writer2);
    }
}
```

**输出结果**

```
浅拷贝后：
writer1：Writer@68837a77 age=18, name='二哥', book=Book@32e6e9c3 bookName='编译原理', price=100}}
writer2：Writer@6d00a15d age=18, name='二哥', book=Book@32e6e9c3 bookName='编译原理', price=100}}
writer2.book 变更后：
writer1：Writer@68837a77 age=18, name='二哥', book=Book@32e6e9c3 bookName='永恒的图灵', price=70}}
writer2：Writer@36d4b5c age=18, name='二哥', book=Book@32e6e9c3 bookName='永恒的图灵', price=70}}
```

可以发现，引用数据book的地址是一样的，所以`@32e6e9c3`这个地址指向堆空间的数据被修改后，只要引用了地址`@32e6e9c3`的对象数据都会被修改

### 深拷贝

当一个对象里面有引用类型的属性，使用深拷贝出来的对象俩个引用地址不一样，所以原引用地址对象的数据被修改，深拷贝出来的对象数据不会被修改

#### 重写clone方式

**对象**

```java
class Writer implements Cloneable{
    private int age;
    private String name;
    private Book book;

    // getter/setter 和构造方法都已省略

    @Override
    public String toString() {
        return super.toString().substring(26) +
                " age=" + age +
                ", name='" + name + '\'' +
                ", book=" + book +
                '}';
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Writer writer = (Writer) super.clone();
        writer.setBook((Book) writer.getBook().clone());
        return writer;
    }
}
```

**测试类**

```java 
class TestClone {
    public static void main(String[] args) throws CloneNotSupportedException {
        Writer writer1 = new Writer(18,"二哥");
        Book book1 = new Book("编译原理",100);
        writer1.setBook(book1);

        Writer writer2 = (Writer) writer1.clone();
        System.out.println("深拷贝后：");

        System.out.println("writer1：" + writer1);
        System.out.println("writer2：" + writer2);

        Book book2 = writer2.getBook();
        book2.setBookName("永恒的图灵");
        book2.setPrice(70);
        System.out.println("writer2.book 变更后：");

        System.out.println("writer1：" + writer1);
        System.out.println("writer2：" + writer2);
    }
}
```

**输出结果**

```
深拷贝后：
writer1：Writer@6be46e8f age=18, name='二哥', book=Book@5056dfcb bookName='编译原理', price=100}}
writer2：Writer@6d00a15d age=18, name='二哥', book=Book@51efea79 bookName='编译原理', price=100}}
writer2.book 变更后：
writer1：Writer@6be46e8f age=18, name='二哥', book=Book@5056dfcb bookName='编译原理', price=100}}
writer2：Writer@6d00a15d age=18, name='二哥', book=Book@51efea79 bookName='永恒的图灵', price=70}}
```

可以发现，使用深拷贝拷贝出来的对象引用类型数据book的地址不一样，一个是`@5056dfcb`一个是`@51efea79`，所以修改book数据俩者不影响（引用地址不一样，指向的堆空间数据也不一样）

不过，通过 `clone()` 方法实现的深拷贝比较笨重，因为要将所有的引用类型都重写 `clone()` 方法，当嵌套的对象比较多的时候，就废了！所以还有另一种方式，**序列化**

#### **序列化的实现方式**

**前提**

使用序列化，需要实现Serializable接口

**对象**

```java
class Writer implements Serializable {
    private int age;
    private String name;
    private Book book;

    // getter/setter 和构造方法都已省略

    @Override
    public String toString() {
        return super.toString().substring(26) +
                " age=" + age +
                ", name='" + name + '\'' +
                ", book=" + book +
                '}';
    }

    //深度拷贝
    public Object deepClone() throws IOException, ClassNotFoundException {
        // 序列化
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bos);

        oos.writeObject(this);

        // 反序列化
        ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bis);

        return ois.readObject();
    }
}
```

**测试类**

```java
class TestClone {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        Writer writer1 = new Writer(18,"二哥");
        Book book1 = new Book("编译原理",100);
        writer1.setBook(book1);

        Writer writer2 = (Writer) writer1.deepClone();
        System.out.println("深拷贝后：");

        System.out.println("writer1：" + writer1);
        System.out.println("writer2：" + writer2);

        Book book2 = writer2.getBook();
        book2.setBookName("永恒的图灵");
        book2.setPrice(70);
        System.out.println("writer2.book 变更后：");

        System.out.println("writer1：" + writer1);
        System.out.println("writer2：" + writer2);
    }
}
```

**输出结果**

```
深拷贝后：
writer1：Writer@9629756 age=18, name='二哥', book=Book@735b5592 bookName='编译原理', price=100}}
writer2：Writer@544fe44c age=18, name='二哥', book=Book@31610302 bookName='编译原理', price=100}}
writer2.book 变更后：
writer1：Writer@9629756 age=18, name='二哥', book=Book@735b5592 bookName='编译原理', price=100}}
writer2：Writer@544fe44c age=18, name='二哥', book=Book@31610302 bookName='永恒的图灵', price=70}}
```

#### clone和序列化的区别

clone：每个对象都需要重写clone方法，实现太重

序列化：序列化涉及到输入流和输出流的读写，在性能上要比 HotSpot 虚拟机实现的 `clone()` 方法差很多。

### 深拷贝浅拷贝区别

- 当一个对象里面有引用类型的属性，使用深拷贝出来的对象**俩个引用地址不一样**，所以原引用地址对象的数据被修改，深拷贝出来的对象数据**不会**被修改

- 当一个对象里面有引用类型的属性，使用浅拷贝出来的对象**俩个引用地址一模一样**，所以原引用地址对象的数据被修改，浅拷贝出来的对象数据**也会**被修改

### 为什么要使用深拷贝？

我们希望在改变新的数组（对象）的时候，不改变原数组（对象）

## 创建对象有哪几种方式？

Java 有四种创建对象的方式：

①、new 关键字创建，这是最常见和直接的方式，通过调用类的构造方法来创建对象。

```Java
Person person = new Person();
```

②、反射机制创建，反射机制允许在运行时创建对象，并且可以访问类的私有成员，在框架和工具类中比较常见。

```java
Class clazz = Class.forName("Person");
Person person = (Person) clazz.newInstance();
```

③、clone 拷贝创建，通过 clone 方法创建对象，需要实现 Cloneable 接口并重写 clone 方法。

```java
Person person = new Person();
Person person2 = (Person) person.clone();
```

④、序列化机制创建，通过序列化将对象转换为字节流，再通过反序列化从字节流中恢复对象。需要实现 Serializable 接口。

```java
Person person = new Person();
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("person.txt"));
oos.writeObject(person);
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person.txt"));
Person person2 = (Person) ois.readObject();
```

## 静态和非静态的区别

被static修饰的话在jvm初始化的过程中就会去初始化静态方法和静态变量这些数据,所以后续才不需要去new就能使用,而非static需要通过new来创建对象的时候初始化

## hashCode()与equals()之间的关系

在Java的一些集合类的实现中，再比如Set在比较两个对象是否相等时，会根据上面的原则，会先调用对象的hashCode()方法得到hashCode进行比较，如果hashCode不相同，就可以直接认为这两个对象不相同，如果hashCode相同，那么就会进一步调用equals()方法进行比较。而equals()方法，就是用来最终确定两个对象是不是相等的，通常equals方法的实现会比较重，逻辑比较多，而hashCode()主要就是得到一个哈希值，实际上就一个数字，相对而言比较轻，所以在比较两个对象时，通常都会先根据hashCode想比较一下。

所以我们就需要注意，如果我们重写了equals()方法，那么就要注意hashCode()方法，一定要保证能遵守上述规则。

## ==和equals方法的区别

- ==：它的作用是判断两个对象的地址是不是相等。如果是基本数据类型，比较是值，如果是引用类型，比较的是引用地址
- equals： 它的作用也是判断两个对象是否相等,但它一般有两种使用情况:
  - 情况1:类没有重写 equals() 方法。则通过 equals() 比较该类的两个对象时,等价于通过“==”比较这两个对象引用地址。
  - 情况2:类重写了 equals() 方法。一般,我们都重写例如String的equals() 方法来两个对象的内容相等;若它们的内容相等,则返回 TRUE(即,认为这两个对象相等)。


## 重载和重写的区别

- 重载： 在一个类中，同名的方法如果有不同的参数列表（比如参数类型不同、参数个数不同）则视为重载。
- 重写： 从字面上看，重写就是 重新写一遍的意思。如果父类方法访问修饰符为 **private** 则子类就不能重写该方法,其实就是在子类中把父类本身有的方法重新写一遍。子类继承了父类的方法，但有时子类并不想原封不动的继承父类中的某个方法，所以在方法名，参数列表，返回类型都相同(子类中方法的返回值可以是父类中方法返回值的子类)的情况下， 对方法体进行修改，这就是重写。但要注意子类方法的访问修饰权限不能小于父类的。

## 异常处理

### Java 中异常处理体系?

![三分恶面渣逆袭：Java异常体系](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-22.png)

### 异常的处理方式？

![三分恶面渣逆袭：异常处理](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-23.png)

①、遇到异常时可以不处理，直接通过throw 和 throws 抛出异常，交给上层调用者处理。

throws 关键字用于声明可能会抛出的异常，而 throw 关键字用于抛出异常。

```java
public void test() throws Exception {
    throw new Exception("抛出异常");
}
```

②、使用 try-catch 捕获异常，处理异常。

```java
try {
    //包含可能会出现异常的代码以及声明异常的方法
}catch(Exception e) {
    //捕获异常并进行处理
}finally {
    //可选，必执行的代码
}
```

#### catch和finally的异常可以同时抛出吗？

如果 catch 块抛出一个异常，而 finally 块中也抛出异常，那么最终抛出的将是 finally 块中的异常。catch 块中的异常会被丢弃，而 finally 块中的异常会覆盖并向上传递。

```java
public class Example {
    public static void main(String[] args) {
        try {
            throw new Exception("Exception in try");
        } catch (Exception e) {
            throw new RuntimeException("Exception in catch");
        } finally {
            throw new IllegalArgumentException("Exception in finally");
        }
    }
}
```

- try 块首先抛出一个 Exception。
- 控制流进入 catch 块，catch 块中又抛出了一个 RuntimeException。
- 但是在 finally 块中，抛出了一个 IllegalArgumentException，最终程序抛出的异常是 finally 块中的 IllegalArgumentException。

虽然 catch 和 finally 中的异常不能同时抛出，但可以手动捕获 finally 块中的异常，并将 catch 块中的异常保留下来，避免被覆盖。常见的做法是使用一个变量临时存储 catch 中的异常，然后在 finally 中处理该异常：



```java
public class Example {
    public static void main(String[] args) {
        Exception catchException = null;
        try {
            throw new Exception("Exception in try");
        } catch (Exception e) {
            catchException = e;
            throw new RuntimeException("Exception in catch");
        } finally {
            try {
                throw new IllegalArgumentException("Exception in finally");
            } catch (IllegalArgumentException e) {
                if (catchException != null) {
                    System.out.println("Catch exception: " + catchException.getMessage());
                }
                System.out.println("Finally exception: " + e.getMessage());
            }
        }
    }
}
```

# 反射

## 反射的作用

在**程序运行时**动态加载类并获取类的详细信息，从而操作**类或对象**的**属性和方法**，不需要提前在编译期知道运行的对象是谁。

## 反射在你平时写代码或者框架中的应用场景有哪些?   

> springIOC和AOP

- IOC
  - Spring的ioc在bean的生命周期中的创建bean这一步骤就是基于反射拿到类然后通过构造方法创建对象
- AOP
  - 我之前看过点JDK动态代理源码,底层就是通过反射动态获取类对象然后在使用invoke方法来对方法增强

# I/0 *

## Java 中 IO 流分为几种?

### 按数据流方向划分

- 输入流（Input Stream）：从源（如文件、网络等）读取数据到程序。
- 输出流（Output Stream）：将数据从程序写出到目的地（如文件、网络、控制台等）。

### 按功能如何划分

- 节点流（Node Streams）：直接与数据源或目的地相连，如 FileInputStream、FileOutputStream。
- 处理流（Processing Streams）：对一个已存在的流进行包装，如缓冲流 BufferedInputStream、BufferedOutputStream。
- 管道流（Piped Streams）：用于线程之间的数据传输，如 PipedInputStream、PipedOutputStream。

### 按处理数据单位划分

- 字节流（Byte Streams）：以字节为单位读写数据，主要用于处理二进制数据，如音频、图像文件等。
- 字符流（Character Streams）：以字符为单位读写数据，主要用于处理文本数据。

![二哥的 Java 进阶之路](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/shangtou-01.png)

### IO 流用到了什么设计模式？

其实，Java 的 IO 流体系还用到了一个设计模式——**装饰器模式**。

![Java IO流用到装饰器模式](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-25.png)Java IO流用到装饰器模式

### Java 缓冲区溢出，如何预防

Java 缓冲区溢出主要是由于向缓冲区写入的数据超过其能够存储的数据量。可以采用这些措施来避免：

①、**合理设置缓冲区大小**：在创建缓冲区时，应根据实际需求合理设置缓冲区的大小，避免创建过大或过小的缓冲区。

②、**控制写入数据量**：在向缓冲区写入数据时，应该控制写入的数据量，确保不会超过缓冲区的容量。Java 的 ByteBuffer 类提供了`remaining()`方法，可以获取缓冲区中剩余的可写入数据量。

```java
import java.nio.ByteBuffer;

public class ByteBufferExample {

    public static void main(String[] args) {
        // 模拟接收到的数据
        byte[] receivedData = {1, 2, 3, 4, 5};
        int bufferSize = 1024;  // 设置一个合理的缓冲区大小

        // 创建ByteBuffer
        ByteBuffer buffer = ByteBuffer.allocate(bufferSize);

        // 写入数据之前检查容量是否足够
        if (buffer.remaining() >= receivedData.length) {
            buffer.put(receivedData);
        } else {
            System.out.println("Not enough space in buffer to write data.");
        }

        // 准备读取数据：将limit设置为当前位置，position设回0
        buffer.flip();

        // 读取数据
        while (buffer.hasRemaining()) {
            byte data = buffer.get();
            System.out.println("Read data: " + data);
        }

        // 清空缓冲区以便再次使用
        buffer.clear();
    }
}
```

### 既然有了字节流,为什么还要有字符流?

字符流一般用来处理文本数据，而字节流一般用来处理图片、视频、音频这种媒体数据

## BIO、NIO、AIO区别是什么？

- **BIO （Blocking I/O）**：同步阻塞 I/O 模式（传统IO），线程在执行 I/O 操作时被阻塞，无法处理其他任务，**适用于连接数较少且稳定的场景。**

- **NIO （New I/O）**：同步非阻塞模式，线程在等待 I/O 时可执行其他任务，通过 Selector 监控多个 Channel 上的事件，提高性能和可伸缩性，**适用于高并发场景。**
- **AIO （Asynchronous I/O）**：异步非阻塞 I/O 模型，线程发起 I/O 请求后立即返回，当 I/O 操作完成时通过回调函数通知线程，进一步提高了并发处理能力，**适用于高吞吐量场景。**

## 有了传统IO为什么还要NIO？

1. 传统 I/O 采用**阻塞式模型**也就是BIO，线程在 I/O 操作期间无法执行其他任务，NIO 使用**非阻塞模型**，。
2. 传统 I/O 使用基于字节流或字符流的类（如 FileInputStream、BufferedReader 等）进行文件读写。NIO 使用通道（Channel）和缓冲区（Buffer）进行文件操作，**不过实际上NIO 在性能上的优势并不大**。传统 I/O 使用 Socket 和 ServerSocket 进行网络传输，存在阻塞问题。NIO 提供了 SocketChannel 和 ServerSocketChannel，支持非阻塞网络传输，**提高了并发处理能力，这才是NIO核心。**

# 序列化 *

## 什么是序列化？什么是反序列化？

序列化（Serialization）是指将对象转换为字节流的过程，以便能够将该对象保存到文件、数据库，或者进行网络传输。

反序列化（Deserialization）就是将字节流转换回对象的过程，以便构建原始对象。

## 序列化的步骤

第一步，实现 Serializable 接口。

```java
public class Person implements Serializable {
    private String name;
    private int age;

    // 省略构造方法、getters和setters
}
```

第二步，使用 ObjectOutputStream 来将对象写入到输出流中。

```java
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("person.ser"));
```

第三步，调用 ObjectOutputStream 的 writeObject 方法，将对象序列化并写入到输出流中。

```java
Person person = new Person("沉默王二", 18);
out.writeObject(person);
```

## 说说有几种序列化方式？

Java 序列化方式有很多，常见的有三种：

![Java常见序列化方式](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-31.png)

- Java 对象序列化 ：Java 原生序列化方法即通过 Java 原生流(InputStream 和 OutputStream 之间的转化)的方式进行转化，一般是对象输出流 `ObjectOutputStream`和对象输入流`ObjectInputStream`。
- Json 序列化：这个可能是我们最常用的序列化方式，Json 序列化的选择很多，一般会使用 jackson 包，通过 ObjectMapper 类来进行一些操作，比如将对象转化为 byte 数组或者将 json 串转化为对象。
- ProtoBuff 序列化：ProtocolBuffer 是一种轻便高效的结构化数据存储格式，ProtoBuff 序列化对象可以很大程度上将其压缩，可以大大减少数据传输大小，提高系统性能。

# 泛型 *

## Java 泛型了解么？什么是类型擦除？介绍一下常用的通配符？

### 什么是泛型？

泛型主要用于提高代码的类型安全，它允许在定义类、接口和方法时使用类型参数，这样可以在编译时检查类型一致性，避免不必要的类型转换和类型错误。

没有泛型的时候，像 List 这样的集合类存储的是 Object 类型，导致从集合中读取数据时，必须进行强制类型转换，否则会引发 ClassCastException。

```
List list = new ArrayList();
list.add("hello");
String str = (String) list.get(0);  // 必须强制类型转换
```

泛型一般有三种使用方式:**泛型类**、**泛型接口**、**泛型方法**。

![泛型类、泛型接口、泛型方法](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-32.png)

**1.泛型类**：

```java
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{

    private T key;

    public Generic(T key) {
        this.key = key;
    }

    public T getKey(){
        return key;
    }
}
```

如何实例化泛型类：

```java
Generic<Integer> genericInteger = new Generic<Integer>(123456);
```

**2.泛型接口** ：

```java
public interface Generator<T> {
    public T method();
}
```

实现泛型接口，指定类型：

```java
class GeneratorImpl<T> implements Generator<String>{
    @Override
    public String method() {
        return "hello";
    }
}
```

**3.泛型方法** ：

```java
   public static < E > void printArray( E[] inputArray )
   {
         for ( E element : inputArray ){
            System.out.printf( "%s ", element );
         }
         System.out.println();
    }
```

使用：

```java
// 创建不同类型数组： Integer, Double 和 Character
Integer[] intArray = { 1, 2, 3 };
String[] stringArray = { "Hello", "World" };
printArray( intArray  );
printArray( stringArray  );
```

### 泛型常用的通配符有哪些？

**常用的通配符为： T，E，K，V，？**

- ？ 表示不确定的 java 类型
- T (type) 表示具体的一个 java 类型
- K V (key value) 分别代表 java 键值中的 Key Value
- E (element) 代表 Element

### 什么是泛型擦除？

所谓的泛型擦除，官方名叫“类型擦除”。

Java 的泛型是伪泛型，这是因为 Java 在编译期间，所有的类型信息都会被擦掉。

也就是说，在运行的时候是没有泛型的。

例如这段代码，往一群猫里放条狗：

```java
LinkedList<Cat> cats = new LinkedList<Cat>();
LinkedList list = cats;  // 注意我在这里把范型去掉了，但是list和cats是同一个链表！
list.add(new Dog());  // 完全没问题！
```

因为 Java 的范型只存在于源码里，编译的时候给你静态地检查一下范型类型是否正确，而到了运行时就不检查了。上面这段代码在 JRE（Java**运行**环境）看来和下面这段没区别：

```java
LinkedList cats = new LinkedList();  // 注意：没有范型！
LinkedList list = cats;
list.add(new Dog());
```

### 为什么要类型擦除呢？

主要是为了向下兼容，因为 JDK5 之前是没有泛型的，为了让 JVM 保持向下兼容，就出了类型擦除这个策略。

# 排序算法

## 总结

（1）当**数据规模较小**时候，可以使用**插入**排序或者**选择**排序。

（2）当**基本有序**，可以用**插入**排序和**冒泡**排序。

（3）当**数据规模较大**时，可以考虑使用**快速**排序。

## 二分查找(排序好)

1. 前提：有已排序数组 A（假设已经做好）
2. 定义左边界 L、右边界 R，确定搜索范围，循环执行二分查找（3、4两步）
3. 获取中间索引 Mid = Floor((L+R) /2)
4. 中间索引的值 A[M] 与待搜索的值 T 进行比较

​		① A[M] == Target 表示找到，返回中间索引

​		② A[M] > Target ，中间值右侧的其它元素都大于 Target ，无需比较，中间索引左边去找，M - 1 设置为右边界，重新查找

​		③ A[M] < Target ，中间值左侧的其它元素都小于 Target ，无需比较，中间索引右边去找， M + 1 设置为左边界，重新查找

5. 当 L > R 时，表示没有找到，应结束循环

## 冒泡排序

1. 依次比较数组中相邻两个元素大小，若 当前下表元素> next元素，则交换两个元素，两两都比较一遍称为一轮冒泡，结果是让最大的元素排至最后
2. 重复以上步骤，直到整个数组有序

冒泡排序动画演示

## 选择排序(2子集,选择)

1. 将数组分为两个子集，排序的和未排序的，每一轮从未排序的子集中选出**最小**的元素，**放入排序子集\****
2. 重复以上步骤，直到整个数组有序

选择排序动画演示

## 插入排序(2子集,插入)

1. 将数组分为两个区域，排序区域和未排序区域，每一轮从未排序区域中取出第一个元素，插入到排序区域（需保证顺序）
2. 插入:每次用当前元素跟已排序区域元素进行比较,如果小于已排序区域元素就交换位置,继续比较直到当前元素小于未排序区域下一个元素
3. 重复以上步骤，直到整个数组有序

插入排序动画演示

## 快速排序(分治,递归,单边排序)   *

1. 每一轮排序选择一个基准点（pivot）进行分区

2. 1. 让小于基准点的元素的进入一个分区，大于基准点的元素的进入另一个分区
   2. 当分区完成时，基准点元素的位置就是其最终位置

3. 在子分区内重复以上过程，直至子分区元素个数少于等于 1，这体现的是分而治之的思想 （[divide-and-conquer](https://en.wikipedia.org/wiki/Divide-and-conquer_algorithm)）
4. 单边排序

5. 1. 选择最右元素作为目标值
   2. j 指针负责找到比目标值小的元，一旦找到则与 i 进行交换,如果下标和目标下标一致说明走到最后了则也交换
   3. i 指针维护小于目标值元素的边界，也是每次交换的目标索引,交换后i索引要+1
   4. 最后目标值与 i 交换，i 即为分区位置

快速排序动画演示

# String

## String str1 = new String("abc") 和 String str2 = "abc" 的区别？

直接使用双引号为字符串变量赋值时，Java 首先会检查字符串常量池中是否已经存在相同内容的字符串。

如果存在，Java 就会让新的变量引用池中的那个字符串；如果不存在，它会创建一个新的字符串，放入池中，并让变量引用它。

使用 `new String("abc")` 的方式创建字符串时，实际分为两步：

- 第一步，先检查字符串字面量 "abc" 是否在字符串常量池中，如果没有则创建一个；如果已经存在，则引用它。
- 第二步，在堆中再创建一个新的字符串对象，并将其初始化为字符串常量池中 "abc" 的一个副本。

![三分恶面渣逆袭：堆与常量池中的String](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-17.png)三分

也就是说：

```java
String s1 = "沉默王二";
String s2 = "沉默王二";
String s3 = new String("沉默王二");

System.out.println(s1 == s2); // 输出 true，因为 s1 和 s2 引用的是字符串常量池中同一个对象。
System.out.println(s1 == s3); // 输出 false，因为 s3 是通过 new 关键字显式创建的，指向堆上不同的对象。
```

### String s = new String("abc")创建了几个对象？

字符串常量池中如果之前已经有一个，则不再创建新的，直接引用；如果没有，则创建一个。

堆中肯定有一个，因为只要使用了 new 关键字，肯定会在堆中创建一个。

## String 是 Java 基本数据类型吗？

不是,String 是一个比较特殊的引用数据类型。

## String 类可以继承吗？

不行。String 类使用 final 修饰，是所谓的不可变类，无法被继承。

## string为什么是不可变的,为什么要这样设计

被private和final修饰了,final保证他不可指向新数组,而private保证他数值不能被修改,并且他没有提供任何修改字符数组的方法

- 这样设计主要是因为只有当string不可变,字符串常量池才能发挥作用,因为当你创建字符串时,会根据字符串常量池里如果有这个字符串,他会返回已有这个字符串对象的引用,如果字符串可变,那你引用之后可以随时修改数据,其他引用的数据就也被修改了,导致数据发生各种修改错误
- string不可变可以保证他的hash码也不会变,如果string可改变那他的hash码也会改变,会出问题,比如你之前存储一个string对象,到后面就会发现找不到源对象
- 不可变对象是线程安全的可以放心使用

## String常量池作用是什么

字符串常量池存储字符串对象引用,常量池位于堆里面,当你创建字符串的时候,如果常量池有数据,那么会直接返回一个引用地址给你复用,避免相同字符串创建消耗资源

## String、StringBuffer、StringBuilder的区别

可变:

- String 类中**使用 final** 关键字字符数组保存字符串，所以 String 对象是不可变的。
- StringBuilder 与 StringBuffer 是**没有用 final** 关键字修饰，所以这两种对象都是可变的。

线程安全:

- String中的对象是不可变的，也就可以理解为常量，**线程安全**。
- StringBuffer 对方法加了**同步锁**或者对调用的方法加了同步锁，所以是**线程安全**的。
- StringBuilder 并没有对方法进行加同步锁，所以是**非线程安全**的。

总结: 于三者使用的总结：操作少量的数据 = String单线程大量字符串操作 = StringBuilder多线程大量字符串操作 = StringBuffer

## 字符串拼接是如何实现的？

因为 String 是不可变的，因此通过“**+**”操作符进行的字符串拼接，会生成新的字符串对象。

例如：

```java
String a = "hello ";
String b = "world!";
String ab = a + b;
```

a 和 b 是通过双引号定义的，所以会在字符串常量池中，而 ab 是通过“+”操作符拼接的，所以会在堆中生成一个新的对象。

![三分恶面渣逆袭：jdk1.8之前的字符串拼接](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-18.png)三分恶面渣逆袭：jdk1.8之前的字符串拼接

Java 8 时，JDK 对“+”号的字符串拼接进行了优化，Java 会在编译期基于 StringBuilder 的 append 方法进行拼接。

也就是说，上面的代码相当于：

```java 
String a = "hello ";
String b = "world!";
StringBuilder sb = new StringBuilder();
sb.append(a);
sb.append(b);
String ab = sb.toString();
```

因此，如果笼统地讲，通过加号拼接字符串时会创建多个 String 对象是不准确的。因为加号拼接在编译期还会创建一个 StringBuilder 对象，最终调用 `toString()` 方法的时候再返回一个新的 String 对象。 

# Integer

## Integer a= 127，Integer b = 127；Integer c= 128，Integer d = 128；相等吗? *

a 和 b 相等，c 和 d 不相等。

这个问题涉及到 Java 的自动装箱机制以及`Integer`类的缓存机制。

对于第一对：

```Java
Integer a = 127;
Integer b = 127;
```

`a`和`b`是相等的。这是因为 Java 在自动装箱过程中，会使用`Integer.valueOf()`方法来创建`Integer`对象。

`Integer.valueOf()`方法会针对数值在-128 到 127 之间的`Integer`对象使用缓存。因此，`a`和`b`实际上引用了常量池中相同的`Integer`对象。

对于第二对：

```
Integer c = 128;
Integer d = 128;
```

`c`和`d`不相等。这是因为 128 超出了`Integer`缓存的范围(-128 到 127)。

因此，自动装箱过程会为`c`和`d`创建两个不同的`Integer`对象，它们有不同的引用地址。

可以通过`==`运算符来检查它们是否相等：

```
System.out.println(a == b); // 输出true
System.out.println(c == d); // 输出false
```

要比较`Integer`对象的数值是否相等，应该使用`equals`方法，而不是`==`运算符：

```
System.out.println(a.equals(b)); // 输出true
System.out.println(c.equals(d)); // 输出true
```

使用`equals`方法时，`c`和`d`的比较结果为`true`，因为`equals`比较的是对象的数值，而不是引用地址。

### 什么是 Integer 缓存？

Integer 缓存的主要目的是优化性能和内存使用。对于小整数的频繁操作，使用缓存可以显著减少对象创建的数量。

就拿 Integer 的缓存来说吧。根据实践发现，大部分的数据操作都集中在值比较小的范围，因此 Integer 搞了个缓存池，默认范围是 -128 到 127。

![二哥的 Java 进阶之路：integer 源码](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/javase-20240323080956.png)

当我们使用自动装箱来创建这个范围内的 Integer 对象时，Java 会直接从缓存中返回一个已存在的对象，而不是每次都创建一个新的对象。这意味着，对于这个值范围内的所有 Integer 对象，它们实际上是引用相同的对象实例。

引用是 Integer 类型，= 右侧是 int 基本类型时，会进行自动装箱，调用的其实是 `Integer.valueOf()`方法，它会调用 IntegerCache。

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

IntegerCache 是一个静态内部类，在静态代码块中会初始化好缓存的值。

```java
private static class IntegerCache {
    ……
    static {
        //创建Integer对象存储
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);
        ……
    }
}
```

### new Integer(10) == new Integer(10) 相等吗

在 Java 中，使用`new Integer(10) == new Integer(10)`进行比较时，结果是 false。

这是因为 new 关键字会在堆（Heap）上为每个 Integer 对象分配新的内存空间，所以这里创建了两个不同的 Integer 对象，它们有不同的内存地址。

当使用==运算符比较这两个对象时，实际上比较的是它们的内存地址，而不是它们的值，因此即使两个对象代表相同的数值（10），结果也是 false。

# 场景

## 有一个学生类，想按照分数排序，再按学号排序，应该怎么做？ *

可以使用Comparable接口来实现按照分数排序，再按照学号排序。首先在学生类中实现Comparable接口，并重写compareTo方法，然后在==compareTo==方法中实现按照分数排序和按照学号排序的逻辑。

```java
public class Student implements Comparable<Student> {
    private int id;
    private int score;

    // 构造方法和其他属性、方法省略

    @Override
    public int compareTo(Student other) {
        if (this.score != other.score) {
            return Integer.compare(other.score, this.score); // 按照分数降序排序
        } else {
            return Integer.compare(this.id, other.id); // 如果分数相同，则按照学号升序排序
        }
    }
}
```

然后在需要对学生列表进行排序的地方，使用Collections.sort()方法对学生列表进行排序即可：

```java
List<Student> students = new ArrayList<>();
// 添加学生对象到列表中
Collections.sort(students);
```

# 