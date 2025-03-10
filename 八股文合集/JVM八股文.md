# JVM的组成

[思维导图](https://naotu.baidu.com/file/82c558b99eb86355629a8d6742f687d8)

## JVM的作用

- 一次编写，到处运行
  - 因为别的都是在操作系统上(linux,windows)上跑的,而java是在jvm,所以才可以做到一次编写,到处运行

- 自动内存管理，垃圾回收机制

## JVM的组成

1. **类加载器**：
   - 负责加载Java字节码文件，并将其转换为可以执行的类。
   - 它将字节码解释成机器码并执行，也有可能使用即时编译（JIT）技术将字节码直接编译成本地机器码执行。

2. **运行时数据区域**：
   - 这是JVM在执行Java程序时使用的内存区域，包括：
    - **方法区**：用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。
    - **堆**：用于存储对象实例和数组。堆内存是GC（垃圾回收）的重点回收区域，其中分代回收机制将堆内存划分为年轻代和老年代两个区域。
    - **虚拟机栈**：也叫线程栈，用于存储方法调用和局部变量。每个方法在执行时都会创建一个栈帧，栈帧中包含了局部变量表、操作数栈、动态链接、方法出口等信息。
    - **本地方法栈**：为虚拟机使用的Native方法服务，执行每个本地方法时都会创建一个栈帧。
    - **程序计数器**：指示Java虚拟机下一条需要执行的字节码指令。

3. **执行引擎**：
   - 也叫解释器，负责解释和执行Java字节码指令。
   - 它将字节码指令转换为机器码，然后交由操作系统执行。

4. **垃圾回收器**：
   - 负责自动回收不再使用的对象内存空间，释放资源。
   - 垃圾回收器会定期扫描堆内存，找出不再被引用的对象，并将其回收，以确保JVM的内存使用效率。

5. **本地库接口**：
   - 本地接口的作用是融合不同的语言为Java所用。
   - 它允许Java代码与本地代码（如C、C++代码）进行交互。

## JVM运行流程是什么？

运行流程：

- **类加载器**把Java代码转换为字节码

- **运行时数据区**把字节码加载到内存中，而字节码文件只是JVM的一套指令集规范，并不能直接交给底层系统去执行，而是有执行引擎运行

- **执行引擎**将字节码翻译为底层系统指令，再交由CPU执行去执行，此时需要调用其他语言的**本地库接口**来实现整个程序的功能。

## 运行时数据区

### 程序计数器

**介绍**

程序计数器：**线程私有的**，内部保存的字节码的行号。**用于记录正在执行的字节码指令的地址。**

> 为什么是线程私有的?

我们考虑一下，如果在线程切换的过程中，下一条指令执行到哪里了，是不是还是会用到我们的程序计数器啊。每个线程都有自己的程序计数器，因为它们各自执行的代码的指令地址是不一样的呀，所以每个线程都应该有自己的程序计数器。

**作用**

首先我们知道单核的处理器在处理多线程的情况下会给每个线程分配一个执行时间,那举个例子线程A正在执行并且用完了可分配的执行时间,这个时候就轮到线程B执行了,那B也用完了下次分配回A线程,这个时候不可能说重新执行一遍,那要怎么知道上次执行到哪是需要东西来记录的,这个时候程序计数器就是这个作用,他会记录正在执行的字节码的指令

> 程序计数器是JVM规范中唯一一个没有规定现OOM的区域，所以这个空间也不会进行GC。

### 堆

JAVA的堆是**线程共享的**,然后堆是有垃圾回收机制的,**存储的是对象还有数组这类数据**

- **新生代**:年轻代被划分为三部分，Eden(伊甸园)区和两个大小严格相同的Survivor(幸存者)区，根据JVM的策略，在经过几次垃圾收集后，任然存活于Survivor的对象将被移动到老年代区间。
- **老年代**:老年代主要保存生命周期长的对象，一般是一些老的对象
- **元空间**:元空间保存的类信息、**静态变量、常量、常量池**、编译后的代码
  - String s = new String（“abc”）执行过程中分别对应哪些内存区域？
    - 首先，我们看到这个代码中有一个new关键字，我们知道**new**指令是创建一个类的实例对象并完成加载初始化的，因此这个字符串对象是在**运行期**才能确定的，创建的字符串对象是在**堆内存上**。
    - 其次，在String的构造方法中传递了一个字符串abc，由于这里的abc是被final修饰的属性，所以它是一个字符串常量。在首次构建这个对象时，JVM拿字面量"abc"去字符串常量池试图获取其对应String对象的引用。于是在堆中创建了一个"abc"的String对象，并将其引用保存到字符串常量池中，然后返回；
    - 所以，**如果abc这个字符串常量不存在，则创建两个对象，分别是abc这个字符串常量，以及new String这个实例对象。如果abc这字符串常量存在，则只会创建一个对象**。
  - 在jdk1.7之前还有个方法区但是放在堆是**会出现内存溢出的情况**,所以1.8之后通过元空间的规范实现并且放在栈里解决了这个问题
- **大对象区**:在某些JVM实现中（如G1垃圾收集器），为大对象分配了专门的区域，称为大对象区或Humongous Objects区域。大对象是指需要大量连续内存空间的对象，如大数组。这类对象直接分配在老年代，以避免因频繁的年轻代晋升而导致的内存碎片化问题。

> 堆的话可以详细讲一讲**新生代和老年代的分代回收**,也可以详细讲讲他的**垃圾回收**

### 栈

- 每个线程都会分配一个栈内存，是先进后出
- 每个栈由多个栈帧组成，对应着每次方法调用时所占用的内存
- 每个线程**只能有一个活动栈帧**，对应着当前正在执行的那个方法

> 垃圾回收是否涉及栈内存？

垃圾回收主要指就是堆内存，当栈帧弹栈以后，内存就会释放

> 栈内存分配越大越好吗？

未必，默认的栈内存通常为1024k

栈帧过大会导致线程数变少，例如，机器总内存为512m，目前能活动的线程数则为512个，如果把栈内存改为2048k，那么能活动的栈帧就会减半

> 方法内的局部变量是否线程安全？

- 如果方法内局部变量没有逃离方法的作用范围，它是线程安全的
- 如果是局部变量引用了对象，并逃离方法的作用范围，需要考虑线程安全
- 比如以下代码：

> 栈内存溢出情况

- 栈帧过多导致栈内存溢出，典型问题：递归调用
- 栈帧过大导致栈内存溢出

### 方法区

**概述**

- 方法区是各个线程共享的内存区域
- 主要存储类的信息、运行时常量池
- 虚拟机启动的时候创建，关闭虚拟机时释放
- 如果方法区域中的内存无法满足分配请求，则会抛出OutOfMemoryError: Metaspace

### 堆栈的区别

1. 栈内存一般会用来存储**局部变量和方法调用**，但堆内存是用来**存储Java对象和数组**的。
2. **堆会GC垃圾回收，而栈不会。**

2. 栈内存是**线程私有的**，而堆内存是**线程共有的**。

3. 两者异常错误不同，但如果栈内存或者堆内存不足都会抛出异常。

栈空间不足：java.lang.StackOverFlowError。

堆空间不足：java.lang.OutOfMemoryError。



## 类加载器

### 什么是类加载器

JVM只会运行二进制文件，而类加载器（ClassLoader）的主要作用就是将**字节码文件加载到JVM中**，从而让Java程序能够启动起来。现有的类加载器基本上都是java.lang.ClassLoader的子类，该类的只要职责就是用于将指定的类找到或生成对应的字节码文件，同时类加载器还会负责加载程序所需要的资源

### 类加载器有哪些

- **启动类加载器(BootStrap ClassLoader)：**

该类并不继承ClassLoader类，其是由C++编写实现。用于加载**JAVA_HOME/jre/lib**目录下的类库。

- **扩展类加载器(ExtClassLoader)：**

该类是ClassLoader的子类，主要加载**JAVA_HOME/jre/lib/ext**目录中的类库。

- **应用类加载器(AppClassLoader)：**

该类是ClassLoader的子类，主要用于加载**classPath**下的类，也就是加载开发者自己编写的Java类。

- **自定义类加载器：**

开发者自定义类继承ClassLoader，实现自定义类加载规则。

> 引出双亲委派
>

### 双亲委派

#### 什么是双亲委派模型

首先Java加载类是自上而下的,也就是说如果顶层父类有需要加载的类,就优先加载顶层父类,没有的话在向下一层继续看,以此类推

#### JVM为什么采用双亲委派机制

主要是因为如果不这样的话假设你自己写了个string类那么java会在应用类加载器部分读取到string也就是说后续你要使用string用的是自己写的stirng而不是jdk/jre/lib下的string类,自己写的肯定没有jdk的好用,所以如果不使用双亲委派模型会出现这种覆盖原类库的情况 

#### 如何打破双亲委派

可以通过继承classloder类重写loadclass方法,这个方法就是具体的双亲委派的逻辑,可以修改

#### 为什么要打破双亲委派

1. **加载不同版本的类库**：在大型应用或Web容器中，不同的应用程序可能会依赖同一个第三方类库的不同版本。为了保证每个应用程序的类库都是独立、相互隔离的，需要打破双亲委派机制，使得每个应用程序的类加载器能够加载自己所需的类库版本。例如，在Tomcat中，通过自定义类加载器并重写loadClass方法，实现了对双亲委派机制的破坏，以达到类库隔离的效果。
2. **实现类的热部署和热替换**：在一些需要频繁更新代码的应用场景中，如开发环境或测试环境中，开发者可能希望在不重启应用的情况下更新类库或代码。这要求类加载器能够动态地加载新的类定义，并替换旧的类定义。然而，双亲委派机制限制了类加载的灵活性，使得一旦类被加载，其定义就不能再被修改。因此，在这些场景中，可能需要打破双亲委派机制，以实现类的热部署和热替换。

### 类加载的执行过程 (类的生命周期)

1. **加载** 
   1. 将类的二进制字节码载入JVM中 
   1. **然后将二进制字节码转换为方法区所理解的数据结构**
   1. 并在堆中生成一个java.lang.Class 对象，表示堆方法区中类的引用 

1. **连接**
   1. 验证：保证载入的类不会危害JVM 
   1. 准备：给静态变量常量设置默认值 
   1. 解析：将类中的符号引用转化为直接引用 

1. **初始化**：初始化**类的静态变量和静态常量**

1. **使用**：使用类或者创建对象

1. **卸载**：如果有下面的情况，类就会被卸载
   1.  该类所有的实例都已经被回收，也就是java堆中不存在该类的任何实例。

   1.  加载该类的ClassLoader已经被回收。 

   1. 类对应的java.lang.Class对象没有任何地方被引用，无法在任何地方通过反射访问该类的方法。


## 垃圾回收

除了Java语言，C#、Python等语言也都有自动的垃圾回收机制。

### minorGC、majorGC、fullGC的区别，什么场景触发full GC *

- Minor GC (Young GC)

  - **作用范围**：**只针对年轻代进行回收**，包括Eden区和两个Survivor区（S0和S1）。

  - **触发条件**：**当Eden区空间不足时**，JVM会触发一次Minor GC，将Eden区和一个Survivor区中的存活对象移动到另一个Survivor区或老年代（Old Generation）。

  - **特点**：通常发生得非常频繁，因为年轻代中对象的生命周期较短，回收效率高，暂停时间相对较短。

- Major GC

  - **作用范围**：主要针对老年代进行回收，**但不一定只回收老年代**。
    - **触发条件**：当老年代空间不足时，或者系统检测到年轻代对象晋升到老年代的速度过快，可能会触发Major GC。

  - **特点**：相比Minor GC，Major GC发生的频率较低，但每次回收可能需要更长的时间，因为老年代中的对象存活率较高。

- Full GC

  - **作用范围**：对**整个堆内存**（包括年轻代、老年代以及永久代/元空间）进行回收。

  - **触发条件**：
    - 直接调用`System.gc()`或`Runtime.getRuntime().gc()`方法时，虽然不能保证立即执行，但JVM会尝试执行Full GC。
    - Minor GC（新生代垃圾回收）时，如果存活的对象无法全部放入老年代，或者老年代空间不足以容纳存活的对象，则会触发Full GC，对整个堆内存进行回收。
    - 当永久代（Java 8之前的版本）或元空间（Java 8及以后的版本）空间不足时。

  - **特点**：Full GC是最昂贵的操作，因为它需要停止所有的工作线程（Stop The World），遍历整个堆内存来查找和回收不再使用的对象，因此应尽量减少Full GC的触发。

### 哪些可以作为GC Roots的对象

- 变量
  - 栈中的本地变量
  - 方法区中的静态变量

- 对象
  - 正在运行的线程对象
  - 被synchronized锁定的对象

- 还有很多就不多说了

### GC只会对堆进行GC吗？

JVM 的垃圾回收器不仅仅会对堆进行垃圾回收，它还会对方法区进行垃圾回收。

1. **堆（Heap）：** 堆是用于存储对象实例的内存区域。大部分的垃圾回收工作都发生在堆上，因为大多数对象都会被分配在堆上，而垃圾回收的重点通常也是回收堆中不再被引用的对象，以释放内存空间。
2. **方法区（Method Area）：** 方法区是用于存储类信息、常量、静态变量等数据的区域。虽然方法区中的垃圾回收与堆有所不同，但是同样存在对不再需要的常量、无用的类信息等进行清理的过程。

### 对象什么时候可以被垃圾器回收

简单一句就是：如果一个或多个对象没有任何的引用指向它了，那么这个对象现在就是垃圾，如果定位了垃圾，则有可能会被垃圾回收器回收。

如果要定位什么是垃圾，有两种方式来确定，第一个是引用计数法，第二个是可达性分析算法

#### 引用计数法

一个对象被引用了一次，在当前的对象头上递增一次引用次数，如果这个对象的引用次数为0，代表这个对象可回收

**当对象间出现了循环引用的话，则引用计数法就会失效**,也就是A引用B,B引用A则会出问题,因为正常来说给他们俩=null次数-1但是这里循环引用A被引用+1又被B引用+1就为2所以=null -1还是存在就不会被垃圾回收

优点：

- **实时性较高**，无需等到内存不够的时候，才开始回收，运行时根据对象的计数器是否为0，就可以直接回收。
- 在垃圾回收过程中，应用无需挂起。如果申请内存时，内存不足，则立刻报OOM错误。
- 区域性，更新对象的计数器时，只是影响到该对象，不会扫描全部对象。

缺点：

- 每次对象被引用时，都需要去更新计数器，有一点时间开销。 
- **浪费CPU资源**，即使内存够用，仍然在运行时进行计数器的统计。
- **无法解决循环引用问题，会引发内存泄露**。（最大的缺点） 

#### 可达性分析算法

存在一个根节点【GC Roots】，引出它下面指向的下一个节点，再以下一个节点节点开始找出它下面的节点，依次往下类推。直到所有的节点全部遍历完毕。

**根对象是那些肯定不能当做垃圾回收的对象，就可以当做根对象**

**举个例子**
节点ABCDE,A->B->C->D->E但是D->E也就是说DE是循环引用,然后我让他俩都为null这个时候没有东西指向他俩,只有他俩互相指向,GCroot不能到达他们那条线,这个线所以就垃圾回收解决了引用计数法的问题

### 垃圾回收算法 

#### 标记清除算法 

标记清除算法，是将垃圾回收分为2个阶段，分别是**标记和清除**。

1.根据可达性分析算法得出的垃圾进行标记

2.对这些标记为可回收的内容进行垃圾回收

可以看到，标记清除算法解决了引用计数算法中的循环引用的问题，没有从root节点引用的对象都会被回收。

同样，标记清除算法也是有缺点的：

- 效率较低，**标记和清除两个动作都需要遍历所有的对象**，并且在GC时，**需要停止应用程序**，对于交互性要求比较高的应用而言这个体验是非常差的。
- （**重要**）通过标记清除算法清理出来的内存，碎片化较为严重，因为被回收的对象可能存在于内存的各个角落，所以清理出来的内存是不连贯，标记清除之后会产生大量不连续的内存碎片，空间碎片太多可能会导致，当程序在以后的运行过程中需要分配较大对象时无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。。

####  复制算法

​	复制算法的核心就是，**将原有的内存空间一分为二，每次只用其中的一块**，在垃圾回收时，将正在使用的对象复制到另一个内存空间中，然后将该内存空间清空，交换两个内存的角色，完成垃圾的回收。

​	**如果内存中的垃圾对象较多，需要复制的对象就较少，这种情况下适合使用该方式并且效率比较高，反之，则不适合。** 

1. 将内存区域分成两部分，每次操作其中一个。
2. 当进行垃圾回收时，**将正在使用的内存区域中的存活对象移动到未使用的内存区域**。当移动完对这部分内存区域一次性清除。
3. 周而复始。

优点：

- 在垃圾对象多的情况下，效率较高
- 清理后，内存无碎片

缺点：

- 分配的2块内存空间，在同一个时刻，只能使用一半，内存使用率较低

#### 标记整理算法

​	标记压缩算法是在**标记清除算法的基础之上**，做了优化改进的算法。和标记清除算法一样，也是从根节点开始，对对象的引用进行标记，**在清理阶段，并不是简单的直接清理可回收对象，而是将存活对象都向内存另一端移动，然后清理边界以外的垃圾，从而解决了碎片化的问题。**

1. 标记垃圾。
2. 需要清除向右边走，不需要清除的向左边走。
3. 清除边界以外的垃圾。

**优缺点同标记清除算法，解决了标记清除算法的碎片化的问题，同时，标记压缩算法多了一步，对象移动内存位置的步骤，其效率也有有一定的影响。**

**与复制算法对比：复制算法标记完就复制，但标记整理算法得等把所有存活对象都标记完毕，再进行整理**

####  分代回收算法

##### 概述

在java8时，堆被分为了两份：**新生代和老年代【1：2】**，在java7时，还存在一个永久代。

对于新生代，内部又被分为了三个区域。Eden区，S0区，S1区【8：1：1】

当对新生代产生GC：MinorGC【young GC】

当对老年代产生GC：Major GC 

当对新生代和老年代产生FullGC： 新生代 + 老年代完整垃圾回收，暂停时间长，**应尽力避免**

##### 工作机制

1. 首先新创建的对象会被放在新生代,然后每次垃圾回收标记出存活对象,就会将存活对象放到另一个空闲的幸存者区
2. 第二次同样是伊甸园区和非空闲的幸存者区标记存活对象,然后复制到另一个幸存者区
3. 这样循环次数多了之后,就会把存活时间长的对象存放到老年代区域里面,因为老年代就是用来保存存活时间长的对象

##### **Young GC（Minor GC）**、**Old GC（Major GC）**、 Mixed GC 、 FullGC的区别是什么

**Mixed** GC

- Mixed GC 新生代 + 老年代部分区域的垃圾回收，G1 收集器特有

**Young GC（Minor GC）**

- **定义**：Young GC，也被称为**Minor GC**，是针对**新生代的内存区域进行的垃圾回收**。新生代是JVM堆内存的一部分，主要用于存放生命周期较短的对象。
- **运行条件**：当**新生代内存空间不足时，会触发Young GC。**
- **回收对象**：主要回收新生代中的伊甸区和幸存者区中的垃圾对象，并将存活的对象复制到另一个幸存者区（如果当前幸存者区空间不足，则可能晋升到老年代）。
- **特点**：Young GC通常比较频繁，但**速度较快**，对系统**性能的影响相对较小。**

**Old GC（Major GC）**

- **定义**：Old GC，有时也被称为Major GC，是**针对老年代的内存区域进行的垃圾回收**。老年代主要用于存放生命周期较长的对象。
- **运行条件**：当**老年代内存空间不足时，会触发Old GC。**
- **回收对象**：主要回收老年代中的垃圾对象。
- **特点**：Old GC的执行频率相对较低，因为老年代中的对象生命周期较长，不容易产生大量垃圾。然而，Old GC的耗时通常比Young GC要长，因为它需要扫描老年代中更多的对象。

**Full GC**

- **定义**：Full GC是一种针对整个堆内存（包括新生代和老年代）以及永久代（在Java 8及之前版本存在，Java 8及之后被元空间取代）进行垃圾回收的过程。
- **运行条件**：当整个**堆内存空间不足**时，或者在某些特定情况下（如**System.gc()被显式调用**、永久代/**元空间不足**等），会触发Full GC。
- **回收对象**：Full GC会同时**回收新生代、老年代以及永久代/元空间**中的垃圾对象。
- **特点**：Full GC是最耗时的垃圾回收过程，因为它需要扫描整个堆内存。此外，Full GC**会暂停除了GC线程外的所有线程，这种暂停时间相对较长，对系统性能的影响较大。**

#### G1垃圾回收算法

##### 新生代回收

- 初始时，所有区域都处于空闲状态
- 创建了一些对象，挑出一些空闲区域作为伊甸园区存储这些对象
- 当伊甸园需要垃圾回收时，挑出一个空闲区域作为幸存区，用复制算法复制存活对象，需要暂停用户线程
- 随着时间流逝，伊甸园的内存又有不足
- 将伊甸园以及之前幸存区中的存活对象，采用复制算法，复制到新的幸存区，其中较老对象晋升至老年代

##### 并发标记

**当老年代占用内存超过阈值(默认是45%)**后，触发并发标记，这时**无需暂停用户线程**

- 并发标记之后，会有重新标记阶段解决漏标问题，此时需要暂停用户线程。
- 这些都完成后就知道了老年代有哪些存活对象，随后进入混合收集阶段。此时不会对所有老年代区域进行回收，而是根据暂停时间目标优先回收价值高（存活对象少）的区域（这也是 Gabage First 名称的由来）。

##### 混合收集

复制完成，内存得到释放。进入下一轮的新生代回收、并发标记、混合收集

其中H叫做巨型对象，如果对象非常大，会开辟一块连续的空间存储巨型对象

#### 垃圾回收算法总结

1. 标记清除
   1. 缺点
      1. 碎片化严重,空间碎片太多可能会导致，当程序在以后的运行过程中需要分配较大对象时无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。
      2. 标记和清除都需要遍历对象,效率低
2. 标记整理
   1. 优点:在标记清除之上多了个整理步骤,解决碎片化问题
   2. 缺点:同标记清除
3. 复制算法
   1. 优点
      1. 解决碎片化问题
      2. 效率相对高
   2. 缺点
      1. 需要浪费一半空间
      2. 当存活对象比垃圾对象多的时候,效率相对不够好,因为他要一次次复制存活对象

#### 垃圾回收算法哪些阶段会STW?

G1的**混合回收**过程可以分为标记阶段、清理阶段和复制阶段。

**标记阶段停顿分析**

- 初始标记阶段：初始标记阶段是指从GC Roots出发标记全部直接子节点的过程，该阶段是STW的。由于GC Roots数量不多，通常该阶段耗时非常短。 yes
- 并发标记阶段：并发标记阶段是指从GC Roots开始对堆中对象进行可达性分析，找出存活对象。该阶段是并发的，即应用线程和GC线程可以同时活动。并发标记耗时相对长很多，但因为不是STW，所以我们不太关心该阶段耗时的长短。 no
- 再标记阶段：重新标记那些在并发标记阶段发生变化的对象。该阶段是STW的。 yes

**清理阶段停顿分析**

- 清理阶段**清点出有存活对象的分区和没有存活对象**的分区，该阶段不会清理垃圾对象，也不会执行存活对象的复制。该阶段是STW的。 yes

**复制阶段停顿分析**

- 复制算法中的转移阶段需要分配新内存和复制对象的成员变量。转移阶段是STW的，其中内存分配通常耗时非常短，但对象成员变量的复制耗时有可能较长，这是因为复制耗时与存活对象数量与对象复杂度成正比。对象越复杂，复制耗时越长。 yes

四个STW过程中

- 初始标记因为只标记GC Roots，耗时较短。
- 再标记因为对象数少，耗时也较短。
- 清理阶段因为内存分区数量少，耗时也较短。
- 转移阶段要处理所有存活的对象，耗时会较长。

因此，G1停顿时间的瓶颈主要是标记-复制中的转移阶段STW。  (**重点**)

### 垃圾回收器  *

- 串行垃圾收集器:使用单线程进行垃圾回收，堆内存较小，适合个人电脑
- 并行垃圾收集器:**JDK8默认使用此垃圾回收器**
- CMS（并发）垃圾收集器:特点是在进行垃圾回收时，应用仍然能正常运行,其他垃圾回收都要暂停线程。
- G1垃圾收集器

#### G1垃圾回收器

**概述**

- 应用于新生代和老年代，**在JDK9之后默认使用G1**
- 划分成多个区域，每个区域都可以充当 eden，survivor，old， humongous，其中 humongous 专为大对象准备
- 采用复制算法
- 分成三个阶段：新生代回收、并发标记、混合收集
- 如果**并发失败（即回收速度赶不上创建新对象速度），会触发 Full GC**

### 强引用、软引用、弱引用、虚引用的区别？

#### 强引用

强引用：永远不会被GC回收,就算不使用这个对象JVM也不会对他垃圾回收,这也是Java内存泄漏的主要原因。

User user = new User();

#### 软引用

软引用：系统在发生内存溢出前会对这类引用的对象进行回收。

```JAVA
User user = new User();
SoftReference softReference = new SoftReference(user);
```

#### 弱引用

弱引用：弱引用的对象下一次GC的时候一定会被回收，而不管内存是否足够。

```JAVA 
User user = new User();
WeakReference weakReference = new WeakReference(user);
```

延伸话题：ThreadLocal内存泄漏问题

ThreadLocal用的就是弱引用，看以下源码：

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
         super(k);
         value = v; //强引用，不会被回收
     }
}
```

`Entry`的key是当前ThreadLocal，value值是我们要设置的数据。

`WeakReference`表示的是弱引用，当JVM进行GC时，一旦发现了只具有弱引用的对象，不管当前内存空间是否足够，都会回收它的内存。但是`value`是强引用，它不会被回收掉。

ThreadLocal使用建议：使用完毕后注意调用清理方法。

#### 虚引用

虚引用：必须配合引用队列使用，被引用对象回收时，会将虚引用入队，由 Reference Handler 线程调用虚引用相关方法释放直接内存

# JVM实践（调优）

### JVM 调优的参数可以在哪里设置参数值？

难易程度：☆☆

出现频率：☆☆☆

#### tomcat的设置vm参数

修改TOMCAT_HOME/bin/catalina.sh文件，如下图

```
JAVA_OPTS="-Xms512m -Xmx1024m"
```

#### springboot项目jar文件启动

通常在linux系统下直接加参数启动springboot项目

nohup java -Xms512m -Xmx1024m -jar xxxx.jar --spring.profiles.active=prod &

nohup : 用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行

参数 **&** ：让命令在后台执行，终端退出后命令仍旧执行。

### 用的 JVM 调优的参数都有哪些？

难易程度：☆☆☆

出现频率：☆☆☆☆

​	对于JVM调优，主要就是调整年轻代、年老大、元空间的内存空间大小及使用的垃圾回收器类型。

https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html

1）设置堆的初始大小和最大大小，为了防止垃圾收集器在初始大小、最大大小之间收缩堆而产生额外的时间，通常把最大、初始大小设置为相同的值。

```plain
-Xms：设置堆的初始化大小

-Xmx：设置堆的最大大小
```

2） 设置年轻代中Eden区和两个Survivor区的大小比例。该值如果不设置，则默认比例为8:1:1。Java官方通过增大Eden区的大小，来减少YGC发生的次数，但有时我们发现，虽然次数减少了，但Eden区满

的时候，由于占用的空间较大，导致释放缓慢，此时STW的时间较长，因此需要按照程序情况去调优。

-XXSurvivorRatio=3，表示年轻代中的分配比率：survivor:eden = 2:3

3）年轻代和老年代默认比例为1：2。可以通过调整二者空间大小比率来设置两者的大小。

```plain
-XX:newSize   设置年轻代的初始大小
-XX:MaxNewSize   设置年轻代的最大大小，  初始大小和最大大小两个值通常相同
```

4）线程堆栈的设置：**每个线程默认会开启1M的堆栈**，用于存放栈帧、调用参数、局部变量等，但一般256K就够用。通常减少每个线程的堆栈，可以产生更多的线程，但这实际上还受限于操作系统。

-Xss   对每个线程stack大小的调整,-Xss128k

5）一般来说，当survivor区不够大或者占用量达到50%，就会把一些对象放到老年区。通过设置合理的eden区，survivor区及使用率，可以将年轻对象保存在年轻代，从而避免full GC，使用-Xmn设置年轻代的大小

6）系统CPU持续飙高的话，首先先排查代码问题，如果代码没问题，则咨询运维或者云服务器供应商，通常服务器重启或者服务器迁移即可解决。

7）对于占用内存比较多的大对象，一般会选择在老年代分配内存。如果在年轻代给大对象分配内存，年轻代内存不够了，就要在eden区移动大量对象到老年代，然后这些移动的对象可能很快消亡，因此导致full GC。通过设置参数：-XX:PetenureSizeThreshold=1000000，单位为B，标明对象大小超过1M时，在老年代(tenured)分配内存空间。

8）一般情况下，年轻对象放在eden区，当第一次GC后，如果对象还存活，放到survivor区，此后，每GC一次，年龄增加1，当对象的年龄达到阈值，就被放到tenured老年区。这个阈值可以同构-XX:MaxTenuringThreshold设置。如果想让对象留在年轻代，可以设置比较大的阈值。

```plain
（1）-XX:+UseParallelGC:年轻代使用并行垃圾回收收集器。这是一个关注吞吐量的收集器，可以尽可能的减少垃圾回收时间。

（2）-XX:+UseParallelOldGC:设置老年代使用并行垃圾回收收集器。
```

9）尝试使用大的内存分页：使用大的内存分页增加CPU的内存寻址能力，从而系统的性能。

-XX:+LargePageSizeInBytes 设置内存页的大小

10）使用非占用的垃圾收集器。

-XX:+UseConcMarkSweepGC老年代使用CMS收集器降低停顿。

### 说一下 JVM 调优的工具？

难易程度：☆☆

出现频率：☆☆

#### 命令工具

##### jps（Java Process Status）

输出JVM中运行的进程状态信息(现在一般使用jconsole)

##### jstack

查看java进程内**线程的堆栈**信息。

jstack [option] <pid>  

java案例

```plain
package com.heima.jvm;

public class Application {

    public static void main(String[] args) throws InterruptedException {
        while (true){
            Thread.sleep(1000);
            System.out.println("哈哈哈");
        }
    }
}
```

使用jstack查看进行堆栈运行信息

##### jmap

用于生成堆转存快照

jmap [options] pid 内存映像信息

jmap -heap pid 显示Java堆的信息

jmap -dump:format=b,file=heap.hprof pid

​		format=b表示以hprof二进制格式转储Java堆的内存		file=<filename>用于指定快照dump文件的文件名。

例：显示了某一个java运行的堆信息

```plain
C:\Users\yuhon>jmap -heap 53280
Attaching to process ID 53280, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.321-b07

using thread-local object allocation.
Parallel GC with 8 thread(s)   //并行的垃圾回收器

Heap Configuration:  //堆配置
   MinHeapFreeRatio         = 0   //空闲堆空间的最小百分比
   MaxHeapFreeRatio         = 100  //空闲堆空间的最大百分比
   MaxHeapSize              = 8524922880 (8130.0MB) //堆空间允许的最大值
   NewSize                  = 178257920 (170.0MB) //新生代堆空间的默认值
   MaxNewSize               = 2841640960 (2710.0MB) //新生代堆空间允许的最大值
   OldSize                  = 356515840 (340.0MB) //老年代堆空间的默认值
   NewRatio                 = 2 //新生代与老年代的堆空间比值，表示新生代：老年代=1：2
   SurvivorRatio            = 8 //两个Survivor区和Eden区的堆空间比值为8,表示S0:S1:Eden=1:1:8
   MetaspaceSize            = 21807104 (20.796875MB) //元空间的默认值
   CompressedClassSpaceSize = 1073741824 (1024.0MB) //压缩类使用空间大小
   MaxMetaspaceSize         = 17592186044415 MB //元空间允许的最大值
   G1HeapRegionSize         = 0 (0.0MB)//在使用 G1 垃圾回收算法时，JVM 会将 Heap 空间分隔为若干个 Region，该参数用来指定每个 Region 空间的大小。

Heap Usage:
PS Young Generation
Eden Space: //Eden使用情况
   capacity = 134217728 (128.0MB)
   used     = 10737496 (10.240074157714844MB)
   free     = 123480232 (117.75992584228516MB)
   8.000057935714722% used
From Space: //Survivor-From 使用情况
   capacity = 22020096 (21.0MB)
   used     = 0 (0.0MB)
   free     = 22020096 (21.0MB)
   0.0% used
To Space: //Survivor-To 使用情况
   capacity = 22020096 (21.0MB)
   used     = 0 (0.0MB)
   free     = 22020096 (21.0MB)
   0.0% used
PS Old Generation  //老年代 使用情况
   capacity = 356515840 (340.0MB)
   used     = 0 (0.0MB)
   free     = 356515840 (340.0MB)
   0.0% used

3185 interned Strings occupying 261264 bytes.
```

##### 4.3.1.4 jhat

用于分析jmap生成的堆转存快照（一般不推荐使用，而是使用Ecplise Memory Analyzer）

##### 4.3.1.5 jstat

是JVM统计监测工具。可以用来显示垃圾回收信息、类加载信息、新生代统计信息等。

**常见参数**：

①总结垃圾回收统计

jstat -gcutil pid

| **字段** | **含义**               |
| -------- | ---------------------- |
| S0       | 幸存1区当前使用比例    |
| S1       | 幸存2区当前使用比例    |
| E        | 伊甸园区使用比例       |
| O        | 老年代使用比例         |
| M        | 元数据区使用比例       |
| CCS      | 压缩使用比例           |
| YGC      | 年轻代垃圾回收次数     |
| YGCT     | 年轻代垃圾回收消耗时间 |
| FGC      | 老年代垃圾回收次数     |
| FGCT     | 老年代垃圾回收消耗时间 |
| GCT      | 垃圾回收消耗总时间     |

②垃圾回收统计

jstat -gc pid

#### 4.3.2 可视化工具

##### 4.3.2.1 jconsole

用于对jvm的内存，线程，类 的监控，是一个基于 jmx 的 GUI 性能监控工具

打开方式：java 安装目录 bin目录下 直接启动 jconsole.exe 就行

可以内存、线程、类等信息

##### 4.3.2.2 VisualVM：故障处理工具

能够监控线程，内存情况，查看方法的CPU时间和内存中的对 象，已被GC的对象，反向查看分配的堆栈

打开方式：java 安装目录 bin目录下 直接启动 jvisualvm.exe就行

监控程序运行情况

查看运行中的dump

查看堆中的信息

### 4.4 java内存泄露的排查思路？

难易程度：☆☆☆☆

出现频率：☆☆☆☆

原因：

如果线程请求分配的栈容量超过java虚拟机栈允许的最大容量的时候，java虚拟机将抛出一个StackOverFlowError异常

如果java虚拟机栈可以动态拓展，并且扩展的动作已经尝试过，但是目前无法申请到足够的内存去完成拓展，或者在建立新线程的时候没有足够的内存去创建对应的虚拟机栈，那java虚拟机将会抛出一个OutOfMemoryError异常

如果一次加载的类太多，元空间内存不足，则会报OutOfMemoryError: Metaspace

1、通过jmap指定打印他的内存快照 dump

有的情况是内存溢出之后程序则会直接中断，而jmap只能打印在运行中的程序，所以建议通过参数的方式的生成dump文件，配置如下：

-XX:+HeapDumpOnOutOfMemoryError-XX:HeapDumpPath=/home/app/dumps/ 指定生成后文件的保存目录

2、通过工具， VisualVM（Ecplise MAT）去分析 dump文件

VisualVM可以加载离线的dump文件，如下图

文件-->装入--->选择dump文件即可查看堆快照信息

如果是linux系统中的程序，则需要把dump文件下载到本地（windows环境）下，打开VisualVM工具分析。VisualVM目前只支持在windows环境下运行可视化

3、通过查看堆信息的情况，可以大概定位内存溢出是哪行代码出了问题

4、找到对应的代码，通过阅读上下文的情况，进行修复即可

### 4.5 CPU飙高排查方案与思路？

难易程度：☆☆☆☆

出现频率：☆☆☆☆

1.使用top命令查看占用cpu的情况

2.通过top命令查看后，可以查看是哪一个进程占用cpu较高，上图所示的进程为：30978

3.查看当前线程中的进程信息

ps H -eo pid,tid,%cpu | grep 40940

pid 进行id

tid 进程中的线程id

% cpu使用率 

4.通过上图分析，在进程30978中的线程30979占用cpu较高

注意：上述的线程id是一个十进制，我们需要把这个线程id转换为16进制才行，因为通常在日志中展示的都是16进制的线程id名称

转换方式：

在linux中执行命令

```
printf "%x\n" 30979
```

5.可以根据线程 id 找到有问题的线程，进一步定位到问题代码的源码行号

执行命令

jstack 30978   此处是进程id·