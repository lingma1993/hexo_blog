---
title: Java、Python与PHP的虚拟机异同
date: 2017-06-23 23:59:24
tags: [virtual machine, java, python, php]
categories: 开发笔记 
copyright: true
---

## Java-JVM

### 定义

- JDK(Java Development Kit) 是 Java 语言的软件开发工具包（SDK）。JDK 物理存在，是 programming tools、JRE 和 JVM 的一个集合
- JRE（Java Runtime Environment）Java 运行时环境，JRE 物理存在，主要由Java API 和 JVM 组成，提供了用于执行 java 应用程序最低要求的环境。
- JVM(Java Virtual Machine) 是一种软件实现，执行像物理机程序的机器（即电脑）。JVM 通过执行 Java bytecode 可以使 java 代码在不改变的情况下运行在各种硬件之上。JVM是基于栈的。

### JVM 执行

- 加载代码
- 验证代码
- 执行代码
- 提供运行环境

### JVM 生命周期

- 启动：任何一个拥有main函数的class都可以作为JVM实例运行的起点
- 运行：main函数为起点，程序中的其他线程均有它启动，包括daemon守护线程和non-daemon普通线程。daemon是JVM自己使用的线程比如GC线程，main方法的初始线程是non-daemon。
- 消亡：所有线程终止时，JVM实例结束生命。

### JVM结构及内存模型

