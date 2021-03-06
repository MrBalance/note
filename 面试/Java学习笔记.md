# Java学习笔记

## JVM

### 基本概念

jdk：Java development kit

jre：java runtime environment

 jvm：java virtual machine

jdk主要是java编译（将.java文件编译成.class文件），以及java环境运行的检测工具

如：jvisualvm

一种图形化工具，可在Java虚拟机中运行时提供有关基于Java技术的应用程序（Java应用程序）的详细信息。 Java VisualVM提供内存和CPU分析，堆转储分析，内存泄漏检测，MBean访问和垃圾收集。

jre主要是Java运行时所需要的环境，其中就包括jvm

jvm使得java这种语言具有可移植性，也就是常常说的once write run every where,即在任意环境编译出的jar包可以在任意装有对应jvm的系统中运行

同时java语言还具有内存自动分配管理的功能，这使得开发人员在实际开发中不用过多的关注内存分配问题，而将更多的精力放到实际开发中去。这也是java语言能够收大多数开发着欢迎而流行起来的原因

### JVM的构成

| 数据   | 指令       |
| ------ | ---------- |
| 方法区 | 程序计数器 |
| heap   | jvm栈      |
|        | 本地方法栈 |

1. 程序计数器
   程序计数器记录了当前线程正在执行字节码指令的地址和行号
   场景：

   - 当正在执行的线程因为一些因素而被抢占时，线程中的程序计数器会记录下线程执行字节码指令所在的位置，当线程再次被唤醒时能够从记录的运行位置继续运行下去
   - 当正在执行的线程因为其他控制指令（if，switch，break等）控制而改变执行位置的时候，记录下运行到的位置，当控制指令执行完后，再根据记录的指令继续执行下去

   程序计数器属于线程独享资源，每个线程都有自己的程序计数器

2. jvm栈
   jvm栈的总空间是固定的，其中一个每个单位空间成为栈帧，而栈帧又包括：

   1. 局部变量表：存放局部变量的信息，只有程序执行时才有数据，其空间大小受变量个数影响，但是在编译时空间大小就确定下来了。==大小为32位，float、int、short、byte类型就直接存储，long、double类型的变量占两个空间，对象变量存储对象的引用==
   
   2. 操作数栈：字节码指令的工作空间，根据last-in-frist-out原则，按照指令顺序，操作每个字节码指令的进栈和出栈。
   
   3. 动态链接：字节码中方法调用的符号引用，在程序运行时转化为直接引用
   
      ```java
      @Autoware
      private Service service; //Service为接口
      serviceImpl_1;//Service的第一个实现类
      serviceImpl_2;//Service的第二个实现类
      
      service.do();//在实际运行中Service作为接口无法直接使用其中的do方法，实际调用的是其实现类的引用方法，这个过程叫做动态链接
      ```
   
   4. 出口：方法正常（return）和异常（Exception）运行结束后回到的位置
   
   5. ...
   
   每个栈帧的空间大小是不确定的，所以整个栈的深度是不确定的
   
3. 本地方法栈
   本地方法栈和jvm栈类似，不过其中主要存放的是本地方法信息（native）

5. 堆
   运行时数据区，是线程共享的内存，因为对象和数组都存储于此，因此也是垃圾回收最重要的区域，在GC回收中采用分代回收算法，而堆在GC回收中主要是属于新生代和老年代
   
5. 方法区
   方法区主要存放类信息、常量、静态变量、just in time 等信息，也是在GC回收中属于永久代（jdk1.8以前），hotspot把垃圾分代扩充至方法区，用java堆的永久代去实现方法区，这样就不用单独的开发方法区的内存管理器，而永久代主要是针对常量池和类型卸载，因此这部分收益很小。

   运行时常量池也是方法区的一部分，其中除了类的本版、字段、方法、接口等信息以外还有常量池。

   而常量池主要是存放运行时的==字面量==和==符号引用==

### JVM运行时内存

从GC的角度来看JVM的运行时内存分为新生代（1/3）和老年代（2/3）
新生代用来存放新生对象占据1/3空间，由于其要频繁创建对象，所以会频繁触发minorGC，又将其分为eden、SurvivorFrom、SurvivorTo三个区域

