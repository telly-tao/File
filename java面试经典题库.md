

# JVM面试专题
## 01、简述一下JVM的内存模型
JVM内存模型分为**线程共享区域**和**线程私有区域**如下图：
![输入图片说明](/imgs/2026-03-17/PWXvOpWwix859B6A.png)
### 一、线程共享区域 
1.堆（Heap）
作用：存放所有对象实例和数组。
特点：（1）是垃圾回收（GC）的主要区域；
（2）分为新生代（Yong Generation）和老年代（Old Generation）
		新生代：包含Eden
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzM5NjIyMzQ3LC01OTg4NzUwMzJdfQ==
-->