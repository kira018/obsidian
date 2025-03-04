

# 线程基础知识

## 进程、线程、协程 

### 进程

**进程**是应用程序运行的载体，可看做是正在执行的程序。程序本身是没有生命周期的，只是存在于磁盘上的一些指令集合，但程序一旦被运行起来就是进程（可以生成子进程，例如浏览器）。

启动后的进程，会依赖操作系统的调度完成生命周期的转换。<font color="#92d050">每个进程都有独立的代码和数据空间，进程之间不共享资源，这会导致进程间的通信很不方便</font>，举个例子，比如去做一个输入法，那么他就要同时支持键盘的监听和输入内容的运算，如果这个功能使用子进程来实现，那么每一个子进程间的交互就是一个很大的问题，所以就引入了线程

一个进程可以包含多个线程，这些<font color="#92d050">线程可以并发运行，并且共享进程提供的相同的代码和数据空间，每个线程都有自己独立的运行栈和程序计数器，所以他们的切换会更快，数据交互也会更加的容易</font>

### 线程

- 一个进程有多个线程
- 线程是最小的调度单位
- 线程与线程之间可以共享进程的的空间资源


线程分为<span style="background:#fff88f">内核态线程</span>和<span style="background:#fff88f">用户态线程</span> 

用户态线程切换通常只涉及用户空间的线程上下文，比如线程栈、程序计数器等，这些信息在用户空间内即可管理，无需内核介入，不过难以处理一些系统级的任务。而内核态线程切换涉及到更多的上下文信息，比如CPU寄存器、程序计数器、内核栈等。对应的他可以更好的处理系统级的任务

### 协程

而协程的话，属于一种轻量级的线程，一个线程可以有多个协程。并且协程不由CPU调度，并且协程之间不是抢占式的执行，而是协作式的执行，只有当一个协程让出CPU后才会让下一个协程执行

### 进程与线程的区别

- 进程是正在运行程序的实例，**进程中包含了线程**，每个线程执行不同的任务
- 不同的**进程使用不同的内存空间**，在当前进程下的所有**线程可以共享内存空间**
- 线程更轻量，**线程上下文切换成本一般上要比进程上下文切换低**(上下文切换指的是从一个线程切换到另一个线程)
- **线程中包含协程**,只使用用户态空间,**不涉及用户态内核态的切换**

### 查看进程与线程的方法

windows

- tasklist 查看进程
- taskkill 杀死进程

linux

- ps -fe 查看所有进程
- ps -fT -p <PID> 查看某个进程（PID）的所有线程
- kill 杀死进程
- top 按大写 H 切换是否显示线程
- top -H -p <PID> 查看某个进程（PID）的所有线程

Java

- jps 命令查看所有 Java 进程
- jstack <PID> 查看某个 Java 进程（PID）的所有线程状态
- jconsole 来查看某个 Java 进程中线程的运行情况（图形界面）

## 守护线程和用户线程

- 用户线程

  - 用户线程是程序的主要工作线程，用于执行程序的主要业务逻辑和任务。

  - 在Java中，默认情况下创建的线程都是用户线程。

  - 用户线程会完成程序需要完成的业务操作，如输入输出、计算等。

- 守护线程

  - 守护线程是一种特殊类型的线程，主要用于在后台默默地为用户线程提供服务。

  - 守护线程通常执行一些系统性的服务，如垃圾回收、JIT（即时编译器）线程等。

  - 当JVM中所有用户线程结束时，不管守护线程是否还在运行，JVM都会退出，守护线程也会随之结束。

## 串行、并行、并发 

**串行**

串行简单来说就是一次只能做一件事情，而且还得按照顺序依次执行，后面的代码段必须等到前面代码段的任务执行完毕后才能执行。

**并发**

**并发是单核CPU提出的**

指的是两个或俩个以上的线程同时请求执行，但是同一瞬间CPU只能执行一个，于是CPU就安排他们**交替**执行，我们看起来好像是同时执行的，其实不是,简单来说其实宏观来讲是并发,微观来讲是串行

**并行**

**并行则是针对多核 CPU 提出的。**

并行指的是在同一时刻，任务可以同时开始进行，彼此之间没有依赖关系。整个周期的总耗时取决于耗时最长的那件事情所需的时间。

**并行与并发区别**

并发是针对单核 CPU 提出的，而并行则是针对多核 CPU 提出的。

![img](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/202410142139723.png)

## 同步和异步

以调用方角度来讲，如果

- **需要等待结果返回**，才能继续运行就是同步
- **不需要等待结果返回**，就能继续运行就是异步

**使用异步的场景**:

- 比如在项目中，视频文件需要转换格式等操作比较费时，这时开一个新线程处理视频转换，避免阻塞主线程
- 发送邮件,发送报警
- 定时任务批量删除文件,可以开个异步线程
- 如果远程调用上下游互不影响互不依赖就可以使用异步解耦提升性能

### 案例

充分利用多核 cpu 的优势，提高运行效率。想象下面的场景，执行 3 个计算，最后将计算结果汇总。

```java
计算 1 花费 10 ms
计算 2 花费 11 ms
计算 3 花费 9 ms
汇总需要 1 ms
```

- 如果是串行执行，那么总共花费的时间是 10 + 11 + 9 + 1 = 31ms
- 但如果是四核 cpu，那么 3 个线程是并行的，**花费时间只取决于最长的那个线程运行的时间**，即 11ms 最后加上汇总时间只会花费 12ms

**注意**

需要在多核 cpu 才能提高效率，单核仍然时是轮流执行

## 线程间有哪些通信方式？

线程之间传递信息有多种方式，每种方式适用于不同的场景。比如说使用共享对象、`wait()` 和 `notify()`、Exchanger 和 CompletableFuture。

①、**使用共享对象**，多个线程可以访问和修改同一个对象，从而实现信息的传递，比如说 volatile 和 synchronized 关键字。

**关键字 volatile**用来修饰成员变量，告知程序任何对该变量的访问均需要从共享内存中获取，而对它的改变必须同步刷新回共享内存，保证所有线程对变量访问的可见性。

**关键字 synchronized**可以修饰方法，或者以同步代码块的形式来使用，确保多个线程在同一个时刻，只能有一个线程在执行某个方法或某个代码块。

②、**使用 wait() 和 notify()**，例如，生产者-消费者模式中，生产者生产数据，消费者消费数据，通过 `wait()` 和 `notify()` 方法可以实现生产和消费的协调。

一个线程调用共享对象的 `wait()` 方法时，它会进入该对象的等待池，并释放已经持有的该对象的锁，进入等待状态，直到其他线程调用相同对象的 `notify()` 或 `notifyAll()` 方法。

一个线程调用共享对象的 `notify()` 方法时，它会唤醒在该对象等待池中等待的一个线程，使其进入锁池，等待获取锁。

③、**使用 Exchanger**，Exchanger 是一个同步点，可以在两个线程之间交换数据。一个线程调用 exchange() 方法，将数据传递给另一个线程，同时接收另一个线程的数据。

