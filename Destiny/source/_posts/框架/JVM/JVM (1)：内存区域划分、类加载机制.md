---
title: JVM (1)：内存区域划分、类加载机制
date: 2024-04-01
top: 8
description: 内存区域划分，垃圾回收机制，内存分配策略，类加载机制；类文件结构
categories: JVM
---


# 内存区域划分
程序计数器：记录当前线程执行的位置，线程切换后能恢复到正确的执行位置
虚拟机栈：每个 Java 方法在执行之前会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息。
本地方法栈：

堆：用于存放新创建的对象 (几乎所有对象都在这里分配内存)
方法区：主要用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404082249131.png)

## 线程私有

### 程序计数器
>程序计数器(Program Counter Register) 是一块较小的内存空间，它的作用可以看做是当前线程所执行的字节码的行号指示器。
>字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成。

### 虚拟机栈
虚拟机栈描述的是**Java 方法**执行的内存模型：每个方法被执行的时候都会同时创建一个栈帧(Stack Frame) 用于存储局部变量表、操作栈、动态链接、方法出口等信息。


### 本地方法栈
>**虚拟机栈为虚拟机执行 Java 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。**

一个Native Method就是一个**java调用非java代码**的接口。一个Native Method是这样一个java的方法：该方法是一个原生态方法，方法对应的实现不是在当前文件，而是在用其他语言（如C和C++）实现的文件中。标识符native可以与所有其它的java标识符连用，但是abstract除外。这是因为native暗示这些方法是有实现体的，只不过这些实现体是非java的，但是abstract却显然的指明这些方法无实现体。
[Java学习之——理解Native关键字 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/24054609)

## 线程共享
### 堆空间
堆空间是JVM中用于*存储对象实例*的区域，它通常被划分为新生代和老年代两个主要部分，其中新生代又包括Eden区和两个Survivor区。

Java 堆是垃圾收集器管理的主要区域，因此也被称作 **GC 堆（Garbage Collected Heap）**。
堆内存被分为三部分：
1. 新生代内存(Young Generation)，新生代包括Eden区、两个Survivor区S0和S1
2. 老生代(Old Generation)
3. 永久代(Permanent Generation)
**JDK 8 版本之后 PermGen(永久代) 已被 Metaspace(元空间) 取代，元空间使用的是本地内存。**

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404072245762.png)


分配策略:1.对象优先在Eden区分配 2.大对象直接进入老年代 3.长期存活的对象将进入老年代 4.动态对象年龄判定 5.空间分配担保等

  字符串常量池：**字符串常量池** 是 JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建。

  
### 方法区
>当虚拟机要使用一个类时，它需要读取并解析 Class 文件获取相关信息，再将信息存入到方法区。方法区会存储已被虚拟机加载的 **类信息、字段信息、方法信息、常量、静态变量、即时编译器编译后的代码缓存等数据**。

运行时常量池：Class 文件中除了有类的版本、字段、方法、接口等描述信息外，还有用于存放编译期生成的各种字面量（Literal）和符号引用（Symbolic Reference）的 **常量池表(Constant Pool Table)** 。


### 直接内存
直接内存是一种特殊的内存缓冲区，并不在 Java 堆或方法区中分配的，而是通过 JNI 的方式在本地内存上分配的。

直接内存并不是虚拟机运行时数据区的一部分，也不是虚拟机规范中定义的内存区域，但是这部分内存也被频繁地使用。而且也可能导致 `OutOfMemoryError` 错误出现。



# 对象创建
## Step1:类加载检查
虚拟机遇到一条 new 指令时，首先将去检查这个指令的参数是否能在常量池中定位到这个类的符号引用，并且检查这个符号引用代表的类是否已被加载过、解析和初始化过。如果没有，那必须先执行相应的类加载过程。


## Step2:分配内存
在**类加载检查**通过后，接下来虚拟机将为新生对象**分配内存**。对象所需的内存大小在类加载完成后便可确定，为对象分配空间的任务等同于把一块确定大小的内存从 Java 堆中划分出来。**分配方式**有 **“指针碰撞”** 和 **“空闲列表”** 两种，**选择哪种分配方式由 Java 堆是否规整决定，而 Java 堆是否规整又由所采用的垃圾收集器是否带有压缩整理功能决定**。

**内存分配的两种方式** （补充内容，需要掌握）：

- 指针碰撞：
    - 适用场合：堆内存规整（即没有内存碎片）的情况下。
    - 原理：用过的内存全部整合到一边，没有用过的内存放在另一边，中间有一个分界指针，只需要向着没用过的内存方向将该指针移动对象内存大小位置即可。
    - 使用该分配方式的 GC 收集器：Serial, ParNew
