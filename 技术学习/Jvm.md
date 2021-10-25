---

---

# Garbage Collect(垃圾回收)

# 1.1 如何确定一个对象是垃圾？

要想进行垃圾回收，得先知道什么样的对象是垃圾。
1.1.1 引用计数法
对于某个对象而言，只要应用程序中持有该对象的引用，就说明该对象不是垃圾，如果一个对象没有任
何指针对其引用，它就是垃圾。
弊端 :如果AB相互持有引用，导致永远不能被回收。
1.1.2 可达性分析
通过GC Root的对象，开始向下寻找，看某个对象是否可达
能作为GC Root:类加载器、Thread、虚拟机栈的本地变量表、static成员、常量引用、本地方法
栈的变量等。

# 1.2 垃圾收集算法

已经能够确定一个对象为垃圾之后，接下来要考虑的就是回收，怎么回收呢？
得要有对应的算法，下面聊聊常见的垃圾回收算法。

## 1.2.1 标记-清除(Mark-Sweep)

标记
找出内存中需要回收的对象，并且把它们标记出来
此时堆中所有的对象都会被扫描一遍，从而才能确定需要回收的对象，比较耗时



![image-20210706203218519](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\标记清除.png)



缺点

标记清除之后会产生大量不连续的内存碎片，空间碎片太多可能会导致以后在程
序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。
(1)标记和清除两个过程都比较耗时，效率不高
(2)会产生大量不连续的内存碎片，空间碎片太多可能会导致以后在程序运行过程中需要分配较大对象时，无
法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。

## 1.2.2 复制(Copying)



![image-20210706203552261](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\复制.png)





## 1.2.3 标记-整理(Mark-Compact)

![image-20210706204053221](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\标记整理.png)

# 1.3 分代收集算法

```
Young区：复制算法(对象在被分配之后，可能生命周期比较短，Young区复制效率比较高)
Old区：标记清除或标记整理(Old区对象存活时间比较长，复制来复制去没必要，不如做个标记再清理)

```



# 1.4 垃圾收集器

如果说收集算法是内存回收的方法论，那么垃圾收集器就是内存回收的具体实现

![image-20210706204513943](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\垃圾收集.png)

## 1.4.1 Serial收集器

![image-20210706204654045](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\serial收集器.png)



## 1.4.2 ParNew收集器



![image-20210706204818082](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\payNew收集器.png)



## 1.4.3 Parallel Scavenge收集器



![image-20210706205103338](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\Parallel Scavenge收集器.png)

## 1.4.4 Serial Old收集器



![image-20210706205351432](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\Serial Old收集器.png)



## 1.4.5 Parallel Old收集器

```
Parallel Old收集器是Parallel Scavenge收集器的老年代版本，使用多线程和"标记-整理算法"进行垃圾
回收。
```



## 1.4.6 CMS收集器



![image-20210706205850850](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\cms.png)



## 1.4.7 G1收集器

默认 1.9

1.8也有

![image-20210706205959398](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\G1收集器.png)



## 1.4.8 垃圾收集器分类

```
串行收集器->Serial和Serial Old
只能有一个垃圾回收线程执行，用户线程暂停。 适用于内存比较小的嵌入式设备 。
并行收集器[吞吐量优先]->Parallel Scanvenge、Parallel Old
多条垃圾收集线程并行工作，但此时用户线程仍然处于等待状态。 适用于科学计算、后台处理等若交互场
景 。
并发收集器[停顿时间优先]->CMS、G1
用户线程和垃圾收集线程同时执行(但并不一定是并行的，可能是交替执行的)，垃圾收集线程在执行的
时候不会停顿用户线程的运行。 适用于相对时间有要求的场景，比如Web 。

```

## 1.4.9 理解吞吐量和停顿时间