```java
import java.util.concurrent.Exchanger;

public class Main {
    public static void main(String[] args) {
        Exchanger<String> exchanger = new Exchanger<>();

        Thread thread1 = new Thread(() -> {
            try {
                String message = "Message from thread1";
                String response = exchanger.exchange(message);
                System.out.println("Thread1 received: " + response);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread thread2 = new Thread(() -> {
            try {
                String message = "Message from thread2";
                String response = exchanger.exchange(message);
                System.out.println("Thread2 received: " + response);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        thread1.start();
        thread2.start();
    }
} n
```

**结果**

```
Thread2 received: Message from thread1
Thread1 received: Message from thread2
```

④、**使用 CompletableFuture**，CompletableFuture 是 Java 8 引入的一个类，支持异步编程，允许线程在完成计算后将结果传递给其他线程。

```java
    public static void main(String[] args) {
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            // 模拟长时间计算
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            return "Message from CompletableFuture";
        });
        future.thenAccept(message -> {
            System.out.println("线程2开始拿到了异步编排数据了");
            System.out.println("Received: " + message);
        });
        System.out.println("线程1执行了");

    }
```



## 线程的 run()和 start()有什么区别？

- start方法用来启动线程，通过该线程调用run方法执行run方法中所定义的逻辑代码。

- start方法只能被调用一次。run方法封装了要被线程执行的代码，可以被调用多次。

## runnable 和 callable 两个接口创建线程有什么不同呢？

- 最主要的两个线程一个是有返回值，一个是没有返回值的。

- Runnable 接口run方法无返回值；
- Callable接口call方法有返回值，是个泛型，配和Future、FutureTask配合可以用来获取异步执行的结果

##  wait 和 sleep方法有什么不同？

**相同点**

它们两个的相同点是都可以让当前线程暂时**放弃 CPU 的使用权，进入阻塞状态**。

**不同点**

第一：方法归属不同

sleep(long) 是 Thread 的静态方法。而 wait()，是 Object 的成员方法，每个对象都有

第二：线程醒来时机不同

线程执行 sleep(long) 会在等待相应毫秒后醒来，而 wait() 需要被 notify 唤醒，wait() 如果不唤醒就一直等下去

第三：锁特性不同

wait 方法的调用必须先获取 wait 对象的锁，而 sleep 则无此限制

wait 方法执行后会释放对象锁，允许其它线程获得该对象锁（相当于我放弃 cpu，但你们还可以用）

而 sleep 如果在 synchronized 代码块中执行，并不会释放对象锁（相当于我放弃 cpu，你们也用不了）

## T1、T2、T3 三个线程，如何保证它们按顺序执行？

在多线程中有多种方法让线程按特定顺序执行，可以用线程类的**join**()方法在一个线程中启动另一个线程，另外一个线程完成该线程继续执行。

比如说：

使用join方法，T3调用T2，T2调用T1，这样就能确保T1就会先完成而T3最后完成

## 如何停止一个线程的运行?

1. 主要有这些方法：
   - **异常法停止**：线程调用interrupt()方法后，在线程的run方法中判断当前对象的interrupted()状态，如果是中断状态则抛出异常，达到中断线程的效果。
   - **在沉睡中停止**：先将线程sleep，**然后调用interrupt标记中断状态**，**interrupt会将阻塞状态的线程中断。**会抛出中断异常，达到停止线程的效果
   - **stop()暴力停止**：**线程调用stop()方法会被暴力停止**，方法已弃用，该方法会有不好的后果：强制让线程停止有可能使一些请理性的工作得不到完成。
   - **使用return停止线程**：**调用interrupt标记为中断状态后，在run方法中判断当前线程状态，如果为中断状态则return，**能达到停止线程的效果。

## JMM内存模型 

回答tips:你可以扯一嘴,是这样的面试官,我学习知识喜欢研究这个东西为什么需要他,再去了解他的作用是什么,不然直接看他作用是什么会有一种空中阁楼的感觉

**为什么需要JMM?**

是这样的,在计算机里面有cpu和主内存这个概念,那cpu里面又有寄存器,寄存器其实就是算是一个缓存他的性能非常高相比于主内存来说,那么显然在多核的情况下有多个cpu对吧,那你在操作变量的时候肯定选择性能高的cpu来操作而不是主内存,那么这样就会出现一个问题,你一个共享变量需要被多个cpu操作会出现可见性的这么一个问题,可见性其实就是线程A修改了共享数据线程B不知情,就不可见,所以JMM就是用来解决这个问题得,他保证你高性能的操作共享数据又不会出现可见性的这种问题

JMM**解决了什么问题**?

1. 线程可见性问题,以及线程安全问题
   1. 在jdk1.2之前cpu直接读取主内存,那么当出现多个cpu读取主内存的共享变量的时候是一定会出现可见性问题的

1. 解决指令重排序的问题,定义了一套规范
   1. JMM 抽象了 happens-before 原则来解决这个指令重排序问题。

## 线程的上下文切换

> 首先，线程为什么要上下文切换？

使用多线程的目的是为了充分利用 CPU，但是我们知道，并发其实是一个 CPU 来应付多个线程。

为了让用户感觉多个线程是在同时执行的， CPU 资源的分配采用了时间片轮转也就是给每个线程分配一个时间片，线程在时间片内占用 CPU 执行任务。当线程使用完时间片后，就会处于就绪状态并让出 CPU 让其他线程占用，这就是上下文切换。

> 那么什么情况会出现上下文切换呢？

因为以下一些原因导致 cpu 不再执行当前的线程，转而执行另一个线程的代码

- 线程的 cpu 时间片用完
- 垃圾回收
- 有更高优先级的线程需要运行
- 线程自己调用了 sleep、yield、wait、join、park、synchronized、lock 等方法

## 线程运行的常见方法

### start 与 run

- 直接调用 run 是在主线程中执行了 run，没有启动新的线程
- 使用 start 是启动新的线程，通过新的线程间接执行 run 中的代码

### sleep 与 yield

sleep

1. 调用 sleep 会让当前线程从 Running 进入 Timed Waiting 状态（阻塞）

2. 其它线程可以使用 interrupt 方法打断正在睡眠的线程，这时 sleep 方法会抛出 InterruptedException

3. 睡眠结束后的线程未必会立刻得到执行

4. 建议用 TimeUnit 的 sleep 代替 Thread 的 sleep 来获得更好的可读性

yield

1. 调用 yield 会让当前线程从 Running 进入 Runnable 就绪状态，然后调度执行其它线程

2. 具体的实现依赖于操作系统的任务调度器,有可能你交给别人执行,但是任务调度器认为给你更好,最后还是你执行

### join

join是用来在多线程的情况下,**让线程与线程之间同步的命令**

比如我线程B想等待线程A执行完之后执行,我就需要使用join,当然区别于单线程,这里虽然是要等待线程A执行完在执行,但是join可以在别的线程执行的时候自己也执行,只是最后结果需要等待别的线程,举个例子

#### 有时效的 join

多线程情况下join(2秒)当前线程等待俩秒之后就不会继续等了直接执行,也就是说不会一直等你执行完在执行,只能等有限时间

