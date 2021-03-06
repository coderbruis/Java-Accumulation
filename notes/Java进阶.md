# 1、Java并发

# 2、Java虚拟机——JVM

## 2.1 JVM内存结构

如果没有特殊说明，都是针对的是 HotSpot 虚拟机。

> Java有哪些内存结构？说一下Java的内存结构？

Java内存区域可以分为运行时数据区和直接内存区。

**运行时数据区包含了：方法区、堆区、Java虚拟机栈、本地方法栈。【JDK1.8】以前**

### 2.1.1 方法区

首先，方法区是线程间共享的内存区域，它的作用是用于存储已被虚拟机加载的类信息、常量、静态变量（类变量）。方法区也可以成为非堆区。对于HotSpot虚拟机来说，方法区也可以成为“永久代”，这是因为HotSpot的设计团队将GC分代收集扩展至方法区，或者说是使用永久代来实现方法区。不像堆区那样会进行频繁的GC动作，垃圾收集在方法区进行的比较少，这是因为方法区主要是针对“常量池的回收”以及“类型的卸载”。如果方法区中无法满足内存分配需求时，将抛出OutOfMemoryError异常。

### 2.1.2 堆区

Java堆是Java虚拟机中所管理内存中最大的一块，Java堆是所有线程都共享的内存区域，虚拟机启动时，Java堆就会被创建。此区域的唯一目的就是存放对象实例，几乎所有对象都在这里进行内存分配（除了Object对象是存放在方法区中的）。另外由于堆内存区域会进行频繁的GC，所以也被称为GC堆。
从内存回收的角度来看，由于收集器都是基于分代收集算法，所以Java堆区可以细分为新生代和老年代，新生代也还可以细分为Eden空间、From Survivor空间以及To Survivor空间。
从内存分配的角度来看，线程共享的Java堆可能划分出多个线程私有的分配缓冲区（TheadLocal Allocation Buffer，TLAB）。
Java堆内存可以处于物理上不连续的内存空间中，只要逻辑上连续即可。对于Java堆内存空间，如果没法分配到足够的空间，并且堆内存也无法进行再扩展时，将会抛出OutOfMemoryError异常。另外，在程序中设置堆内存可以通过-Xms和-Xmx来进行控制。
需要注意一点的是，java.lang.Class类的对象比较特殊，它虽然是对象，但其对象实例存放在方法区里的，这个对象将作为程序访问方法区中的这些类型数据的外部接口。

### 2.1.3 Java虚拟机栈

Java虚拟机栈是线程私有的，它的生命周期和线程的生命周期相同。一个方法在执行时会创建一个对应的栈帧，栈帧存储于Java虚拟机栈中。如果方法执行完了，则栈帧就会从Java虚拟机栈中出栈。栈帧中存有“局部变量表”，“操作数栈”，“动态链接”等信息。局部变量表中存放的都是编译期可知的各种基本数据类型，包括了：boolean、byte、char、short、long、int、double、float，还有对象引用等。
在Java虚拟机规范中，对这个区域规定了两种异常状况：
① 如果线程请求栈深度大于虚拟机栈所允许的深度，将抛出StackOverFlow异常；
② 当前大部分虚拟机都是可以动态扩展内存的，如果Java虚拟机扩展时没法申请到足够的内存，就会抛出OutOfMemoryError异常。

### 2.1.4 本地方法栈

本地方法栈和Java虚拟机栈发挥的作用相似，它们之间的区别在于Java虚拟机栈为Java方法服务，而本地方法栈为虚拟机用到的Native方法服务。与Java虚拟机栈一样，本地方法栈区域也会抛出StackOverFlowError和OutOfMemoryError。

### 2.1.5 直接内存【非运行时数据区】

直接内存（Direct Memory）并不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范中定义的内存区域，但是这部分内存也被频繁地使用，而且也可能导致OutOfMemoryError异常出现。
在JDK1.4中新加入了NIO（New Input / Output）类，引入了一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，这种方式可以使用Native函数库直接分配对外内存，然后通过一个存储在Java堆中的DirectByteBuffer对象作为这块内存的引用进行操作。这样能在一些场景中显著提升性能，因为避免了在Java堆和Native堆中来回复制数据。
显然，本机直接内存的分配不会受到Java堆大小的限制，但是，既然是内存，肯定还是会受到本机总内存（包括RAM以及SWAP区或者分页文件）大小以及处理器寻址空间的限制。服务器管理员在配置虚拟机参数时，会根据实际内存设置-Xmx等参数信息，但经常忽略直接内存，使得各个内存区域总和大于物理内存限制（包括物理的和操作系统级的限制），从而导致动态扩展时出现OutOfMemoryError异常。

# 3、Spring、SpringMVC、MyBatis、SpringBoot四件套

# 4、消息队列

# 5、缓存

# 6、Netty

# 7、Dubbo

# 8、分布式

# 9、Docker

# 10、K8S 
