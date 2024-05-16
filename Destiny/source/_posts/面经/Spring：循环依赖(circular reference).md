# 循环依赖方式
## 构造参数循环依赖
```java
public class A{
	private  B b;
	public void A(B b){
		this.b=b;
	}
}

public class B{
	private  A a;
	public void B(A a){
		this.a=a;
	}
}
```

```xml
<bean id="a" class="...A">
	<constructor-arg index="0" ref="b"></constructor-arg>
</bean>

<bean id="b" class="...B">
	<constructor-arg index="0" ref="a"></constructor-arg>
</bean>
```

Spring先创建(singleton) A，A依赖B，将A放进当前创建Bean池中；
然后创建B，B依赖A，将B放进当前创建Bean池中，然后发现A已经在池中。

## setter方式（单例，singleton），默认方式
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202401111055058.png)

Spring是先将Bean对象实例化之后再设置对象属性的
## setter方式（原型，prototype）
>scope="prototype"，每次请求都会创建一个实例对象






# Spring源码

```java
```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {

	/** Maximum number of suppressed exceptions to preserve. */
	private static final int SUPPRESSED_EXCEPTIONS_LIMIT = 100;


	/** Cache of singleton objects: bean name to bean instance. */
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

	/** Cache of singleton factories: bean name to ObjectFactory. */
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

	/** Cache of early singleton objects: bean name to bean instance. */
	private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);

	protected Object getSingleton(String beanName, boolean allowEarlyReference) {  
	// 从 singletonObjects 获取实例，singletonObjects 中缓存的实例都是完全实例化好的 bean，可以直接使用
    Object singletonObject = this.singletonObjects.get(beanName);  
    if (singletonObject == null && this.isSingletonCurrentlyInCreation(beanName)) {  
        synchronized(this.singletonObjects) {  
            singletonObject = this.earlySingletonObjects.get(beanName);  
            if (singletonObject == null && allowEarlyReference) {  
                ObjectFactory<?> singletonFactory = (ObjectFactory)this.singletonFactories.get(beanName);  
                if (singletonFactory != null) {  
	                // 加入到三级缓存，暴漏早期对象用于解决循环依赖
                    singletonObject = singletonFactory.getObject();  
                    this.earlySingletonObjects.put(beanName, singletonObject);  
                    this.singletonFactories.remove(beanName);  
                }  
            }  
        }  
    }  
  
    return singletonObject != NULL_OBJECT ? singletonObject : null;  
}


}
```
- singletonObjects：用于存放初始化好的 bean 实例，存放完整对象。
- earlySingletonObjects，用于存放初始化中的 bean，来解决循环依赖。存放半成品对象，属性还未赋值的对象。
- singletonFactories：用于存放 bean 工厂，bean 工厂所生成的 bean 还没有完成初始化 bean。存放的是 `ObjectFactory<?>` 类型的 lambda 表达式，就是这用于处理 AOP 循环依赖的。

相互引用的bean，A依赖B，把A原始对象包装成SingletonFactory 放入三级缓存
B依赖A，B依赖的A是从singletonFactories获取bean工厂调用getObject方法生产bean放入earlySingletonObjects。
# 参考
1. [面试必问：Spring 循环依赖的三种方式 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/89412539#:~:text=%E9%9D%A2%E8%AF%95%E5%BF%85%E9%97%AE%EF%BC%9ASpring%20%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96%E7%9A%84%E4%B8%89%E7%A7%8D%E6%96%B9%E5%BC%8F%201%20%E7%AC%AC%E4%B8%80%E7%A7%8D%EF%BC%9A%E6%9E%84%E9%80%A0%E5%99%A8%E5%8F%82%E6%95%B0%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96,2%20%E7%AC%AC%E4%BA%8C%E7%A7%8D%EF%BC%9Asetter%E6%96%B9%E5%BC%8F%E5%8D%95%E4%BE%8B%EF%BC%8C%E9%BB%98%E8%AE%A4%E6%96%B9%E5%BC%8F%203%20%E7%AC%AC%E4%B8%89%E7%A7%8D%EF%BC%9Asetter%E6%96%B9%E5%BC%8F%E5%8E%9F%E5%9E%8B%EF%BC%8Cprototype)
2. [面经手册 · 第31篇《Spring Bean IOC、AOP 循环依赖解读》 | 小傅哥 bugstack 虫洞栈](https://bugstack.cn/md/java/interview/2021-05-05-%E9%9D%A2%E7%BB%8F%E6%89%8B%E5%86%8C%20%C2%B7%20%E7%AC%AC31%E7%AF%87%E3%80%8ASpring%20Bean%20IOC%E3%80%81AOP%20%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96%E8%A7%A3%E8%AF%BB%E3%80%8B.html)
3. [Spring 循环依赖那些事儿（含Spring详细流程图） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/635659531)
4. [什么是循环依赖以及解决方式-CSDN博客](https://blog.csdn.net/bingguang1993/article/details/88915576)
5. [Spring Bean 循环依赖 - spring 中文网 (springdoc.cn)](https://springdoc.cn/spring-bean-circular-dependencies/)(*****)
6. [Spring源码最难问题《当Spring AOP遇上循环依赖》_循环依赖aop在那个阶段-CSDN博客](https://blog.csdn.net/chaitoudaren/article/details/105060882)
7. 