```
停顿时间->垃圾收集器 进行 垃圾回收终端应用执行响应的时间
吞吐量->运行用户代码时间/(运行用户代码时间+垃圾收集时间)
并行与并发
分代收集（仍然保留了分代的概念）
空间整合（整体上属于“标记-整理”算法，不会导致空间碎片）
可预测的停顿（比CMS更先进的地方在于能让使用者明确指定一个长度为M毫秒的时间片段内，消耗在垃圾收集
上的时间不得超过N毫秒）
初始标记（Initial Marking） 标记一下GC Roots能够关联的对象，并且修改TAMS的值，需要暂
停用户线程
并发标记（Concurrent Marking） 从GC Roots进行可达性分析，找出存活的对象，与用户线程并发
执行
最终标记（Final Marking） 修正在并发标记阶段因为用户程序的并发执行导致变动的数据，需
暂停用户线程
筛选回收（Live Data Counting and Evacuation） 对各个Region的回收价值和成本进行排序，根据
用户所期望的GC停顿时间制定回收计划
停顿时间越短就越适合需要和用户交互的程序，良好的响应速度能提升用户体验；
高吞吐量则可以高效地利用CPU时间，尽快完成程序的运算任务，主要适合在后台运算而不需要太多交互的任
务。
小结 :这两个指标也是评价垃圾回收器好处的标准，其实调优也就是在观察者两个变量。
```



## 1.4.10 如何选择合适的垃圾收集器

官网 ：https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/collectors.html#sthref28

```
优先调整堆的大小让服务器自己来选择
如果内存小于100M，使用串行收集器
如果是单核，并且没有停顿时间要求，使用串行或JVM自己选
如果允许停顿时间超过1秒，选择并行或JVM自己选
如果响应时间最重要，并且不能超过1秒，使用并发收集器
对于G1收集
```

## 1.4.11 再次理解G1

```
JDK 7开始使用，JDK 8非常成熟，JDK 9默认的垃圾收集器，适用于新老生代。
判断是否需要使用G1收集器？
1.4.12 如何开启需要的垃圾收集器
这里JVM参数信息的设置大家先不用关心，下一章节会学习到。
（1）50%以上的堆被存活对象占用
（2）对象分配和晋升的速度变化非常大
（3）垃圾回收时间比较长

```

## 1.4.12 如何开启需要的垃圾收集器

```
（1）串行
-XX：+UseSerialGC
-XX：+UseSerialOldGC
（2）并行(吞吐量优先)：
-XX：+UseParallelGC
-XX：+UseParallelOldGC
（3）并发收集器(响应时间优先)
-XX：+UseConcMarkSweepGC
-XX：+UseG1GC
```



# 查看当前项目用的什么垃圾回收

```
// 获取进程号 110
jps -l 
// 查看 是否使用 有+ 号表示有使用
jinfo -flag UseG1GC 110 
jinfo -flag UseConcMarkSweepGC 110 
jinfo -flag UseParallelGC 110 
jinfo -flag UseSerial 110 

```

# Jvm 调优 建议

jdk11 --> zGC  10毫秒

![image-20210706220334578](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\jvm调优.png)

选择建议

![image-20210706220827836](E:\Users\kai.xu\Desktop\My\Typora-Note\image\jvm\收集器建议.png)

# JVM 实战

jvm 参数

<img src="../image/Jvm/image-20210718200800905.png" alt="image-20210718200800905" style="zoom:33%;" />

查看所有参数

![image-20210718201034481](../image/Jvm/image-20210718201034481.png)





![image-20210718201408021](../image/Jvm/image-20210718201408021.png)



![image-20210718202243808](../image/Jvm/image-20210718202243808.png)'



![image-20210718203216982](../image/Jvm/image-20210718203216982.png)





![image-20210718210541694](../image/Jvm/image-20210718210541694.png)



## **1.3.4.2** **获取****Dump****文件**

手动

```
jmap -dump:format=b,file=heap.hprof 8888

```



自动

```
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=heap.hprof
```

```
-Xms20M
-Xmx20M
-XX:+PrintGCDetails
-XX:+PrintGCTimeStamps
-XX:+PrintGCDateStamps
-Xloggc:gc.log
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=heap.hprof
```



GC 日志

```
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -Xloggc:gc.log
```



**1.3.4.3** **使用**

Histogram

Histogram可以列出内存中的对象，对象的个数及其大小

Leak Suspects

查找并分析内存泄漏的可能原因

jmap -dump:format=b,file=heap.hprof 44808 

-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=heap.hprof 