eden：占8/10
from survivor（S1）：占1/10
to survivor（S2）：占1/10



### jdk性能调优：

jdk自带工具:

* jps 电脑上存在的java程序
* jinfo -flags +【javaid】打印指定程序jvm参数
* jstat -gc +【javaid】堆里每个内存区域分配情况、使用情况、垃圾回收次数
* jmap 查看内存信息
* jstack 线程的快照
* jmap -dump    获取堆在某个时刻的快照

gc日志

j visualVM

==ps：==

元空间动态扩容

元空间：21M水平线

### 类加载子系统

权限类名：包名+类名

类卸载：

	1. 程序正常执行结束/程序出现异常
 	2. 操作系统出现异常
 	3. system.exit

类加载机制：

加载 -> 链接（验证->准备->解析）-> 初始化 -> 使用 -> 卸载

全盘委托机制

双亲委派机制

	* 沙箱安全机制：防止核心库被篡改
	* 避免重复加载

如何判断对象可以被回收

	* 应用技术其法：相互引用
	* 可达性分析法：GC roots根节点 引用链

对象引用有四种？强、软（内存足够不回收）、弱（不论内存足够与否）、虚（任何时候）

判断常量是废弃常量：没有任何字面量去引用的常量

判断类是无用类

重写finalize方法：只能自救一次

### 垃圾回收算法

标记清除算法：内存碎片、效率不高

复制算法：标记 -> 复制 -> 转移

标记整理算法：开销/代价比较大

分代收集算法

分配担保机制：超过新生代的xxx%就会被直接转移到老年代

### 垃圾收集器

垃圾收集器：

Serial收集器：串行 stw 新生代复制、老年代标记-整理，简单高效
ParNew收集器：Serial收集器多线程版本（并发），同样停顿时间长，吞吐量不高
Parallel (Scavenge)收集器：完全实现并行
Serial Old收集器：Serial 老年代版本
Parallel Old收集器：Parallel 老年代版本
==CMS收集器（concurrent Mark Sweep）== ：

1. 初始标记阶段：暂停线程，标记

 	2. 并发标记：一边标记，一边清除
 	3. 重新标记：暂停线程，重新标记
 	4. 并发清理：并发清理之前的标记
 	5. 并发重置：重新开始

优点：用户体验好
缺点：cpu资源敏感、浮动垃圾、回收算法标记-清除算法产生空间碎片

==G1收集器==：区域（Region）区分新生代（eden、suriver）、老年代（old）、humongous（专门用来分配担保）可预测停顿MixGC，CG收集次数多

怎么选择收集器？

 1. 优先调整堆大小，让服务器自己选择

 2. 内存小于100M，使用串行收集器

 3. 如果是单核，并且没有停顿时间的要求，串行或jvm自行选择

 4. 允许停顿时间超过1s，选择并行或者jvm自行选择

 5. 如果相应时间最重要，并且不能超过1s，使用并发收集器

    官方推荐G1，性能高

### JVM调优两个指标：

* 停顿时间：-XX:MaxGCPauseMillis
* 吞吐量：垃圾收集的时间和总时间的占比1/(1+n)，吞吐量1-1/(1+n)，越高越好，-XX:GCTimeRatio=n

### GC调优步骤：

1. 打印日志
   `-XX:+PrintGCDetail -XX:+PrintGCTimeStamps -XX:+PrintGCDataStamps -Xloggc:./gc.log`
   在线工具GCeasy

   | 吞吐量 | 最大停顿 | 平均停顿 | YGC  | FGC  |
   | ------ | -------- | -------- | ---- | ---- |
   |        |          |          |      |      |

   第一次调优，设置MetaSpace大小：增大元空间大小 `-XX:MetaspaceSize=64M -XX:MaxMetaspaceSize=64M`

   第二次调优，增大年轻代动态扩容增量（20%），可以减少YGC：`-XX:YoungGenerationSizeIncrement=30`