### interrupt 方法

打断 sleep，wait，join 的线程

- 这几个方法都会让线程进入阻塞状态,打断阻塞的线程会抛异常提示,sleep interrupted

打断正常运行的线程

- 打断正常运行的线程, 不会清空打断状态

## 使用多线程要注意哪些问题？

Java的线程安全在三个方面体现：

- **原子性**：提供互斥访问，同一时刻只能有一个线程对数据进行操作，在Java中使用了atomic和synchronized这两个关键字来确保原子性；
- **可见性**：一个线程对主内存的修改可以及时地被其他线程看到，在Java中使用了synchronized和volatile这两个关键字确保可见性；
- **有序性**：一个线程观察其他线程中的指令执行顺序，由于指令重排序，该观察结果一般杂乱无序，在Java中使用了happens-before原则来确保有序性。

## 保证数据的一致性有哪些方案呢？

- **事务管理**：使用数据库事务来确保一组数据库操作要么全部成功提交，要么全部失败回滚。通过ACID（原子性、一致性、隔离性、持久性）属性，数据库事务可以保证数据的一致性。
- **锁机制**：使用锁来实现对共享资源的互斥访问。在 Java 中，可以使用 synchronized 关键字、ReentrantLock 或其他锁机制来控制并发访问，从而避免并发操作导致数据不一致。
- **版本控制**：通过乐观锁的方式，在更新数据时记录数据的版本信息，从而避免同时对同一数据进行修改，进而保证数据的一致性。

## 线程的创建方式有哪些? 

1.继承Thread类  

这是最直接的一种方式，用户自定义类继承java.lang.Thread类，重写其run()方法，run()方法中定义了线程执行的具体任务。创建该类的实例后，通过调用start()方法启动线程。

```java
class MyThread extends Thread {
    @Override
    public void run() {
        // 线程执行的代码
    }
}

public static void main(String[] args) {
    MyThread t = new MyThread();
    t.start();
}
```

采用继承Thread类方式

- **优点**: **编写简单**，如果需要访问当前线程，无需使用Thread.currentThread ()方法，直接使用this，即可获得当前线程
- **缺点**:因为线程类已经继承了Thread类，所以**不能再继承其他的父类**

2.实现Runnable接口

Runnable底层实际上还是Thread类如果一个类已经继承了其他类，就不能再继承Thread类，此时可以实现java.lang.Runnable接口。实现Runnable接口需要重写run()方法，然后将此Runnable对象作为参数传递给Thread类的构造器，创建Thread对象后调用其start()方法启动线程。

```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 线程执行的代码
    }
}

public static void main(String[] args) {
    Thread t = new Thread(new MyRunnable());
    t.start();
}
```

采用实现Runnable接口方式： 

- 优点：线程类只是实现了Runable接口，还**可以继承其他的类**。在这种方式下**，可以多个线程共享同一个目标对象**，所以**非常适合多个相同线程来处理同一份资源的情况**，从而可以将CPU代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。
- 缺点：**编程稍微复杂，如果需要访问当前线程，必须使用Thread.currentThread()方法**。

1. 实现Callable接口与FutureTask

java.util.concurrent.Callable接口类似于Runnable，但Callable的call()方法**可以有返回值并且可以抛出异常**。要执行Callable任务，需将它包装进一个**FutureTask**，因为Thread类的构造器只接受Runnable参数，而FutureTask实现了Runnable接口。

```java
class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // 线程执行的代码，这里返回一个整型结果
        return 1;
    }
}

public static void main(String[] args) {
    MyCallable task = new MyCallable();
    FutureTask<Integer> futureTask = new FutureTask<>(task);
    Thread t = new Thread(futureTask);
    t.start();

    try {
        Integer result = futureTask.get();  // 获取线程执行结果
        System.out.println("Result: " + result);
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
}
```

采用实现Callable接口方式：

- 缺点：编程稍微复杂，如果需要访问当前线程，必须调用Thread.currentThread()方法。
- 优点：线程只是实现Runnable或实现Callable接口，还可以继承其他类。这种方式下，多个线程可以共享一个target对象，非常适合多线程处理同一份资源的情形。

1. 使用线程池（Executor框架） 

从Java 5开始引入的java.util.concurrent.ExecutorService和相关类提供了线程池的支持，这是一种更高效的线程管理方式，避免了频繁创建和销毁线程的开销。可以通过Executors类的静态方法创建不同类型的线程池。

```java
class Task implements Runnable {
    @Override
    public void run() {
        // 线程执行的代码
    }
}

public static void main(String[] args) {
    ExecutorService executor = Executors.newFixedThreadPool(10);  // 创建固定大小的线程池
    for (int i = 0; i < 100; i++) {
        executor.submit(new Task());  // 提交任务到线程池执行
    }
    executor.shutdown();  // 关闭线程池
}
```

采用线程池方式：

- 缺点：**程池增加了程序的复杂度**，特别是当涉及线程池参数调整和故障排查时。**错误的配置可能导致死锁、资源耗尽等问题**，这些问题的诊断和修复可能较为复杂。
- 优点：**线程池可以重用预先创建的线程，避免了线程创建和销毁的开销，显著提高了程序的性能**。对于需要快速响应的并发请求，线程池可以迅速提供线程来处理任务，减少等待时间。并且，线程池能够有效控制运行的线程数量，防止因创建过多线程导致的系统资源耗尽（如内存溢出）。通过合理配置线程池大小，可以最大化CPU利用率和系统吞吐量。

**总结:**

- 采用继承Thread类方式

  - **优点**: **编写简单**，如果需要访问当前线程，无需使用Thread.currentThread ()方法，直接使用this，即可获得当前线程

  - **缺点**:因为线程类已经继承了Thread类，所以**不能再继承其他的父类**

- 采用实现Runnable接口方式： 

  - 优点：线程类只是实现了Runable接口，还**可以继承其他的类**。在这种方式下**，可以多个线程共享同一个目标对象**，所以**非常适合多个相同线程来处理同一份资源的情况**，从而可以将CPU代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。

  - 缺点：**编程稍微复杂，如果需要访问当前线程，必须使用Thread.currentThread()方法**。

- 采用实现Callable接口方式：

  - 缺点：**编程稍微复杂，如果需要访问当前线程，必须调用Thread.currentThread()方法**。

  - 优点：线程只是实现Runnable或实现Callable接口，还可以继承其他类。这种方式下，**多个线程可以共享一个target对象**，非常适合多线程处理同一份资源的情形。

- 采用线程池方式：

  - 缺点：**程池增加了程序的复杂度**，特别是当涉及线程池参数调整和故障排查时。**错误的配置可能导致死锁、资源耗尽等问题**，这些问题的诊断和修复可能较为复杂。

  - 优点：**线程池可以重用预先创建的线程，避免了线程创建和销毁的开销，显著提高了程序的性能**。对于需要快速响应的并发请求，线程池可以迅速提供线程来处理任务，减少等待时间。并且，线程池能够有效控制运行的线程数量，防止因创建过多线程导致的系统资源耗尽（如内存溢出）。通过合理配置线程池大小，可以最大化CPU利用率和系统吞吐量。

