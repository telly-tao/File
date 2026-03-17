

# JVM面试专题
## 01、简述一下JVM的内存模型
JVM内存模型分为***线程共享区域***和***线程私有区域***如下图：
![输入图片说明](/imgs/2026-03-17/PWXvOpWwix859B6A.png)
### 一、线程共享区域 
**1.堆（Heap）**
- 作用：存放所有对象实例和数组。
- 特点：（1）是垃圾回收（GC）的主要区域；（2）分为新生代（Yong Generation）和老年代（Old Generation）
新生代：包含Eden区、Survivor From区和Survivor To区（默认比例8:1:1）
老年代：长期存和的对象。
- 异常：OutOfMemoryError(当堆无法分配对象时)。

**2.方法区（Method Area）**
- 作用：存储类信息、常量、静态变量、JIT编译后的代码。
- 实现：JDK1.7时叫永久代；JDK1.8+元空间（Metaspace）,使用本地内存（不在手JVM堆大小限制）
包含子区域：运行时常量池-存放类、方法、字段的符号引用和字面量（如String.interm()的字符串）
- 异常：OutOfMemoryError（元空间溢出）
### 二、线程私有区域（每个线程独立）
**1.程序计数器（Program Counter Register）**
- 作用：用于存储下一条待执行指令的内存地址。
- 特点：唯一不会发生OutOfMemoryError的区域；线程切换后能恢复到正确执行位置。

**2.虚拟机栈（VM Stack）**
- 作用：存储调用的栈帧（Stack Frame）。
栈帧结构：
（1）局部变量表（Local Variable Array）：存放方法参数和局部变量。
（2）操作数栈（Operand Stack）：执行字节码指令的工作区（如加减乘除）。
（3）动态链接（Dynamic Linking）：指向方法区总该方法的符合引用。
（4）返回地址（Return Address）：方法退出后返还到的指令位置。
![输入图片说明](/imgs/2026-03-17/GBKQroK0ARLZM8Ox.png)
- 异常：
StackOverFlowError（栈深度超过限制，如无限递归）。
OutOfMemoryError（扩展栈时无法申请到足够内存）。

**3.本地方法栈（Native Method Stack）**
- 作用：为JVM调用Native方法（如C/C++代码）服务。
- 特点：与虚拟机栈类似，但服务于Native方法
- 异常：同虚拟机栈（StackOverflowError/OutOfMemoryError）

### 三 、完整JVM内存模型图
![输入图片说明](/imgs/2026-03-17/b2JbGF2scGJyP3Fn.png)
## 02、堆和栈的区别
**1.根本性质与归属**
- 栈（JVM Stack）：
（1）是线程私有的内存区域。
（2）每个线程在创建时都会分配一个独立栈。
（3）主要用于存储方法执行时的栈帧。
- 堆（Heap）：
（1）是线程共享的内存区域。
（2）JVM启动时创建，***整个JVM进程只有一个堆***。
（3）主要用于存储对象实例（new关键字创建的对象）和数组。

**2.存储内容**
- 栈：存储当前线程正在执行方法的栈帧。
栈帧：
（1）局部变量表（Local Variable Array）：存放方法参数和局部变量。
（2）操作数栈（Operand Stack）：执行字节码指令的工作区（如加减乘除）。
（3）动态链接（Dynamic Linking）：指向方法区总该方法的符合引用。
（4）返回地址（Return Address）：方法退出后返还到的指令位置。

- 堆：存储所有对象实例（包含其字段名）和数组（包含数组元素）。
字符串常量池（String Table）在HotSpot JVM中通常也位于堆中（Java7及以后）
> 注意：类元信息（Class Metadata）存储在方法区（Metaspace/PermGen）,不在堆中（Java8及以后Metaspace在本地）。

**3.生命周期管理**
- 栈：自动管理与作用域绑定
方法调用时，对应的栈帧被压入栈顶；方法执行结束（正常返回或抛出异常）时栈帧被弹出销毁。
栈帧销毁意味着其中的局部变量（基本类型、对象引用）立即失效。
- 堆：由垃圾回收器（Garbage Collector，GC）管理
对象的生命周期从new创建开始；当对象不再被任何GC Roots对象通过引用链可达时（即成为“垃圾”），由GC在某个不确定的时刻自动回收其内存。
程序员无法精确控制对象何时被回收。

**4.线程安全**
- 栈：天然线程安全
因为每个线程操作自己的私有的栈，不存在线程件共享数据。
- 堆：非线程安全
堆是共享区域，多个线程可以同时访问或修改同一个堆上的对象。
需要程序员使用synchronized、volatile、锁等机制来保证并发访问的正确性（可见性、原子性、有序性）否则会出现线程安全问题。