![](http://upload-images.jianshu.io/upload_images/634730-089a64921220b40d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

名词解释：

- Class Loader：类加载器负责加载程序中的类型（类和接口），并赋予唯一的名字。为什么使用双亲委托模型——ClassLoader 隔离问题。
- Execution Engine：执行引擎。执行引擎以指令为单位读取 Java 字节码。它就像一个 CPU 一样，一条一条地执行机器指令。
- Runtime Data Areas:：运行时数据区。
- PS：想起面试的时候被问到过这样的问题：你在使用java过程中是否遇到过OOM的情况？当时一阵懵比。现在总结下：
  - PC寄存器（PC Register）是唯一一个在 Java 虚拟机规范中没有规定任何OutOfMemoryError情况的区域。
  - JVM 栈（Java Virtual Machine Stack）：如果JVM Stack可以动态扩展，但是在尝试扩展时无法申请到足够的内存去完成扩展，或者在建立新的线程时没有足够的内存去创建对应的虚拟机栈时抛出。
  - 本地方法栈(Native method stack)：如果本地方法栈可以动态扩展，并且扩展的动作已经尝试过，但是目前无法申请到足够的内存去完成扩展，或者在建立新的线程时没有足够的内存去创建对应的本地方法栈，那Java虚拟机将会抛出一个OutOfMemoryError异常。
  - 方法区(Method area)：如果方法区的内存空间不能满足内存分配请求，那Java虚拟机将抛出一个OutOfMemoryError异常。
  - 运行时常量池(Runtime constant pool)：当创建类和接口时，如果构造运行时常量池所需的内存空间超过了方法区所能提供的最大内存空间后就会抛出OutOfMemoryError
  - 堆(Heap)：如果实际所需的堆超过了自动内存管理系统能提供的最大容量时抛出。
  - 总结一下就是无法申请到足够的内存以及超出最大容量两方面原因

### 垃圾回收

![](http://upload-images.jianshu.io/upload_images/2184951-327156d44f4f2446.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



- #### 新生代

  新生代由 Eden 与 Survivor Space（S0，S1）构成，大小通过-Xmn参数指定，Eden 与 Survivor Space 的内存大小比例默认为8:1，可以通过-XX:SurvivorRatio 参数指定，比如新生代为10M 时，Eden分配8M，S0和S1各分配1M。

  Eden：希腊语，意思为伊甸园，在圣经中，伊甸园含有乐园的意思，根据《旧约·创世纪》记载，上帝耶和华照自己的形像造了第一个男人亚当，再用亚当的一个肋骨创造了一个女人夏娃，并安置他们住在了伊甸园。

  大多数情况下，对象在Eden中分配，当Eden没有足够空间时，会触发一次Minor GC，虚拟机提供了-XX:+PrintGCDetails参数，告诉虚拟机在发生垃圾回收时打印内存回收日志。

  Survivor：意思为幸存者，是新生代和老年代的缓冲区域。
  当新生代发生GC（Minor GC）时，会将存活的对象移动到S0内存区域，并清空Eden区域，当再次发生Minor GC时，将Eden和S0中存活的对象移动到S1内存区域。

  存活对象会反复在S0和S1之间移动，当对象从Eden移动到Survivor或者在Survivor之间移动时，对象的GC年龄自动累加，当GC年龄超过默认阈值15时，会将该对象移动到老年代，可以通过参数-XX:MaxTenuringThreshold 对GC年龄的阈值进行设置。


- #### 老年代

  老年代的空间大小即-Xmx 与-Xmn 两个参数之差，用于存放经过几次Minor GC之后依旧存活的对象。当老年代的空间不足时，会触发Major GC/Full GC，速度一般比Minor GC慢10倍以上。


- #### 永久代

  在JDK8之前的HotSpot实现中，类的元数据如方法数据、方法信息（字节码，栈和变量大小）、运行时常量池、已确定的符号引用和虚方法表等被保存在永久代中，32位默认永久代的大小为64M，64位默认为85M，可以通过参数-XX:MaxPermSize进行设置，一旦类的元数据超过了永久代大小，就会抛出OOM异常。

  虚拟机团队在JDK8的HotSpot中，把永久代从Java堆中移除了，并把类的元数据直接保存在本地内存区域（堆外内存），称之为元空间。

  这样做有什么好处？
  有经验的同学会发现，对永久代的调优过程非常困难，永久代的大小很难确定，其中涉及到太多因素，如类的总数、常量池大小和方法数量等，而且永久代的数据可能会随着每一次Full GC而发生移动。

  而在JDK8中，类的元数据保存在本地内存中，元空间的最大可分配空间就是系统可用内存空间，可以避免永久代的内存溢出问题，不过需要监控内存的消耗情况，一旦发生内存泄漏，会占用大量的本地内存。

#### 判断垃圾回收

1. 引用计数法：在对象上添加一个引用计数器，每当有一个对象引用它时，计数器加1，当使用完该对象时，计数器减1，计数器值为0的对象表示不可能再被使用。引用计数法实现简单，判定高效，但不能解决对象之间相互引用的问题。

2. 可达性分析法：通过一系列称为 “GC Roots” 的对象作为起点，从这些节点开始向下搜索，搜索路径称为 “引用链”，以下对象可作为GC Roots：

   - 本地变量表中引用的对象
   - 方法区中静态变量引用的对象
   - 方法区中常量引用的对象
   - Native方法引用的对象

   当一个对象到 GC Roots 没有任何引用链时，意味着该对象可以被回收。

#### 垃圾回收算法

1. 标记-清除算法
   对待回收的对象进行标记。
   算法缺点：效率问题，标记和清除过程效率都很低；空间问题，收集之后会产生大量的内存碎片，不利于大对象的分配。
2. 复制算法
   复制算法将可用内存划分成大小相等的两块A和B，每次只使用其中一块，当A的内存用完了，就把存活的对象复制到B，并清空A的内存，不仅提高了标记的效率，因为只需要标记存活的对象，同时也避免了内存碎片的问题，代价是可用内存缩小为原来的一半。
3. 标记-整理算法
   在老年代中，对象存活率较高，复制算法的效率很低。在标记-整理算法中，标记出所有存活的对象，并移动到一端，然后直接清理边界以外的内存。

### 参考文章

1. [清蒸 JVM （一）]: http://www.importnew.com/23658.html

2. [Java GC的那些事（上）]: http://www.importnew.com/23633.html

3. [Java GC的那些事（下）]: http://www.importnew.com/23640.html


## Python


### PVM

PVM是Python的运行引擎。他通常表现为python系统的一部分。并且他是实际运行脚本的组件。

编译器：将源码编译成运行在虚拟机上执行的opcode(pyc文件)，pyc文件是在python虚拟机上执行的一种跨平台字节码。

运行时：虚拟机解释器把opcode(pyc文件)解释成具体机器的机器码，执行。

### JVM与PVM

- Java代码从源程序到执行，要经过的过程是：编译器(javac)把源代码转化为字节码，然后解释器（Java.exe）把字节码转换为计算机理解的机器码来执行。其中编译器和解释器都是Java虚拟机（JVM）的一部分，由于针对不同的硬件与OS，Java解释器有所不同，因此可以实现“一次编译、到处执行”。所以JVM是Java跨平台特性的关键所在。
- 对于Python，其源代码到执行也要经过如下过程：源代码--->字节码--->机器码。与Java不同的是，Python使用的虚拟机是基于其他语言实现的，比如我们一般使用的Python实际为Cpython，也就是其虚拟机由C实现，这个虚拟机负责把Python源码编译为字节码，再解释执行。另外，还有Jypython、Ironpython等。

## PHP-Zend&HHVM

![](http://img.it-home.org/data/attachment/forum/2016pic3/20141226102950_924.jpg)

- Zend引擎默认做法，是先编译为opcode，然后再逐条执行，通常每条指令对应的是C语言级别的函数。如果我们产生大量重复的opcode（纯PHP写的代码和函数），对应的则是Zend多次逐条执行这些C代码。
- HHVM生成和执行PHP的中间字节码（HHVM生成自己格式的中间字节码），执行时通过JIT（Just In Time，即时编译是种软件优化技术，指在运行时才会去编译字节码为机器码）转为机器码执行。JIT将大量重复执行的字节码在运行的时候编译为机器码，达到提高执行效率的目的。通常，触发JIT的条件是代码或者函数被多次重复调用。



## 写在最后

时间匆忙，囫囵吞枣，努力完善。

后端开发离不开Java，python和php，深入学习原理，比较异同，最佳使用。

2017.06.23