## 启动一个 Java 程序，你能说说里面有哪些线程吗？

首先是 main 线程，这是程序开始执行的入口。

然后是垃圾回收线程，它是一个后台线程，负责回收不再使用的对象。

还有编译器线程，在及时编译中（JIT），负责把一部分热点代码编译后放到 codeCache 中，以提升程序的执行效率。

## 如何停止一个线程的运行?   

主要有这些方法：

- **异常法停止**：线程调用interrupt()方法后，在线程的run方法中判断当前对象的interrupted()状态，如果是中断状态则抛出异常，达到中断线程的效果。
- **在沉睡中停止**：==先将线程sleep==，**然后调用interrupt标记中断状态**，**interrupt会将阻塞状态的线程中断。**会抛出中断异常，达到停止线程的效果
- **stop()暴力停止**：**线程调用stop()方法会被暴力停止**，方法已弃用，该方法会有不好的后果：强制让线程停止有可能使一些请理性的工作得不到完成。
- **使用return停止线程**：**调用interrupt标记为中断状态后，在run方法中判断当前线程状态，如果为中断状态则return，**能达到停止线程的效果。

## 线程包括哪些状态，状态之间是如何变化的？ 

线程的六种状态分别是：**新建、可运行、终结、阻塞、等待和有时限等待**

- 当一个线程对象被创建，但还未调用 start 方法时处于**新建**状态
- 调用了 start 方法，就会由**新建**进入**可运行**状态。
- 如果线程内代码已经执行完毕，由**可运行**进入**终结**状态。

当然这些是一个线程正常执行情况,还有其他有三种情况

- 如果线程获取锁失败后，由**可运行**进入 Monitor 的阻塞队列**阻塞**
- 如果线程获取锁成功后，**但由于条件不满足，调用了Object.wait()`或`LockSupport.park()**，此时从**可运行**状态释放锁**等待**状态，当其它持锁线程调用 notify() 或 notifyAll() 方法，会恢复为**可运行**状态
- 还有一种情况是调用`Thread.sleep(long millis)`、`Object.wait(long timeout)` 或`LockSupport.parkNanos()`方法也会从**可运行**状态进入**有时限等待**状态，不需要主动唤醒，超时时间到自然恢复为**可运行**状态

## notify 和 notifyAll 的区别?

同样是唤醒等待的线程，同样最多只有一个线程能获得锁，同样不能控制哪个线程获得锁。

区别在于：

- **notify**：唤醒一个线程，其他线程依然处于wait的等待唤醒状态，**如果被唤醒的线程结束时没调用notify，其他线程就永远没人去唤醒，只能等待超时，或者被中断**
- **notifyAll**：所有线程退出wait的状态，开始竞争锁，但只有一个线程能抢到，这个线程执行完后，其他线程又会有一个幸运儿脱颖而出得到锁

## notify 选择哪个线程?

notify在源码的注释中说到notify选择唤醒的线程是任意的，但是依赖于具体实现的jvm。

![image-20240725230457096](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/202410061636142.png)

JVM有很多实现，比较流行的就是hotspot，hotspot对notofy()的实现并不是我们以为的随机唤醒,，而是“先进先出”的顺序唤醒。

# 并发安全

## Java中有哪些常用的锁

**悲观锁**:通常指在访问数据前就锁定资源，假设最坏的情况，数据是有可能被其他线程修改。

- `sychronized` 
- `ReentrantLock`

**乐观锁**:通常不锁定资源，而是在更新数据时检查数据是否已被其他线程修改。乐观锁常使用版本号或时间戳来实现。

- CAS:是一种无锁机制,CAS机制基于三个操作数实现的
  - 内存地址V
  - 旧预期值A
  - 需要替换的值B


> 当需要更新一个变量的值的时候会用内存V的值跟旧预期值比较,如果一致就将内存地址V的值替换为B
>

- version:主要是通过一个版本号,当你写数据提交之后会把这个版本号+1,这样别的线程想修改的时候就会判断这个版本号和进来的时候是否一致,由于刚刚+1了不一致所以这个线程不能修改

## Java中想实现一个乐观锁，都有哪些方式？

1. **CAS（Compare and Swap）操作：** CAS 是乐观锁的基础。Java 提供了 java.util.concurrent.atomic 包，包含各种原子变量类（如 AtomicInteger、AtomicLong），这些类使用 CAS 操作实现了线程安全的原子操作，可以用来实现乐观锁。
2. **版本号控制**：增加一个版本号字段记录数据更新时候的版本，每次更新时递增版本号。在更新数据时，同时比较版本号，若当前版本号和更新前获取的版本号一致，则更新成功，否则失败。
3. **时间戳**：使用时间戳记录数据的更新时间，在更新数据时，在比较时间戳。如果当前时间戳大于数据的时间戳，则说明数据已经被其他线程更新，更新失败。

## CAS

CAS:是一种无锁机制,CAS机制基于三个操作数实现的

- 内存地址V
- 旧预期值A
- 需要替换的值B

当需要更新一个变量的值的时候会用内存V的值跟旧预期值比较,如果一致就将内存地址V的值替换为B

### 什么是ABA问题？怎么解决？

当一个值从A更新为B，再从B更新为A，普通CAS机制会误判通过检测。解决方案是使用版本号，通过比较值和版本号才判断是否可以替换

**具体**

现在我们来说下什么是ABA问题。

1. 有三个线程,线程1和2都是讲A改为B而线程3是将B改为A
2. 线程1先一步执行成功，把当前值成功从A更新为B；同时线程2因某种原因阻塞住，没有做更新操作，此时线程3在线程1更新之后，获取了当前值B。
3. 在之后，线程2仍然处于阻塞状态，线程3继续执行，成功把当前值从B更新成A。
4. 最后，线程2终于恢复了运行状态，由于阻塞之前已经获得到了”当前值A“，并且经过compare检测，内存地址V中的实际值也是A，所以成功把变量A的值更新为B。

**看起来这个例子没有什么问题**，但如果结合实际，就可以发现它的问题所在。

1. 假设小帅有100元打算去银行取款,这个时候取50但是由于机器出故障提交了俩次,这个时候就是线程AB俩个线程
2. 线程1首先执行成功，把余额100更新为50，同时线程2由于某种原因陷入了阻塞状态，这时候，小美给小帅v50
3. 线程2仍然是阻塞状态，线程3此时执行成功，把余额从50改成了100.
4. 这时候，线程2恢复运行，由于之前阻塞的时候获得了”当前值“100，并且经过compare检测，此时存款也的确是100元，所以成功把变量值从100更新成50.
5. 原本线程2应当提交失败，小帅的正确余额应该保持100元，结果由于ABA问题提交成功了。

**这就是所谓的ABA问题，那么怎么解决呢？**

添加版本号解决ABA问题

每次操作版本号+1,所以当你到最后一个线程的时候版本号为3,虽然你的金额跟内存地址值一致都是100但是版本号比较不通过所以这一次更新失败

