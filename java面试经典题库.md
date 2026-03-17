

# JVM面试专题
## 01、简述一下JVM的内存模型
JVM内存模型分为**线程共享区域**和**线程私有区域**如下图：
![输入图片说明](/imgs/2026-03-17/PWXvOpWwix859B6A.png)
### 一、线程共享区域 
***1.堆（Heap）***
作用：存放所有对象实例和数组。
特点：（1）是垃圾回收（GC）的主要区域；（2）分为新生代（Yong Generation）和老年代（Old Generation）
新生代：包含Eden区、Survivor From区和Survivor To区（默认比例8:1:1）
老年代：长期存和的对象。
异常：OutOfMemoryError(当堆无法分配对象时)。
***2.方法区（Method Area）***
作用：存储类信息、常量、静态变量、JIT编译后的代码。
实现：JDK1.7时叫永久代；JDK1.8+元空间（Metaspace）,使用本地内存（不在手JVM堆大小限制）
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjA1MDQ0ODkzLC01OTg4NzUwMzJdfQ==
-->