2. 配置CMS收集器：
   `-XX:+UseConcMarkSweeptGC`
   分析gc-cms.log

3. 配置G1收集器：
   `-XX:UseG1GC`
   分析gc-g1.log
   G1内部，混合GC释放内存机制，避免G1出现Region没有可用情况，否则出发FullGC
   调优：

   1. 设置元空间大小
   2. 添加吞吐量、停顿时间参数

调优设置：

堆栈设置：

```
-Xss:每个线程栈大小
-Xms:初始堆大小，默认物理内存1/64
-Xmx:最大堆大小，默认物理内存1/4
-Xmn:新生代大小
....

```

==ps:==

死锁条件

年龄 15次

堆：java存储单位

栈：java运行单位

### JAVA IO和NIO

| IO     | NIO      |
| ------ | -------- |
| 面向流 | 面向缓冲 |
| 阻塞IO | 非阻塞IO |
| 无     | 选择器   |

NIO和IO的区别

1. NIO少了一次从内核空间到用户空间的拷贝
   ByteBuffer.allocateDirect()分配的内存使用的是本机内存而不是Java堆上的内存，通过网络和磁盘交互都在操作系统内核中发生
   allocateDirect()区别在于这块内存不由java堆管理，但仍在同一用户进程内
2. NIO以块来处理数据，IO以流来处理数据
3. 非阻塞，NIO 1个线程可以管理多个输入输出通道

**阻塞IO模型**：读写数据过程中会阻塞，内核发现数据没有就绪就会阻塞线程，数据就绪后内核拷贝到用户线程，并将结果返回给用户线程，线程才会解除阻塞。

**非阻塞IO模型**：发起IO操作后不需要等待，马上得到一个结果，如果数据未就绪就会得到一个error信号，然后再次发出IO请求，一旦内核数据准备就绪并且再次收到用户线程请求，就会马上将数据拷贝到用户线程，然后返回。期间不会交出CPU，并且会一直占用。

**多路复用IO模型**：一个线程轮询多个socket状态，只有socket真正有读写事件才会调用IO读写操作。询问每个通道是否有到达事件，如果没有则会一直阻塞在那里，从而使得用户线程阻塞。

**信号驱动IO模型**：用户线程发起IO请求时会给socket注册一个信号函数，用户线程继续运行，当内核数据准备就绪时向用户线程发送一个信号，用户线程通过调用信号函数来进行实际IO请求操作。

**异步IO模型**：异步IO模型是最理想的IO模型，当用户线程发起read请求后就立刻去执行其他任务，另一方面，内核收到asynchronous read（异步读）信号后，就立刻返回，说明read请求已经发送成功，期间用户线程不会受到任何阻塞，当内核等待数据就绪，就会将数据拷贝到用户线程，同时通知用户线程read操作已经完成，可以直接操作数据。

NIO基于Channel和Buffer，数据总是从通道读到缓冲区，或者从缓冲区写到通道，Selector（选择区）用户监听多个通道事件（链接打开，数据到达）。因此一个线程可以监听多个数据通道。

==ps：==

BIO，同步阻塞IO，NIO，同步非阻塞，AIO，异步非阻塞

servlet == server+applet

网络请求 -> linux :servlet

## Java集合

## 多线程

### 四种线程池：

newCacheThreadPool：根据需要重用以前可用的线程，终止掉缓存中60s未被使用的线程。

newFixedThreadPool：固定线程数量，共享无界队列方式运行线程，线程不够时任务进入等待队列，线程终止时新建一个线程，在被显示关闭线程前线程持续存在。

newSingleThreadPool：只有一个线程，在线程死后（或者异常时）重启一个线程替代原有线程继续执行下去。

### 线程状态

新建 -> 就绪 -> 运行 -> 阻塞 -> 死亡

阻塞情况：等待阻塞、同步阻塞、其他阻塞

线程死亡：正常结束、异常结束、调用stop

### 终止线程的四种方式

1. 正常运行结束
2. 退出标志（volatile 退出同步）
3. interrupt方法中断
4. stop（线程不安全，产生不可预料结果，持有锁全部释放，数据不一致性）

