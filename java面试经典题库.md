

# JVM面试专题
## 01、简述一下JVM的内存模型
JVM内存模型分为**线程共享区域**和**线程私有区域**如下图：
![输入图片说明](/imgs/2026-03-17/PWXvOpWwix859B6A.png)
### 一、线程共享区域 
***1.堆（Heap）***
- 作用：存放所有对象实例和数组。
- 特点：（1）是垃圾回收（GC）的主要区域；（2）分为新生代（Yong Generation）和老年代（Old Generation）
新生代：包含Eden区、Survivor From区和Survivor To区（默认比例8:1:1）
老年代：长期存和的对象。
- 异常：OutOfMemoryError(当堆无法分配对象时)。
***2.方法区（Method Area）***
- 作用：存储类信息、常量、静态变量、JIT编译后的代码。
- 实现：JDK1.7时叫永久代；JDK1.8+元空间（Metaspace）,使用本地内存（不在手JVM堆大小限制）
包含子区域：运行时常量池
存放类、方法、字段的符号引用和字面量（如String.interm()的字符串）
- 异常：OutOfMemoryError（元空间溢出）
### 二、线程私有区域（每个线程独立）
***1.程序计数器（Program Counter Register）***
- 作用：用于存储下一条待执行指令的内存地址。线程切换后能恢复到正确执行位置。
***2.虚拟机栈（VM Stack）***
- 作用：存储调用的栈帧（Stack Frame）。
栈帧结构：
（1）局部变量表：存放方法参数和局部变量。
（2）操作数栈：执行字节码指令的工作区（如加减乘除）。
（3）动态链接：指向方法区总该方法的符合引用。
（4）返回地址：方法退出后返还到的指令位置。
![输入图片说明](/imgs/2026-03-17/GBKQroK0ARLZM8Ox.png)
- 异常：
StackOverFlowError（栈深度超过限制，如无限递归）。
OutOfMemoryError（扩展栈时无法申请到足够内存）。
***本地方法栈（Native Method Stack）***
- 作用：为JVM调用Native方法（如C/C++代码）服务。
- 特点：与虚拟机栈类似，但服务于Native方法
- 异常：同虚拟机栈（StackOverflowError/OutOfMe）
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NjcyMzA2NSwtNTk4ODc1MDMyXX0=
-->