### CAS 有什么缺点？

- **ABA问题**：ABA的问题指的是在CAS更新的过程中，当读取到的值是A，然后准备赋值的时候仍然是A，但是实际上有可能A的值被改成了B，然后又被改回了A，这个CAS更新的漏洞就叫做ABA。只是ABA的问题大部分场景下都不影响并发的最终效果。Java中有AtomicStampedReference来解决这个问题，他加入了预期标志和更新后标志两个字段，更新时不光检查值，还要检查当前的标志是否等于预期标志，全部相等的话才会更新。
- **循环时间长开销大**：CAS操作是基于循环重试的机制，如果CAS操作一直未能成功，线程会一直自旋重试，占用CPU资源。在高并发情况下，大量线程自旋会导致CPU资源浪费。
- **只能保证一个共享变量的原子操作**：只对一个共享变量操作可以保证原子性，但是多个则不行，多个可以通过AtomicReference来处理或者使用锁synchronized实现。

## AQS 

**AQS是什么**

AQS是一个用来==构建**锁**和**同步器**的框架==,像ReentrantLock和FutrueTask都是使用AQS来实现的

**AQS底层**

他是一个双向队列,其中有头head和为tail还有一个state,这个state就是用来判断获取几次锁,像0就是无锁状态1就说明获取了一次锁,并且aqs对state的修改是基于cas来保证原子性的

##  volatile  

volatile 是一个关键字，可以修饰类的成员变量、类的静态成员变量，主要有两个功能

- **保证了不同线程对这个变量进行操作时的可见性**，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的,volatile关键字会强制将修改的值立即写入主存。

- **禁止进行指令重排序，可以保证代码执行有序性**。底层实现原理是，添加了一个**内存屏障**，通过插入内存屏障禁止在内存屏障**前后**的指令执行重排序优化

## Threadlocal

### **介绍**

`ThreadLocal`是Java中用于解决线程安全问题的一种机制，它允许创建线程局部变量，即每个线程都有自己独立的变量副本，从而避免了线程间的资源共享和同步问题。

![img](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/202410061636893.png)

从内存结构图，我们可以看到：

- Thread类中，有个ThreadLocal.ThreadLocalMap 的成员变量。
- ThreadLocalMap内部维护了Entry数组，每个Entry代表一个完整的对象，key**是**ThreadLocal**本身**，value是ThreadLocal的泛型对象值。

### ThreadLocal的作用

- **线程隔离**：`ThreadLocal`为每个线程提供了独立的变量副本，这意味着线程之间不会相互影响，可以安全地在多线程环境中使用这些变量而不必担心数据竞争或同步问题。
- **降低耦合度**：在同一个线程内的多个函数或组件之间，使用`ThreadLocal`可以减少参数的传递，降低代码之间的耦合度，使代码更加清晰和模块化。
- **性能优势**：由于`ThreadLocal`避免了线程间的同步开销，所以在大量线程并发执行时，相比传统的锁机制，它可以提供更好的性能。

### ThreadLocal的原理

`ThreadLocal`的实现依赖于`Thread`类中的一个`ThreadLocalMap`字段，这是一个存储`ThreadLocal`变量本身和对应值的映射。每个线程都有自己的`ThreadLocalMap`实例，用于存储该线程所持有的所有`ThreadLocal`变量的值。

当你创建一个`ThreadLocal`变量时，它实际上就是一个`ThreadLocal`对象的实例。每个`ThreadLocal`对象都可以存储任意类型的值，这个值对每个线程来说是独立的。

- 当调用`ThreadLocal`的`get()`方法时，`ThreadLocal`会检查当前线程的`ThreadLocalMap`中是否有与之关联的值。
- 如果有，返回该值；
- 如果没有，会调用`initialValue()`方法（如果重写了的话）来初始化该值，然后将其放入`ThreadLocalMap`中并返回。
- 当调用`set()`方法时，`ThreadLocal`会将给定的值与当前线程关联起来，即在当前线程的`ThreadLocalMap`中存储一个键值对，键是`ThreadLocal`对象自身，值是传入的值。
- 当调用`remove()`方法时，会从当前线程的`ThreadLocalMap`中移除与该`ThreadLocal`对象关联的条目。

### 可能存在的问题

Key因为是弱引用所以在垃圾回收的时候不管内存够不够都被垃圾回收 回收掉，这样就会导致ThreadLocalMap中key为null， 而value还存在着强引用，只有thead线程退出以后,value的强引用链条才会断掉。

但如果当前线程再迟迟不结束的话，这些key为null的Entry的value就会一直存在一条强引用链。

永远无法回收，造成内存泄漏。

因此，在使用`ThreadLocal`时需要注意，**如果不显式调用**`remove()`**方法，或者线程结束时未正确清理**`ThreadLocal`**变量，可能会导致内存泄漏，因为**`ThreadLocalMap`**会持续持有**`ThreadLocal`**变量的引用，即使这些变量不再被其他地方引用。**

因此，实际应用中需要在使用完`ThreadLocal`变量后调用`remove()`方法释放资源。

### 为什么key不选择强引用呢？

#### key 使用强引用

当ThreadLocalMap的key为强引用回收ThreadLocal时，因为ThreadLocalMap还持有ThreadLocal的强引用，如果没有手动删除，ThreadLocal不会被回收，导致Entry内存泄漏。

所以，使用强引用要求使用者严格规范，不太好控制

#### key 使用弱引用

当ThreadLocalMap的key为弱引用回收ThreadLocal时，由于ThreadLocalMap持有ThreadLocal的弱引用，即使没有手动删除，ThreadLocal也会被回收。当key为null，在下一次ThreadLocalMap调用set(),get()，remove()方法的时候会被清除value值。

因此，ThreadLocal**内存泄漏**的根源是：由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key就会导致内存泄漏，而不是因为弱引用。


## **ReentrantLock**

- `ReentrantLock`是悲观锁的一种,他**支持可重入锁**和**公平锁**还有**非公平锁**并且可以**设置超时时间**默认为非公平锁,如果需要公平锁只需要构造器里true就ok，底层就是继承了`lock`接口然后重写了`lock`和`unlock`方法然后利用`CAS+AQS`来实现

### 公平锁和非公平锁

**公平锁(排队)**：多个线程按照申请锁的顺序去获得锁，线程会直接进入队列去排队，永远都是队列的第一位才能得到锁。

优点：所有的线程都能得到资源，**不会饿死在队列中**。

缺点：吞吐量会下降很多，队列里面除了第一个线程，**其他的线程都会阻塞**

**非公平锁**：多个线程去获取锁的时候，会有一次机会插入到队列第一位尝试获取锁，如果插队失败再去进入等待队列。

- 优点：整体的吞吐效率会高点。
- 缺点：既然有插队的情况就一定有人一直被插队的情况,所以被插队的容易**导致饿死**。



## synchronized

### Monitor原理

