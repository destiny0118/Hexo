
# 单例模式
**双重校验锁实现对象单例（线程安全）**：
>volatile禁止指令重排序
>- 分配内存
>- 对象初始化
>- 设置对象引用
```java
public class Singleton {

    private volatile static Singleton uniqueInstance;

    private Singleton() {
    }

    public  static Singleton getUniqueInstance() {
       //先判断对象是否已经实例过，没有实例化过才进入加锁代码
        if (uniqueInstance == null) {
            //类对象加锁
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

`uniqueInstance` 采用 `volatile` 关键字修饰也是很有必要的， `uniqueInstance = new Singleton();` 这段代码其实是分为三步执行：

1. 为 `uniqueInstance` 分配内存空间
2. 初始化 `uniqueInstance`
3. 将 `uniqueInstance` 指向分配的内存地址




# 模拟死锁
## 线程死锁
```java
public class DeadLock {
    private static Object a=new Object();
    private static Object b=new Object();

    public static void main(String[] args){
        new Thread(()->{
            synchronized (a){
                System.out.println(Thread.currentThread()+ "获得资源a");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                System.out.println(Thread.currentThread()+"请求资源b");
                synchronized (b){
                    System.out.println(Thread.currentThread()+ "获得资源b");
                }
            }
        },"线程1").start();
        new Thread(()->{
            synchronized (b){
                System.out.println(Thread.currentThread()+ "获得资源b");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                System.out.println(Thread.currentThread()+"请求资源a");
                synchronized (a){
                    System.out.println(Thread.currentThread()+ "获得资源a");
                }
            }
        },"线程2").start();
    }
}
```
## 数据库事务死锁
```SQL
-- 事务1
begin;
-- SQL1更新id为1的
update user set age = 1 where id = 1;
-- SQL2更新id为2的
update user set age = 2 where id = 2;
commit;


-- 事务2
begin;
-- SQL1更新id为2的
update user set age = 3 where id = 2;
-- SQL2更新id为1的
update user set age = 4 where id = 1;
commit;
```

[面试官：请用SQL模拟一个死锁 - 问北 - 博客园 (cnblogs.com)](https://www.cnblogs.com/ibigboy/p/16202718.html)

# 生产者、消费者模型
```java
// 生产者
class Producer implements Runnable {
    private BlockingQueue<Integer> queue;

    public Producer(BlockingQueue<Integer> queue) {
        this.queue = queue;
    }
    
    public void run() {
        try {
            while (true) {
                int value = produce(); // 生产数据
                queue.put(value); // 将数据放入队列
                Thread.sleep(1000); // 模拟生产过程
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    
    private int produce() {
        // 生产过程
        return 1;
    }
}

// 消费者
class Consumer implements Runnable {
    private BlockingQueue<Integer> queue;

    public Consumer(BlockingQueue<Integer> queue) {
        this.queue = queue;
    }
    
    public void run() {
        try {
            while (true) {
                int value = queue.take(); // 从队列中取出数据
                consume(value); // 消费数据
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    
    private void consume(int value) {
        // 消费过程
    }
}
```