- 空闲列表：
    - 适用场合：堆内存不规整的情况下。
    - 原理：虚拟机会维护一个列表，该列表中会记录哪些内存块是可用的，在分配的时候，找一块儿足够大的内存块儿来划分给对象实例，最后更新列表记录。
    - 使用该分配方式的 GC 收集器：CMS

选择以上两种方式中的哪一种，取决于 Java 堆内存是否规整。而 Java 堆内存是否规整，取决于 GC 收集器的算法是"标记-清除"，还是"标记-整理"（也称作"标记-压缩"），值得注意的是，复制算法内存也是规整的。

## Step3:初始化零值
内存分配完成后，虚拟机需要将分配到的内存空间都初始化为零值（不包括对象头），这一步操作保证了对象的实例字段在 Java 代码中可以不赋初始值就直接使用，程序能访问到这些字段的数据类型所对应的零值。

## Step4:设置对象头
初始化零值完成之后，**虚拟机要对对象进行必要的设置**，例如这个对象是哪个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的 GC 分代年龄等信息。 **这些信息存放在对象头中。** 另外，根据虚拟机当前运行状态的不同，如是否启用偏向锁等，对象头会有不同的设置方式。


Hotspot 虚拟机的对象头( Object Header ) 分为两部分信息，第一部分用于存储对象自身的运行时数据，如哈希码
GC( HashCode ) 、GC 分代年龄( Generational Age ) 等，这部分数据的长度在32 位和64 位的虚拟机中分别为32 个和64 个Bits,官方称它为“Mark Word",它是实现轻量级锁和偏向锁的关键另外一部分用千存储指向方法区对象类型数据的指针，如果是数组对象的话，还会有一个额外的部分用千存储数组长度。

如在32 位的HotSpot 虚拟机中对象未被锁定的状态下， Mark Word 的32 个Bits 空间中的25Bits 用千存储对象哈希
码(HashCode ) , 4B心用千存储对象分代年龄， 2Bits 用千存储锁标志位，lBit 固定为0.
## Step5:执行 init 方法
在上面工作都完成之后，从虚拟机的视角来看，一个新的对象已经产生了，但从 Java 程序的视角来看，对象创建才刚开始，`init` 方法还没有执行，所有的字段都还为零。所以一般来说，执行 new 指令之后会接着执行 `init` 方法，把对象按照程序员的意愿进行初始化，这样一个真正可用的对象才算完全产生出来。



# 类加载过程

## 类的生命周期
类从被加载到虚拟机内存中开始到卸载出内存为止，它的整个生命周期可以简单概括为 7 个阶段：：加载（Loading）、验证（Verification）、准备（Preparation）、解析（Resolution）、初始化（Initialization）、使用（Using）和卸载（Unloading）。其中，验证、准备和解析这三个阶段可以统称为连接（Linking）。

![image.png|450](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404092255659.png)

## 类加载过程
### 加载
加载通过 **类加载器** 完成的。类加载器有很多种，当我们想要加载一个类的时候，具体是哪个类加载器加载由 **双亲委派模型** 决定
1. 通过全类名获取定义此类的二进制字节流。
2. 将字节流所代表的静态存储结构转换为方法区的运行时数据结构。
3. 在内存中生成一个代表该类的 `Class` 对象，作为方法区这些数据的访问入口。


### 验证
**验证是连接阶段的第一步，这一阶段的目的是确保 Class 文件的字节流中包含的信息符合《Java 虚拟机规范》的全部约束要求，保证这些信息被当作代码运行后不会危害虚拟机自身的安全。**

1. 文件格式验证（Class 文件格式检查）
2. 元数据验证（字节码语义检查）
3. 字节码验证（程序语义检查）
4. 符号引用验证（类的正确性检查）

### 准备
**准备阶段是正式为类变量分配内存并设置类变量初始值的阶段**


### 解析
**解析阶段是虚拟机将常量池内的符号引用替换为直接引用的过程。** 解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用限定符 7 类符号引用进行。


### 初始化
**初始化阶段是执行初始化方法 `<clinit> ()`方法的过程，是类加载的最后一步，这一步 JVM 才开始真正执行类中定义的 Java 程序代码(字节码)。**


### 类卸载
1. 该类的所有的实例对象都已被 GC，也就是说堆不存在该类的实例对象。
2. 该类没有在其他任何地方被引用
3. 该类的类加载器的实例已被 GC

# 类加载器
类加载器是一个负责加载类的对象。`ClassLoader` 是一个抽象类。给定类的二进制名称，类加载器应尝试定位或生成构成类定义的数据。
每个 Java 类都有一个引用指向加载它的 `ClassLoader`。

**类加载器的主要作用就是加载 Java 类的字节码（ `.class` 文件）到 JVM 中（在内存中生成一个代表该类的 `Class` 对象）。**

