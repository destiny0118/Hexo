---
title: Java并发(2)：ThreadLocal、线程池
description: ThreadLocal、线程池
categories: java
tags:
  - 并发
top: 7
date: 2024-05-15
---

# 线程局部变量(ThreadLocal)
>ThreadLocal辅助类为各个线程提供各自的实例

```java
public static final SimpleDateFormat dateFormat=new SimpleDateFormat("yyyyMMdd HHmm");
String dateStamp=dateFormat.format(new Date());

public static final ThreadLocal<SimpleDateFormat> formatter = ThreadLocal.withInitial(
	() -> new SimpleDateFormat("yyyyMMdd HHmm")
);



public static final ThreadLocal<SimpleDateFormat> formatter = new ThreadLocal<SimpleDateFormat>(){
	@Override
	protected SimpleDateFormat initialValue(){
		new SimpleDateFormat("yyyyMMdd HHmm");
	}
};
String dateStamp=formatter.get().format(new Date());

```
## 内存泄漏
`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用，而 value 是强引用。所以，如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。

这样一来，`ThreadLocalMap` 中就会出现 key 为 null 的 Entry。假如我们不做任何措施的话，value 永远无法被 GC 回收，这个时候就可能会产生内存泄露。`ThreadLocalMap` 实现中已经考虑了这种情况，在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录。使用完 `ThreadLocal`方法后最好手动调用`remove()`方法

## 引用类型

**1．强引用（StrongReference）**
当内存空间不足，Java 虚拟机抛出 OutOfMemoryError 错误，使程序异常终止

**2．软引用（SoftReference）**
可有可无，如果内存空间足够，垃圾回收器就不会回收它，如果==内存空间不足，就会回收这些对象的内存==。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。

**3．弱引用（WeakReference）**
可有可无，弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。

**4．虚引用（PhantomReference）**
虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收。**虚引用主要用来跟踪对象被垃圾回收的活动**。

虚引用必须和引用队列（ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。

弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java 虚拟机就会把这个弱引用加入到与之关联的引用队列中。

[Java 并发 - ThreadLocal详解 | Java 全栈知识体系 (pdai.tech)](https://pdai.tech/md/java/thread/java-thread-x-threadlocal.html)
# 线程池
>池化技术，线程池、数据库连接池、HTTP 连接池等等都是对这个思想的应用。池化技术的思想主要是为了减少每次获取资源的消耗，提高对资源的利用率。
>线程池是管理一系列线程的资源池。

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控

## 创建线程池
**方式一：通过`ThreadPoolExecutor`构造函数来创建（推荐）。**
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202405151608044.png)

**方式二：通过 `Executor` 框架的工具类 `Executors` 来创建。**
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202405151609702.png)


- `FixedThreadPool`：固定线程数量的线程池。该线程池中的线程数量始终不变。当有一个新的任务提交时，线程池中若有空闲线程，则立即执行。若没有，则新的任务会被暂存在一个任务队列中，待有线程空闲时，便处理在任务队列中的任务。
- `SingleThreadExecutor`： 只有一个线程的线程池。若多余一个任务被提交到该线程池，任务会被保存在一个任务队列中，待线程空闲，按先入先出的顺序执行队列中的任务。
- `CachedThreadPool`： 可根据实际情况调整线程数量的线程池。线程池的线程数量不确定，但若有空闲线程可以复用，则会优先使用可复用的线程。若所有线程均在工作，又有新的任务提交，则会创建新的线程处理任务。所有线程在当前任务执行完毕后，将返回线程池进行复用。
- `ScheduledThreadPool`：给定的延迟后运行任务或者定期执行任务的线程池。

| 方法                                | 描述                 |
| --------------------------------- | ------------------ |
| newCachedThreadPool               | 必要时创建新线程，空闲线程保留60秒 |
| newFixedThreadPool                | 包含固定数量线程，空闲线程保留    |
| newSingleThreadExecutor           | 只有一个线程             |
| 预定执行                              |                    |
| newScheduledThreadPool            |                    |
| newSingleThreadSccheduledExecutor |                    |
|                                   |                    |
|                                   |                    |
`Executors` 返回线程池对象的弊端如下：

- `FixedThreadPool` 和 `SingleThreadExecutor`:使用的是无界的 `LinkedBlockingQueue`，任务队列最大长度为 `Integer.MAX_VALUE`,可能堆积大量的请求，从而导致 OOM。
- `CachedThreadPool`:使用的是同步队列 `SynchronousQueue`, 允许创建的线程数量为 `Integer.MAX_VALUE` ，如果任务数量过多且执行速度较慢，可能会创建大量的线程，从而导致 OOM。
- `ScheduledThreadPool` 和 `SingleThreadScheduledExecutor`:使用的无界的延迟阻塞队列`DelayedWorkQueue`，任务队列最大长度为 `Integer.MAX_VALUE`,可能堆积大量的请求，从而导致 OOM。

## 线程池参数
```java
int corePoolSize,//线程池的核心线程数量 
int maximumPoolSize,//线程池的最大线程数 
long keepAliveTime,//当线程数大于核心线程数时，多余的空闲线程存活的最长时间 
TimeUnit unit,//时间单位 
BlockingQueue<Runnable> workQueue,//任务队列，用来储存等待执行任务的队列 
ThreadFactory threadFactory,//线程工厂，用来创建线程，一般默认即可 
RejectedExecutionHandler handler//拒绝策略，当提交的任务过多而不能及时处理时，我们可以定制策略来处理任务
```

- corePoolSize（核心线程数）：这是线程池的基本大小，即线程池中始终保持的线程数量，不受空闲时间的影响。当有新任务提交时，如果线程池中的线程数小于corePoolSize，即便存在空闲线程，线程池也会创建一个新线程来执行任务。
    
- maximumPoolSize（最大线程数）：线程池允许创建的最大线程数量。当线程池中的线程数超过corePoolSize，但小于maximumPoolSize时，只有在队列满的情况下，才会创建新的线程来处理任务。
    
- keepAliveTime（空闲线程存活时间）：当线程池中的线程数超过corePoolSize时，多余的线程在空闲时间超过keepAliveTime后会被销毁，直到线程池中的线程数不超过corePoolSize。这有助于在系统空闲时节省资源。
    
- unit（时间单位）：这是keepAliveTime的时间单位，如秒、毫秒等。它定义了如何解释keepAliveTime的值。
    
- workQueue（工作队列）：这是一个用于存储等待执行的任务的队列。当线程池中的线程数达到corePoolSize，且这些线程都在执行任务时，新提交的任务会被放入workQueue中等待执行。
    
- threadFactory（线程工厂）：这是一个用于创建新线程的对象。通过自定义线程工厂，我们可以控制新线程的创建方式，例如设置线程的名字、是否是守护线程等。
    
- handler（拒绝策略）：当所有线程都在繁忙，且workQueue也已满时，新提交的任务会被拒绝。此时，就需要一个拒绝策略来处理这种情况。常见的拒绝策略有抛出异常、丢弃当前任务、丢弃队列中最旧的任务或者由调用线程直接执行该任务等。
    

**`ThreadPoolExecutor` 3 个最重要的参数：**

- **`corePoolSize` :** 任务队列未达到队列容量时，最大可以同时运行的线程数量。
- **`maximumPoolSize` :** 任务队列中存放的任务达到队列容量的时候，当前可以同时运行的线程数量变为最大线程数。
- **`workQueue`:** 新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，新任务就会被存放在队列中。

`ThreadPoolExecutor`其他常见参数 :

- **`keepAliveTime`**:线程池中的线程数量大于 `corePoolSize` 的时候，如果这时没有新的任务提交，多余的空闲线程不会立即销毁，而是会等待，直到等待的时间超过了 `keepAliveTime`才会被回收销毁，线程池回收线程时，会对核心线程和非核心线程一视同仁，直到线程池中线程的数量等于 `corePoolSize` ，回收过程才会停止。
- **`unit`** : `keepAliveTime` 参数的时间单位。
- **`threadFactory`** :executor 创建新线程的时候会用到。
- **`handler`** :饱和策略。关于饱和策略下面单独介绍一下。

## 拒绝策略
如果当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任务时，`ThreadPoolExecutor` 定义一些策略:

- `ThreadPoolExecutor.AbortPolicy`：抛出 RejectedExecutionException来**拒绝新任务的处理**。
- `ThreadPoolExecutor.CallerRunsPolicy`：调用执行自己的线程运行任务，也就是直接在调用`execute`方法的线程中运行(`run`)被拒绝的任务，如果执行程序已关闭，则会丢弃该任务。因此这种策略会降低对于新任务提交速度，影响程序的整体性能。如果你的应用程序可以承受此延迟并且你要求任何一个任务请求都要被执行的话，你可以选择这个策略。
- `ThreadPoolExecutor.DiscardPolicy`：不处理新任务，直接丢弃掉。
- `ThreadPoolExecutor.DiscardOldestPolicy`：此策略将丢弃最早的未处理的任务请求。

### CallerRunsPolicy 拒绝策略有什么风险？如何解决？
>如果想要保证任何一个任务请求都要被执行的话，那选择 `CallerRunsPolicy` 拒绝策略更合适一些。

如果走到`CallerRunsPolicy`的任务是个非常耗时的任务，且处理提交任务的线程是主线程，可能会导致主线程阻塞，影响程序的正常运行。
#### 解决办法
- 增加阻塞队列`BlockingQueue`的大小并调整堆内存以容纳更多的任务，确保任务能够被准确执行。
- 为了充分利用 CPU，我们还可以调整线程池的`maximumPoolSize` （最大线程数）参数，这样可以提高任务处理速度，避免累计在 `BlockingQueue`的任务过多导致内存用完。
- 任务持久化
	1. 设计一张任务表将任务存储到 MySQL 数据库中。
	2. Redis缓存任务
	3. 将任务提交到消息队列

任务持久化：
- **拒绝策略**： 实现`RejectedExecutionHandler`接口自定义拒绝策略，自定义拒绝策略负责将线程池暂时无法处理（此时阻塞队列已满）的任务入库（保存到 MySQL 中）。注意：线程池暂时无法处理的任务会先被放在阻塞队列中，阻塞队列满了才会触发拒绝策略。
- **任务队列**：继承`BlockingQueue`实现一个混合式阻塞队列，该队列包含`JDK`自带的`ArrayBlockingQueue`。另外，该混合式阻塞队列需要修改取任务处理的逻辑，也就是重写`take()`方法，取任务时优先从数据库中读取最早的任务，数据库中无任务时再从 `ArrayBlockingQueue`中去取任务。

## 线程池阻塞队列
新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，新任务就会被存放在队列中。

- 容量为 `Integer.MAX_VALUE` 的 `LinkedBlockingQueue`（无界队列）：`FixedThreadPool` 和 `SingleThreadExector` 。`FixedThreadPool`最多只能创建核心线程数的线程（核心线程数和最大线程数相等），`SingleThreadExector`只能创建一个线程（核心线程数和最大线程数都是 1），二者的任务队列永远不会被放满。
- `SynchronousQueue`（同步队列）：`CachedThreadPool` 。`SynchronousQueue` 没有容量，不存储元素，目的是保证对于提交的任务，如果有空闲线程，则使用空闲线程来处理；否则新建一个线程来处理任务。也就是说，`CachedThreadPool` 的最大线程数是 `Integer.MAX_VALUE` ，可以理解为线程数是可以无限扩展的，可能会创建大量线程，从而导致 OOM。
- `DelayedWorkQueue`（延迟阻塞队列）：`ScheduledThreadPool` 和 `SingleThreadScheduledExecutor` 。`DelayedWorkQueue` 的内部元素并不是按照放入的时间排序，而是会按照延迟的时间长短对任务进行排序，内部采用的是“堆”的数据结构，可以保证每次出队的任务都是当前队列中执行时间最靠前的。`DelayedWorkQueue` 添加元素满了之后会自动扩容原来容量的 1/2，即永远不会阻塞，最大扩容可达 `Integer.MAX_VALUE`，所以最多只能创建核心线程数的线程。
## 处理任务流程
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403051648891.png)
- 如果当前运行的线程数小于核心线程数，那么就会新建一个线程来执行任务。
- 如果当前运行的线程数等于或大于核心线程数，但是小于最大线程数，那么就把该任务放入到任务队列里等待执行。
- 如果向任务队列投放任务失败（任务队列已经满了），但是当前运行的线程数是小于最大线程数的，就新建一个线程来执行任务。
- 如果当前运行的线程数已经等同于最大线程数了，新建线程将会使当前运行的线程超出最大线程数，那么当前任务会被拒绝，饱和策略会调用`RejectedExecutionHandler.rejectedExecution()`方法。


[Java 线程池详解 | JavaGuide](https://javaguide.cn/java/concurrent/java-thread-pool-summary.html)
# 阻塞队列(生产者/消费者模型)
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403051635350.png)
>poll和peek返回空来表示失败，因此，不能向队列中插入null

| 队列                                             | 描述                      | 使用的线程池                                                  |
| ---------------------------------------------- | ----------------------- | ------------------------------------------------------- |
| LinkedBlockingQueue                            | 无上边界，链表实现               | `FixedThreadPool` 和 `SingleThreadExector`               |
| ArrayBlockingQueue(int capacity, boolean fair) | 构建指定容量和公平性设置(可选)，循环数组实现 |                                                         |
| PriorityBlockingQueue                          | 阻塞优先队列                  |                                                         |
| DelayQueue                                     | 无界，延迟超过指定时间的元素可以移出      | `ScheduledThreadPool` 和 `SingleThreadScheduledExecutor` |
| SynchronousQueue                               | 没有容量，不存储元素              | `CachedThreadPool`                                      |


# Future