### sleep和wait区别

sleep属于Object类中，让出cpu，不会释放锁，监控状态依然保持，指定时间恢复运行状态

wait属于sychronous，释放锁，线程进入等待队列，notify方法恢复

### start和run区别

1. start启动线程，真正实现多线程
2. start启动线程进入就绪状态，没有真正运行
3. run线程体，线程运行时执行run中的方法，方法执行结束，线程终止cpu调度其他线程

### Java后台线程

1. 守护线程（服务线程）为用户线程提供公共服务
2. 优先级低
3. setDeamon（true）设置用户线程为守护线程
4. Deamon产生的新线程也是守护线程
5. JVM级别的即使停止web应用，线程依旧活跃
6. 垃圾回收线程是最经典的守护线程
7. 生命周期与系统共生死，JVM退出，守护线程才会退出

### 新建线程的方法

1. 继承Thread类
2. 实现Runnable接口
3. 实现Callable接口（用到线程池，execute无返回值，submit有返回值Future）

## Java锁

### 乐观锁

不上锁，在数据更新前确认一下数据有没有被更新，先读取版本号，再加锁，cas（原子操作）比较传入值是否一样

### 悲观锁

Synchronize
AQS框架下先尝试乐观锁，获取锁，取不到则用悲观锁

### 自旋锁

持有锁在很短时间内释放资源，其他线程不需要进入阻塞状态，等待线程结束后立刻占用锁，避免在用户线程和系统内核之间切换

让cpu做无用功，自选时间

jdk1.6适应性自旋锁：前一次上锁自选时间和锁拥有者的状态

### Synchronize同步锁

独占式悲观锁，可重入锁

作用范围:

1. 作用方法时锁住对象实例（this）
2. 静态方法，Class实例，全局锁，锁住所有调用该方法的线程
3. 代码块，对象实例

非公平锁，等待线程会优先尝试自旋

重量级操作

JDK优化：适应自旋、锁消除、锁粗化、轻量级锁、偏向锁

偏向锁、轻量级锁：对象头标记，不需要操作系统加锁

### ReentrantLock

可重入锁，具备：可相应中断锁、可轮询锁请求、定时锁等避免线程死锁的方法

reentrant锁提供是否公平锁的初始化方式

#### Condition和Object区别

await == wait

signal == notify

signalAll == notifyAll

ReentrantLock可以指定条件唤醒线程

### **Semaphore** 信号量

基于计数的信号量，可设置阈值

acquire()

release()

### AtomicInteger

原子操作Integer类

### 可重入锁（递归锁）

同一线程，外层获得锁后，其中的递归函数也能获得锁代码

### **ReadWriteLock** 读写锁

没有写锁，读锁无阻碍

多个读锁不互斥

读锁和写锁互斥

多个写锁互斥

### 共享锁和独享锁

独占锁：每次只有一个线程能够持有锁

共享锁：多个线程同时持有锁，访问共享资源（读锁）

### 重量级锁

加锁需要从用户状态切换到核心状态

### 轻量级锁

适用线程交替执行同步块，减少重量锁的性能开销

无锁 -> 偏向锁 -> 轻量锁 -> 重量锁

锁升级，只能从低到高

### 偏向锁

某个线程获得锁后消除线程重入锁（cas）的开销，提高只有一个线程执行同步块的性能

### 分段锁

ConcurrentHashMap

### 锁优化

减少锁持有时间

减小锁粒度

所分离

所粗化

锁消除

### 线程的基本方法

wait、notify、notifyAll、sleep、join、yield

sleep、isAlive、join、activeCount、enumerate、currentThread、isDeamon、setDeamon、setName、setPriority、getPriority

### 线程上下文切换

保存任务状态，再加载的过程称为上下文切换

#### 进程

程序运行的实例，是操作系统资源分配的基本单位。

#### 线程

线程是任务调度的和执行的基本单位。

#### 上下文

某一时间点cpu寄存器和程序计数器的内容

#### 寄存器

cpu内数量很少，但是速度很快的内存

