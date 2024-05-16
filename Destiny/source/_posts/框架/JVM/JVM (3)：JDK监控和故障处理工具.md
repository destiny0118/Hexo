
- **`jps`** (JVM Process Status）: 类似 UNIX 的 `ps` 命令。用于查看所有 Java 进程的启动类、传入参数和 Java 虚拟机参数等信息；
- **`jstat`**（JVM Statistics Monitoring Tool）: 用于收集 HotSpot 虚拟机各方面的运行数据;
- **`jinfo`** (Configuration Info for Java) : Configuration Info for Java,显示虚拟机配置信息;
- **`jmap`** (Memory Map for Java) : 生成堆转储快照;
- **`jhat`** (JVM Heap Dump Browser) : 用于分析 heapdump 文件，它会建立一个 HTTP/HTML 服务器，让用户可以在浏览器上查看分析结果;
- **`jstack`** (Stack Trace for Java) : 生成虚拟机当前时刻的线程快照，线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合。

# JVM参数

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404102245741.png)


![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404112053964.png)

## 堆参数
```shell
初始堆(memory start)大小、最大堆大小
-Xms<heap size>[unit]
-Xmx<heap size>[unit]


年轻代大小
-XX:NewSize=n 
-XX:NewRatio=n  年轻代和年老代比值为1:n
-XX:SurvivorRatio=n 年轻代中Eden区与两个Survivor区的比值。



格式：-XX:[+-]<name> 表示启用或者禁用name属性。
例子：-XX:+UseG1GC（表示启用G1垃圾收集器）
```



# OOM排查
>OOM 全称 “Out Of Memory”，表示内存耗尽。当 JVM 因为没有足够的内存来为对象分配空间，并且垃圾回收器也已经没有空间可回收时，就会抛出这个错误。
>产生原因：
>1. 分配过少：JVM 初始化内存小，业务使用了大量内存；或者不同 JVM 区域分配内存不合理
>2. 代码漏洞：某一个对象被频繁申请，不用了之后却没有被释放，导致内存耗尽

**内存泄漏**：申请使用完的内存没有释放，导致虚拟机不能再次使用该内存，此时这段内存就泄露了。因为申请者不用了，而又不能被虚拟机分配给别人用

**内存溢出**：申请的内存超出了 JVM 能提供的内存大小，此时称之为溢出

## OOM类型
**java.lang.OutOfMemoryError: PermGen space**

Java7 永久代（方法区）溢出，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。每当一个类初次加载的时候，元数据都会存放到永久代

一般出现于大量 Class 对象或者 JSP 页面，或者采用 CgLib 动态代理技术导致

我们可以通过 `-XX：PermSize` 和 `-XX：MaxPermSize` 修改方法区大小

> Java8 将永久代变更为元空间，报错：java.lang.OutOfMemoryError: Metadata space，元空间内存不足默认进行动态扩展


**java.lang.StackOverflowError**

**虚拟机栈溢出**，一般是由于程序中存在 **死循环或者深度递归调用** 造成的。如果栈大小设置过小也会出现溢出，可以通过 `-Xss` 设置栈的大小

虚拟机抛出栈溢出错误，可以在日志中定位到错误的类、方法



**java.lang.OutOfMemoryError: Java heap space**

**Java 堆内存溢出**，溢出的原因一般由于 JVM 堆内存设置不合理或者内存泄漏导致

如果是内存泄漏，可以通过工具查看泄漏对象到 GC Roots 的引用链。掌握了泄漏对象的类型信息以及 GC Roots 引用链信息，就可以精准地定位出泄漏代码的位置

如果不存在内存泄漏，就是内存中的对象确实都还必须存活着，那就应该检查虚拟机的堆参数（-Xmx 与 -Xms），查看是否可以将虚拟机的内存调大些



```shell
tasklist | findstr "java"

查看内存堆栈信息  
//查看jvm内存分布
jmap -heap pid
jhsdb jmap --heap --pid 16279  
(对于jdk8之后的版本，不能再使用jmap -heap pid的命令了，需要使用上面的命令)。

- 查看gc情况  
    jstat -gc pid 刷新时间  
    例如：jstat -gc 1289 5000 表示每5秒查看一次pid为1289的gc时间。
- 查看内存对象实例数量  
    jmap -histo:live 16279 > c.jmap  
    将结果导入到c.jmap的文件中，这个是自定义的文件。
```







# 参考

JVM参数
[最重要的JVM参数总结 | JavaGuide](https://javaguide.cn/java/jvm/jvm-parameters-intro.html)
[美团面试：熟悉哪些JVM调优参数，幸好我准备过！-CSDN博客](https://blog.csdn.net/o9109003234/article/details/119951151)


[JDK监控和故障处理工具总结 | JavaGuide](https://javaguide.cn/java/jvm/jdk-monitoring-and-troubleshooting-tools.html)
[生产事故-记一次特殊的OOM排查 - 程语有云 - 博客园 (cnblogs.com)](https://www.cnblogs.com/mylibs/p/production-accident-0002.html)
[YGC问题排查，又让我涨姿势了！ | HeapDump性能社区](https://heapdump.cn/article/1661497)