```java
public abstract class ClassLoader {
  ...
  private final ClassLoader parent;
  // 由这个类加载器加载的类。
  private final Vector<Class<?>> classes = new Vector<>();
  // 由VM调用，用此类加载器记录每个已加载类。
  void addClass(Class<?> c) {
        classes.addElement(c);
   }
  ...
}

```

- `BootstrapClassLoader`(启动类加载器)：最顶层的加载类，由 C++实现，通常表示为 null，并且没有父级，主要用来加载 JDK 内部的核心类库（ `%JAVA_HOME%/lib`目录下的 `rt.jar`、`resources.jar`、`charsets.jar`等 jar 包和类）以及被 `-Xbootclasspath`参数指定的路径下的所有类。
- **`ExtensionClassLoader`(扩展类加载器)**：主要负责加载 `%JRE_HOME%/lib/ext` 目录下的 jar 包和类以及被 `java.ext.dirs` 系统变量所指定的路径下的所有类。
- **`AppClassLoader`(应用程序类加载器)**：面向我们用户的加载器，负责加载当前应用 classpath 下的所有 jar 包和类。


## 双亲委派模型
双亲委派机制（Parent Delegation Mechanism）是 Java 中的一种类加载机制。在 Java 中，类加载器负责加载类的字节码并创建对应的 Class 对象。双亲委派机制的核心思想是：当一个类加载器收到类加载请求时，它会先将该请求委派给它的父类加载器去尝试加载。只有当父级加载器无法加载该类时，才会尝试自行加载。

这个机制的作用在于**保证类的加载是从上到下的**，即从启动类加载器开始，逐级向下传递，直到找到所需的类或者无法加载。这样可以**避免类的重复加载**，提高了类加载的效率和安全性。例如，如果一个类已经被父类加载器加载过，那么子类加载器就不会再次加载它，从而避免了类的冲突和不一致性。

类加载器虽然只用于实现类的加载动作，但是对于任意一个类，都需要由加载它的类加载器和这个类本身共同确立其在Java虚拟机中的**唯一性**。

总之，双亲委派机制是 Java 类加载器中的一项重要特性，它**确保了类的加载顺序和一致性**，使得 Java 程序能够正常运行并保持良好的安全性。

>`ClassLoader` 类使用委托模型来搜索类和资源。每个 `ClassLoader` 实例都有一个相关的父类加载器。需要查找类或资源时，`ClassLoader` 实例会在试图亲自查找类或资源之前，将搜索类或资源的任务委托给其父类加载器。  虚拟机中被称为 "bootstrap class loader"的内置类加载器本身没有父类加载器，但是可以作为 `ClassLoader` 实例的父类加载器。

- `ClassLoader` 类使用委托模型来搜索类和资源。
- 双亲委派模型要求除了顶层的启动类加载器外，其余的类加载器都应有自己的父类加载器。
- `ClassLoader` 实例会在试图亲自查找类或资源之前，将搜索类或资源的任务委托给其父类加载器。


- 避免类的重复加载
- 保护程序安全，防止核心API被随意篡改
    - 自定义类：java.lang.String (没用)
    - 自定义类：java.lang.ShkStart（报错：阻止创建 java.lang开头的类）

结合上面的源码，简单总结一下双亲委派模型的执行流程：

- 在类加载的时候，系统会首先判断当前类是否被加载过。已经被加载的类会直接返回，否则才会尝试加载（每个父类加载器都会走一遍这个流程）。
- 类加载器在进行类加载的时候，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成（调用父加载器 `loadClass()`方法来加载类）。这样的话，所有的请求最终都会传送到顶层的启动类加载器 `BootstrapClassLoader` 中。
- 只有当父加载器反馈自己无法完成这个加载请求（它的搜索范围中没有找到所需的类）时，子加载器才会尝试自己去加载（调用自己的 `findClass()` 方法来加载类）。
- 如果子类加载器也无法加载这个类，那么它会抛出一个 `ClassNotFoundException` 异常。

[详谈双亲委派机制（面试常问）[通俗易懂]-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2055271)
[java中双亲委派机制(+总结） - luwanglin - 博客园 (cnblogs.com)](https://www.cnblogs.com/luckforefforts/p/13642685.html)

# 参考
[某团面试：如果线上遇到了OOM，你该如何排查？如何解决？哪些方案？-CSDN博客](https://blog.csdn.net/o9109003234/article/details/121917786)
[Java内存区域详解（重点） | JavaGuide](https://javaguide.cn/java/jvm/memory-area.html#%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%AE%BF%E9%97%AE%E5%AE%9A%E4%BD%8D)
[类加载过程详解 | JavaGuide](https://javaguide.cn/java/jvm/class-loading-process.html)