#### 程序计数器

一个专用的寄存器，专门存放指令序列中cpu正在执行的位置

### 同步锁和死锁

#### 死锁

两个或以上线程在执行过程中，由于资源竞争或者通信而产生的阻塞现象，若无外力作用，它们都将无法推进下去

#### 死锁的必要条件

互斥

请求和保持请求

不剥夺

循环等待

#### 破坏死锁

破坏请求和保持请求：一次申请所有资源或者在申请资源前先释放资源

破坏不剥夺：允许抢占资源，如果被拒绝则释放所有资源，操作系统允许抢占，优先级高就可以抢占

破坏循环等待：申请资源按照序号提出

#### 拒绝策略

### votatile关键字

votatile变量不会被缓存到寄存器或者其他处理器不可见的地方

votatile变量总会返回最新写入值

#### 变量可见性

保证变量对所有线程可见，当一个线程修改变量值，新值对其他线程可以立刻获取

#### 禁止指令重排

volatile禁止指令重排

#### 比synchronize更轻量级的同步锁

适合场景：一个变量被多个线程共享，线程直接给变量赋值

不同volatile变量不能相互依赖

### ThreadLocal

线程本地变量，一个线程的局部变量

#### ThreadLocalMap

#### ConCurrentHashMap

减小粒度

分段锁16段（segment）

### Java中线程调度

抢占式调度（JVM的调度实现）

协同式调度

#### 线程让出cpu的情况

1. 线程主动让出yield
2. 线程阻塞，IO
3. 线程运行结束

#### 进程调度算法

1. 先来先服务（FCFS）
2. 短进程优先（SJF）
3. 高优先权优先调度（抢占式和非抢占式）、高响应比优先
4. 时间片轮转
5. 多级反馈队列调度

### CAS

比较交换：三个参数：V（要更新的变量内存值）、E（表示预期值旧的）、N（新值）

当且仅当V=E的时候V才会设为N

锁自旋

ABA问题，版本号

AQS ABS

## Java异常

Error：系统内部错误或则资源耗尽错误，不会向外抛出，告知用户并且安全结束程序

Exception：分为CheckedException和RuntimeException

RuntimeException：NullPointerException、ClassCastExcetion，运行时异常，编译阶段不捕获，程序运行时才捕获并向外抛出

CheckedException：IOException、SQLException，外部错误，编译阶段强制捕获异常

### 异常处理方式

#### throw和throws区别

1. throws用在函数上，后面可以跟多个异常类
   throw用在函数内，后面跟异常对象

2. throws声明异常，让调用者知道可能出现的问题

   throw实际抛出异常，运行到这里功能已经结束了

3. throws表示抛出异常的可能性，并不会一定会有异常

   throw表示已经抛出了异常，该异常已经被实际的抛出

## Java反射

### 反射机制概念

运行状态中知道类的所有属性和方法

### 获取class的3中方法

1. 对象.getClass()
2. 类.class
3. Class.forName("");

### 创建对象的两种方法

1. clazz.newInstance()
2. Class.forName("xxxx")
3. clazz.getDeclareConstructot()

## Java注解

接口，通过反射来获取指定元素

### 四种标准元注解

1. @Target 说明注解所修饰的对象范围（packages、types）
2. @Retention 定义被保留的时长 souce（源文件保留）、class（class保留）、runtime（运行时保留）
3. @Documented 描述它的类型
4. @ 描述被某个标记类继承

## Java内部类

静态内部类、成员内部类、局部内部类、匿名内部类

## Java范型

T和通配符？

## Java序列化

保存java对象的状态（持久化）

## Java复制

* 直接赋值
* 浅拷贝，复制引用不复制引用对象
* 深拷贝，复制对象和引用 （.clone）

## Java可变类和不可变类

可变类：获得引用实例时，可以改变实例内容

不可变类：获得引用实例时，不能改变实例内容

### 创建不可变类

所有成员private

不提供对成员的改变方法

确保方法不会被重载final，final class强不可变类，final所有方法弱不可变类

如果类中对象不是原始变量，不可变类，需要