![img](https://cdn.nlark.com/yuque/0/2024/png/40835658/1725679269544-cf8332b7-ae74-47da-9c68-7cad9e676a72.png)

 每个 Java 对象都可以关联一个 Monitor 对象，如果使用 synchronized 给对象上锁（重量级）之后，该对象头的 Mark Word 中就被设置指向 Monitor 对象的指针  

- 刚开始 Monitor 中 Owner 为 null 
- 当 Thread-2 执行 synchronized(obj) 就会将 Monitor 的所有者 Owner 置为 Thread-2，Monitor中只能有一 个 Owner 
- 在 Thread-2 上锁的过程中，如果 Thread-3，Thread-4，Thread-5 也来执行 synchronized(obj)，就会进入 EntryList BLOCKED 
- Thread-2 执行完同步代码块的内容，然后唤醒 EntryList 中等待的线程来竞争锁，竞争的时是非公平的 
- 图中 WaitSet 中的 Thread-0，Thread-1 是之前获得过锁，但条件不满足进入 WAITING 状态的线程，后面讲 wait-notify 时会分析  



### synchronized解决什么问题

- Synchronized 采用互斥的方式让同一时刻至多只有一个线程能持有
- **实现原子性操作**和**解决共享变量的内存可见性**问题

### synchronized加锁逻辑

1. 使用synchronized之后，会在编译之后在同步的代码块前后加上monitor**ente**r和monitor**exit**字节码指令
2. 执行monitorenter指令时会尝试获取对象锁，如果对象没有被锁定或者已经获得了锁，锁的计数器+1。此时其他竞争锁的线程则会进入等待队列中（entrylist）。执行monitorexit指令时则会把计数器-1，当计数器值为0时，则锁释放，处于等待队列（entrylist）中的线程再继续竞争锁。
3. 如果线程调用wait方法，将释放锁，计数器-1，同时进入waitSet等待被唤醒，调用notify或者notifyAll之后又会进入entryList竞争锁

- 在1.6之前它的底层由monitor实现的
- 在monitor内部有三个属性，分别是owner、entrylist、 waitset

- - 其中owner是关联的获得锁的线程，并且只能关联一个线程; 
  - entrylist关联的是处于阻塞状态的线程;
  - waitset关联的是处于Waiting状态的线程

- **在JDK 1.6引入了两种新型锁机制**：偏向锁和轻量级锁，它们的引入是为了解决在没有多线程竞争或基本没有竞争的场景下因使用传统锁机制带来的性能开销问题。

### synchronized锁升级

Java中的synchronized有偏向锁、轻量级锁、重量级锁三种形式，分别对应了锁**只被一个线程持有**、**不同线程交替持有锁**、**多线程竞争锁**三种情况。

状态:偏向锁->轻量级锁->重量级锁

- 偏向锁(没有锁竞争)
  - 判断线程是id是否是自己的线程id,如果是就直接执行,如果不是则尝试cas替换==对象头中的id==
  - 如果这里cas尝试替换失败的话说明之前线程还在执行,可能会出现锁竞争,升级轻量级锁

- 轻量级锁(不同线程持有锁)
  - 轻量级锁会通过cas不断循环等待尝试获取锁,如果锁竞争情况严重，某个达到最大自旋次数的线程，会将轻量级锁升级为`重量级锁`。

- 重量级锁(多线程竞争锁)
  - 底层使用的Monitor实现，里面涉及到了用户态和内核态的切换、进程的上下文切换，成本较高，性能比较低。


**一旦锁发生了竞争，都会升级为重量级锁**

### 被synchronized修饰方法时指向的对象是谁

在Java中，当`synchronized`关键字用于修饰方法时，它所指向的对象取决于方法的类型（实例方法或静态方法）

#### 1. 实例方法

- **锁的对象**：当`synchronized`修饰一个实例方法时，它锁定的是调用该方法的对象（即`this`对象）。这意味着，如果两个线程同时调用同一个对象的这个同步方法，那么这两个线程将会以互斥的方式执行该方法；但如果两个线程分别调用不同对象的这个同步方法，那么这两个线程是可以同时执行该方法的，因为每个对象都有自己的锁。

#### 2. 静态方法

- **锁的对象**：当`synchronized`修饰一个静态方法时，它锁定的是这个类的`Class`对象。因为静态方法是属于类的，而不是类的某个具体实例，所以无论创建多少个类的实例，调用这个静态同步方法时，所有的线程都会争夺同一个锁，即该类的`Class`对象。

#### 总结

- **实例方法**：指向调用该方法的对象（`this`）。
- **静态方法**：指向该类的`Class`对象。

这种机制确保了多线程环境下，对共享资源的访问是线程安全的。通过`synchronized`关键字，我们可以避免多个线程同时访问同一个对象或类的关键部分时导致的数据不一致或状态破坏问题。

### JVM对Synchornized的优化？ *

synchronized 核心优化方案主要包含以下 4 个：

- **锁膨胀**：synchronized 从无锁升级到偏向锁，再到轻量级锁，最后到重量级锁的过程，它叫做锁膨胀也叫做锁升级。JDK 1.6 之前，synchronized 是重量级锁，也就是说 synchronized 在释放和获取锁时都会从用户态转换成内核态，而转换的效率是比较低的。但有了锁膨胀机制之后，synchronized 的状态就多了无锁、偏向锁以及轻量级锁了，这时候在进行并发操作时，大部分的场景都不需要用户态到内核态的转换了，这样就大幅的提升了 synchronized 的性能。
- **锁消除**：指的是在某些情况下，JVM 虚拟机如果检测不到某段代码被共享和竞争的可能性，就会将这段代码所属的同步锁消除掉，从而到底提高程序性能的目的。
- **锁粗化**：将多个连续的加锁、解锁操作连接在一起，扩展成一个范围更大的锁。
- **自适应自旋锁**：指通过自身循环，尝试获取锁的一种方式，优点在于它避免一些线程的挂起和恢复操作，因为挂起线程和恢复线程都需要从用户态转入内核态，这个过程是比较慢的，所以通过自旋的方式可以一定程度上避免线程挂起和恢复所造成的性能开销。

### synchronized 支持重入吗？如何实现的?

synchronized是基于原子性的内部锁机制，是可重入的，因此在一个线程调用synchronized方法的同时在其方法体内部调用该对象另一个synchronized方法，也就是说一个线程得到一个对象锁后再次请求该对象锁，是允许的，这就是synchronized的可重入性。

synchronized底层是利用计算机系统mutex Lock实现的。每一个可重入锁都会关联一个线程ID和一个锁状态status。

当一个线程请求方法时，会去检查锁状态。

1. 如果锁状态是0，代表该锁没有被占用，使用CAS操作获取锁，将线程ID替换成自己的线程ID。
2. 如果锁状态不是0，代表有线程在访问该方法。此时，如果线程ID是自己的线程ID，如果是可重入锁，会将status自增1，然后获取到该锁，进而执行相应的方法；如果是非重入锁，就会进入阻塞队列等待。

在释放锁时

1. 如果是可重入锁的，每一次退出方法，就会将status减1，直至status的值为0，最后释放该锁。
2. 如果非可重入锁的，线程退出方法，直接就会释放该锁。

### synchronized和volatile的区别 

`synchronized` 关键字和 `volatile` 关键字是两个互补的存在，而不是对立的存在！

- `volatile` 关键字是线程同步的轻量级实现，所以 `volatile`性能肯定比`synchronized`关键字要好 。但是 `volatile` 关键字**只能用于变量**而 `synchronized` 关键字**可以修饰方法以及代码块** 。
- `volatile` 关键字能保证数据的**可见性**，但不能保证数据的原子性。`synchronized` 关键字**两者都能保证。**
- `volatile`关键字主要用于**解决变量在多个线程之间的可见性**，而 `synchronized` 关键字**解决的是多个线程之间访问资源的同步性**
- `volatile`不会造成线程的阻塞；`synchronized`可能会造成线程的阻塞。

### synchronized和Lock有什么区别 ? 	

第一，语法层面

- synchronized 是关键字，源码在 jvm 中，用 c++ 语言实现，退出同步代码块锁会自动释放
- Lock 是接口，源码由 jdk 提供，用 java 语言实现，需要手动调用 unlock 方法释放锁

第三，性能层面

- 在没有竞争时，synchronized 做了很多优化，如偏向锁、轻量级锁，性能不赖
- 在竞争激烈时，Lock 的实现通常会提供更好的性能

统合来看，需要根据不同的场景来选择不同的锁的使用。

synchronized和reentrantlock区别？

synchronized 和 ReentrantLock 都是 Java 中提供的可重入锁：

- **用法不同**：synchronized 可用来修饰普通方法、静态方法和代码块，而 ReentrantLock 只能用在代码块上。
- **获取锁和释放锁方式不同**：synchronized 会自动加锁和释放锁，当进入 synchronized 修饰的代码块之后会自动加锁，当离开 synchronized 的代码段之后会自动释放锁。而 ReentrantLock 需要手动加锁和释放锁
- **锁类型不同**：synchronized 属于非公平锁，而 ReentrantLock 既可以是公平锁也可以是非公平锁。
- **响应中断不同**：ReentrantLock 可以响应中断，解决死锁的问题，而 synchronized 不能响应中断。
- **底层实现不同**：synchronized 是 JVM 层面通过监视器实现的，而 ReentrantLock 是基于 AQS 实现的。

## 怎么理解可重入锁？ *

可重入锁是指**同一个线程在获取了锁之后，可以再次重复获取该锁而不会造成死锁或其他问题**。当一个线程持有锁时，如果再次尝试获取该锁，就会成功获取而不会被阻塞。

ReentrantLock实现可重入锁的机制是**基于线程持有锁的计数器。**

- 当一个线程第一次获取锁时，计数器会加1，表示该线程持有了锁。在此之后，如果同一个线程再次获取锁，计数器会再次加1。每次线程成功获取锁时，都会将计数器加1。
- 当线程释放锁时，计数器会相应地减1。只有当计数器减到0时，锁才会完全释放，其他线程才有机会获取锁。

synchronized也实现了可重入锁的机制

1. 如果锁状态是0，代表该锁没有被占用，使用CAS操作获取锁，将线程ID替换成自己的线程ID。
2. 如果锁状态不是0，代表有线程在访问该方法。此时，如果线程ID是自己的线程ID，如果是可重入锁，会将status自增1，然后获取到该锁，进而执行相应的方法；如果是非重入锁，就会进入阻塞队列等待。

在释放锁时，

1. 如果是可重入锁的，每一次退出方法，就会将status减1，直至status的值为0，最后释放该锁。
2. 如果非可重入锁的，线程退出方法，直接就会释放该锁。

## 如何保证Java程序在多线程的情况下执行安全呢？

- 原子性:synchronized、LOCK，Atomic,ReentrantLock
  - 对于**简单的变量操作**：使用 Atomic 类来实现无锁线程安全。

- 可见性:synchronized、volatile
- 有序性:volatile
- 隔离性：使用 ThreadLocal 来为每个线程提供独立的变量副本。
- 借助线程安全包:如 ConcurrentHashMap 或 CopyOnWriteArrayList。

## 死锁产生的条件是什么？ *

死锁的四个必要条件:

- 互斥条件：一个资源每次只能被一个进程使用。
- 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
- 不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺。
- 循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系。

##  常见线程安全类  

- String
- Integer 
- StringBuffer 
- Random 
- Vector 
- Hashtable
- java.util.concurrent 包下的类

 这里说它们是线程安全的是指，多个线程调用它们同一个实例的某个方法时，是线程安全的。也可以理解  

```java
Hashtable table = new Hashtable();
new Thread(()->{
 table.put("key", "value1");
}).start();
new Thread(()->{
 table.put("key", "value2");
}).start();
```

- 它们的每个方法是原子的 ,但注意它们多个方法的组合不是原子的
- 比如这里

```java
Hashtable table = new Hashtable();
// 线程1，线程2
if( table.get("key") == null) {
 table.put("key", value);
}
```

这里put和get独自都是线程安全的,但是放一起组合就具有原子性了 



# 线程池

## 如何确定核心线程数

- IO密集型任务

一般来说：文件读写、DB读写、网络请求等

推荐：核心线程数大小设置为2N+1 （N为计算机的CPU核数）

- CPU密集型任务

一般来说：计算型代码、Bitmap转换、Gson转换等

推荐：核心线程数大小设置为N+1 （N为计算机的CPU核数）

## **核心线程数设置为0可不可以？**

可以，当核心线程数为0的时候，会创建一个非核心线程进行执行。

从下面的源码也可以看到，当核心线程数为 0 时，来了一个任务之后，会先将任务添加到任务队列，同时也会判断当前工作的线程数是否为 0，如果为 0，则会创建线程来执行线程池的任务。

![image-20240820113849549](https://pic-1329573580.cos.ap-nanjing.myqcloud.com/202410061636039.png)

## 线程池种类有哪些？

- ScheduledThreadPool：可以设置定期的执行任务，它支持定时或周期性执行任务，比如每隔 10 秒钟执行一次任务，我通过这个实现类设置定期执行任务的策略。
- FixedThreadPool：它的核心线程数和最大线程数是一样的，所以可以把它看作是固定线程数的线程池，它的特点是线程池中的线程数除了初始阶段需要从 0 开始增加外，之后的线程数量就是固定的，就算任务数超过线程数，线程池也不会再创建更多的线程来处理任务，而是会把超出线程处理能力的任务放到任务队列中进行等待。而且就算任务队列满了，到了本该继续增加线程数的时候，由于它的最大线程数和核心线程数是一样的，所以也无法再增加新的线程了。
- CachedThreadPool：可以称作可缓存线程池，它的特点在于线程数是几乎可以无限增加的（实际最大可以达到 Integer.MAX_VALUE，为 2^31-1，这个数非常大，所以基本不可能达到），而当线程闲置时还可以对线程进行回收。也就是说该线程池的线程数量不是固定不变的，当然它也有一个用于存储提交任务的队列，但这个队列是 SynchronousQueue，队列的容量为0，实际不存储任何任务，它只负责对任务进行中转和传递，所以效率比较高。
- SingleThreadExecutor：它会使用唯一的线程去执行任务，原理和 FixedThreadPool 是一样的，只不过这里线程只有一个，如果线程在执行任务的过程中发生异常，线程池也会重新创建一个线程来执行后续的任务。这种线程池由于只有一个线程，所以非常适合用于所有任务都需要按被提交的顺序依次执行的场景，而前几种线程池不一定能够保障任务的执行顺序等于被提交的顺序，因为它们是多线程并行执行的。
- SingleThreadScheduledExecutor：它实际和 ScheduledThreadPool 线程池非常相似，它只是 ScheduledThreadPool 的一个特例，内部只有一个线程。

## 说一下线程池的核心参数（线程池的执行原理知道嘛）

在线程池中一共有7个核心参数：

1. corePoolSize 核心线程数目 - **池中会保留的最多线程数**

2. maximumPoolSize 最大线程数目 - 核心线程+救急线程的最大数目

3. keepAliveTime 生存时间 - 救急线程的生存时间，生存时间内没有新任务，此线程资源会释放

4. unit 时间单位 - 救急线程的生存时间单位，如秒、毫秒等

5. **workQueue** - 当没有空闲核心线程时，新来任务会加入到此队列排队，队列满会创建救急线程执行任

   1. **ArrayBlockingQueue**：基于数组结构的有界阻塞队列，FIFO。
   2. **LinkedBlockingQueue**：基于链表结构的有界阻塞队列，FIFO。
   3. 这个也叫阻塞队列,如果问道常用的阻塞队列就答这个

6. threadFactory 线程工厂 - 可以定制线程对象的创建，例如设置线程名字、是否是守护线程等

7. handler 拒绝策略 - 当所有线程都在繁忙，workQueue 也放满时，会触发拒绝策略

   1. 在拒绝策略中又有4中拒绝策略

      1. 当线程数过多以后**第一种是抛异常**(默认)

      2. 第二种是由调用者执行任务

      3. 第三是丢弃当前的任务

      4. 第四是丢弃最早排队任务

      5. 补充：其他框架拒绝策略

         * Dubbo：在抛出 RejectedExecutionException 异常前记录日志，并 dump 线程栈信息，方便定位问题

         * Netty：创建一个新线程来执行任务

         * ActiveMQ：带超时等待（60s）尝试放入队列

         * PinPoint：它使用了一个拒绝策略链，会逐一尝试策略链中每种拒绝策略


## **线程池的工作流程**

1. 任务在提交的时候，首先判断核心线程数是否已满，如果没有满则直接添加到工作线程执行
2. 如果核心线程数满了，则判断阻塞队列是否已满，如果没有满，当前任务存入阻塞队列
3. 如果阻塞队列也满了，则判断线程数是否小于最大线程数，如果满足条件，则使用临时线程执行任务
4. 如果核心或临时线程执行完成任务后会检查阻塞队列中是否有需要执行的线程，如果有，则使用非核心线程执行任务
5. 如果所有线程都在忙着（核心线程+临时线程），则走拒绝策略

## 为什么不建议使用Executors创建线程池呢？

主要原因是如果使用Executors创建线程池的话，它允许的请求队列默认长度是Integer.MAX_VALUE，这样的话，有可能导致堆积大量的请求，从而导致OOM（内存溢出）。

所以，我们一般推荐使用ThreadPoolExecutor来创建线程池，这样可以明确规定线程池的参数(线程池的7个参数)，避免资源的耗尽。

## 线程池一般是怎么用的？

可以使用Excutors的各种策略,但是《阿里巴巴 Java 开发手册》中提到，禁止使用这些方法来创建线程池，而应该手动 new ThreadPoolExecutor 来创建线程池。最典型的就是Excutors策略中的newFixedThreadPool 和 newCachedThreadPool，可能因为资源耗尽导致 OOM 问题。

## 提交给线程池中的任务可以被撤回吗？

可以，当向线程池提交任务时，会得到一个`Future`对象。这个`Future`对象提供了几种方法来管理任务的执行，包括取消任务。

取消任务的主要方法是`Future`接口中的`cancel(boolean mayInterruptIfRunning)`方法。这个方法尝试取消执行的任务。参数`mayInterruptIfRunning`指示是否允许中断正在执行的任务。如果设置为`true`，则表示如果任务已经开始执行，那么允许中断任务；如果设置为`false`，任务已经开始执行则不会被中断。

```java
public interface Future<V> {
    // 是否取消线程的执行
    boolean cancel(boolean mayInterruptIfRunning);
    // 线程是否被取消
    boolean isCancelled();
    //线程是否执行完毕
    boolean isDone();
      // 立即获得线程返回的结果
    V get() throws InterruptedException, ExecutionException;
      // 延时时间后再获得线程返回的结果
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

取消线程池中任务的方式，代码如下，通过 future 对象的 cancel(boolean) 函数来定向取消特定的任务。

```java
public static void main(String[] args) {
        ExecutorService service = Executors.newSingleThreadExecutor();
        Future future = service.submit(new TheradDemo());

        try {
          // 可能抛出异常
            future.get();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }finally {
          //终止任务的执行
            future.cancel(true);
        }
 }
```

# 其他

## 你在项目中哪里用了多线程？

参考场景一：

发送邮件:这个呢主要就是用来异步解耦用的,像发送邮件这种逻辑就没必要妨碍主线程流程,直接异步就ok了

参考场景二：

调用rpc接口:主要就是你可能一个业务调用很多rpc接口,这个时候就可以开线程每个线程去执行一个rpc接口,可以优化时间

**参考场景三：**

定时任务批量写数据:当你数据量比较大的情况下比如100w可以开十个线程,也就是每个线程分配10w嘛,性能肯定会更好



## 多线程打印奇偶数，怎么控制打印的顺序

可以利用wait()和notify()来控制线程的执行顺序。

以下是一个基于这种方法的简单示例：

```java
public class PrintOddEven {
    private static final Object lock = new Object();
    private static int count = 1;
    private static final int MAX_COUNT = 10;

    public static void main(String[] args) {
        Runnable printOdd = () -> {
            synchronized (lock) {
                while (count <= MAX_COUNT) {
                    if (count % 2 != 0) {
                        System.out.println(Thread.currentThread().getName() + ": " + count++);
                        lock.notify();
                    } else {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        };

        Runnable printEven = () -> {
            synchronized (lock) {
                while (count <= MAX_COUNT) {
                    if (count % 2 == 0) {
                        System.out.println(Thread.currentThread().getName() + ": " + count++);
                        lock.notify();
                    } else {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        };

        Thread oddThread = new Thread(printOdd, "OddThread");
        Thread evenThread = new Thread(printEven, "EvenThread");

        oddThread.start();
        evenThread.start();
    }
}
```

在上面的示例中，通过一个共享的锁对象lock来控制两个线程的交替执行。一个线程负责打印奇数，另一个线程负责打印偶数，通过wait()和notify()方法来在两个线程之间实现顺序控制。当当前应该打印奇数时，偶数线程会进入等待状态，反之亦然。