**5.错误与异常**
- 栈：
***StackOverflowError：*** 当线程请求的栈超过虚拟机允许的最大深度（通常是深递归或过大的局部变量数组）
***OutOfMemoryError：*** 如果栈可以动态扩展，但在扩展是无法申请到足够的内存。
- 堆：
***OutOfMemoryError：Java heap space：*** 堆中没有足够内存完成新对象的分配，并且堆也无法再扩展。
***OutOfMemoryError：GC Overhead limit exceeded等：*** 由GC问题（如频繁Full GC但回收效果差）导致的OOM。

**6.碎片化**
- 栈：无碎片化问题
- 堆：可能产生内存碎片化。
频繁创建和销毁不同大小的对象会导致内存中出现许多不连续的小空闲区域。
现代GC算法（如G1、ZGC、Shenandoah）会进行压缩（Compaction）来减少或消除碎片。

## 03、简述Java对象的结构
Java对象在内存中的结构主要由三部分组成：***对象头（Object Header）*** 、***实例数据（Instance Data）*** 和 ***对齐填充（Padding）*** 。
**1.对象头（Object Header）**
对象头包含两类关键信息：
***Mark Word：*** 存储对象自身的运行时元数据，长度在32位系统为32位，在64位系统为64位。包含：哈希码（HashCode）、GC分代年龄（Generation GC Age）、锁状态标志（如偏向锁、轻量级锁、重量级锁）、线程持有的锁/偏向线程ID、偏向时间戳。
***类型指针：*** 指向方法区中对象的类元数据（Class 对象），JVM通过它确定对象属于哪个类。64位系统默认开启压缩指针（-XX:+UseCompressedOops），此时占4个字节，否则占8个字节。
**2.实例数据（Instance Data）**
 存储对象的所有字段（包括从父类继承过来的字段）。
 字段排列顺序受虚拟机分配策略影响：
 （1）基本类型优先：long/double>int/float>short/char>byte/boolen>引用类型。
 （2）父类字段在子类之前。
 字段重排序：为节省内存，JVM可能重新排列字段顺序（如将int和boolean相邻放置以减少对齐填充）。
 **对齐填充（Padding）**
 JVM要求对象大小必须是8字节的整数倍。
 ```
 实例对象：
 一个包含int id 和 String name 的类
 对象头：12字节，int id： 4字节，String name（引用）:4字节
 总计：20字节，需填充4字节，最终占用24字节。
 ```
 ![输入图片说明](/imgs/2026-03-17/KkqhhBz4Yqmi4o9b.png)
 ![输入图片说明](/imgs/2026-03-17/fOc30g7usPeMRiUY.png)
 - 作用：提升内存访问效率（CPU通常按块读取内存）。
 
## 04、如何判断对象可以被回收
 **1、可达性分析算法**
 从GC Roots对象出发，遍历引用链。若对象与GC Roots间无路径，则判定可回收。
 ***GC Roots 引用范畴***
 （1）虚拟机栈中的局部变量（栈帧中的本地变量表）
 （2）方法区中类静态属性引用的对象（static 变量）
 （3）方法区中常量引用的对象（final常量）
 （4）本地方法栈中JNI引用的对象（Native方法）
 （5）虚拟机内部引用（如Class对象、异常对象等）
 ![输入图片说明](/imgs/2026-03-17/ug0qQibsrJGGhyiy.png)
 结论：ObjectF因与GC Roots无路径，可被回收。
 **2、四种引用决定回收策略**
 （1）强引用（Strong Reference）：永不回收
 （2）软引用（Soft Reference）：内存不足时回收，缓存（图片、网页）
 （3）弱引用（Weak Reference）：无论内存是否充足，GC必回收，缓存（WeakHashMap）
 （4）虚引用（Phantom Reference）：任何时候都可以回收，需配合引用队列使用。
 **3、分代回收机制**
 Java堆内存分为不同区域，对象回收策略页不同：
 ***新生代（Yong Generation）：*** 新对象在Eden区创建，经过Minor GC后存活的对象进入Survivor区，默认经历15次GC后进入老年代。
 ***老年代（Old Generation）：*** 存放长期存活的对象，触发Full GC时回收。
 ***永久代/元空间：*** 存放类元数据，类卸载时回收相关对象。
 
## 05、你知道哪些垃圾收集算法
### 一、基础垃圾收集算法
Java虚拟机主要采用以下几种基础垃圾收集算法：
**1、标记-清除算法（Mark-Sweep）：**


<!--stackedit_data:
eyJoaXN0b3J5IjpbMTEwMDU2MTY2OSwtNTk5ODUyOTA1LC05MD
Y3MDA4NzYsLTE5MDM3ODk1NzUsLTE0MjE1Nzk1OTUsMTQ3NjA0
NTAzMiw0MTY5NDM5NTIsLTU5ODg3NTAzMl19
-->