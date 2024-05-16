
## 反射机制
>应用场景：动态代理、注解

赋予了我们在运行时分析类以及执行类中方法的能力,通过反射你可以获取任意一个类的所有属性和方法，你还可以调用这些方法和属性。

获取Class对象
> 一个类的对象表示，一个Class对象表示一个特定类的属性

```java
//TargteObject表示一个类
1. 知道具体类
	Class c=TargetObject.class;
2. 通过 Class.forName()传入类的全路径获取：
	Class c=Class.forName("cn.javaguide.TargetObject");
3. 通过对象实例获取
	TargetObject o = new TargetObject();
	Class c = o.getClass();
4.  通过类加载器xxxClassLoader.loadClass()传入类路径获取:
	ClassLoader.getSystemClassLoader().loadClass("cn.javaguide.TargetObject");
```

- c.getDeclaredConstructors()
	- getParameterTypes()
- c.getDeclaredMethods()
- c.getDeclaredFields()

```java
类名.方法名
方法名.invoke(类名)
```
# 注解
`Annotation` （注解） 是 Java5 开始引入的新特性，可以看作是一种特殊的注释，主要用于修饰类、方法或者变量，提供某些信息供程序在编译或者运行时使用。

注解本质是一个继承了`Annotation` 的特殊接口：

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {

}

public interface Override extends Annotation{

}
```

JDK 提供了很多内置的注解（比如 `@Override`、`@Deprecated`），同时，我们还可以自定义注解。


注解只有被解析之后才会生效，常见的解析方法有两种：

- **编译期直接扫描**：编译器在编译 Java 代码的时候扫描对应的注解并处理，比如某个方法使用`@Override` 注解，编译器在编译的时候就会检测当前的方法是否重写了父类对应的方法。
- **运行期通过反射处理**：像框架中自带的注解(比如 Spring 框架的 `@Value`、`@Component`)都是通过反射来进行处理的。

# 代理
## 动态代理(proxy)
>为指定的接口在系统运行期间动态地生成代理对象
>动态代理机制的实现主要由一个类和一个接口组成， 即java. lang. reflect . Proxy类和java .lang. reflect.InvocationH.andler接口

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403301619023.png)

```java
public interface Animal {  
    void info();  
}

public class Dog implements Animal{  
    @Override  
    public void info() {  
        System.out.println("Animal Dog");  
    }  
}

public class AnimalInvocationHandler implements InvocationHandler {  
    private Object target;  
    public AnimalInvocationHandler(Object target){  
        this.target=target;  
    }  
    @Override  
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {  
	    if(method.getName().equals("info")){
	        return method.invoke(target,args);  
        }
    }  
}

public class AgentTest {  
    @Test  
    public void test_proxy(){  
        ClassLoader classLoader = Thread.currentThread().getContextClassLoader();  
  
        Animal animal=(Animal) Proxy.newProxyInstance(classLoader,new Class[]{Animal.class},new AnimalInvocationHandler(new Dog()));  
        animal.info();  
    }
}
```
当Proxy动态生成的代理对象上相应的接口方法被调用时，对应的Invocat ionHandler就会拦截相应的方法调用，并进行相应处理。InvocationHandler就是我们实现横切逻辑的地方，它是横切逻辑的载体，作用跟Advice是一样
的。
## CGLIB（动态字节码生成, Code Generation Library）
>动态字节码生成技术扩展对象行为：对目标对象进行继承扩展，为其生成相应的子类，而子类可以通过覆写来扩展父类的行为，只要将横切逻辑的实现放到子类中，然后让系统使用扩展后的目标对象的子类，就可以达到与代理模式相同的效果。
>CGLIB可以对实现了某种接口的类，或者没有实现任何接口的类进行扩

```java
public class Requestable {  
    public void request(){  
        System.out.println("In class Requestable, method request");  
    }  
}

public class RequestCallback implements MethodInterceptor{  
    public Object newInstall(Object object) {  
        return Enhancer.create(object.getClass(), this);  
    }  
    @Override  
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {  
        if(method.getName().equals("request")){  
            System.out.println("Before request");  
            return methodProxy.invokeSuper(o,objects);  
        }  
        return null;  
    }  
}

public class AgentTest {
	@Test  
	public void test_cglib(){  
		//通过CGLIB的Enhancer为目标对象动态地生成一个子类
	    Enhancer enhancer=new Enhancer();  
	    enhancer.setSuperclass(Requestable.class);  
	    enhancer.setCallback(new RequestCallback());  
		//通过为enhancer指定需要生成的子类对应的父类，以及Callback实现
		//enhancer最终为我们生成了需要的代理对象实例。
	    Requestable proxy=(Requestable) enhancer.create();  
	    proxy.request();  
	  
	    RequestCallback requestCallback=new RequestCallback();  
	    Requestable requestable=(Requestable) requestCallback.newInstall(new Requestable());  
	    requestable.request();  
	}
}
```
- 无法对final方法进行覆写
[【Java】代理模式（Proxy模式）详解_java proxy-CSDN博客](https://blog.csdn.net/Passer_hua/article/details/122617628)


## 依赖注入


# 序列化
如果我们需要持久化 Java 对象比如将 Java 对象保存在文件中，或者在网络传输 Java 对象，这些场景都需要用到序列化。

简单来说：

- **序列化**：将数据结构或对象转换成二进制字节流的过程
- **反序列化**：将在序列化过程中所生成的二进制字节流转换成数据结构或者对象的过程

对于 Java 这种面向对象编程语言来说，我们序列化的都是对象（Object）也就是实例化后的类(Class)，但是在 C++这种半面向对象的语言中，struct(结构体)定义的是数据结构类型，而 class 对应的是对象类型。

下面是序列化和反序列化常见应用场景：

- 对象在进行网络传输（比如远程方法调用 RPC 的时候）之前需要先被序列化，接收到序列化的对象之后需要再进行反序列化；
- 将对象存储到文件之前需要进行序列化，将对象从文件中读取出来需要进行反序列化；
- 将对象存储到数据库（如 Redis）之前需要用到序列化，将对象从缓存数据库中读取出来需要反序列化；
- 将对象存储到内存之前需要进行序列化，从内存中读取出来之后需要进行反序列化。


# Spring
## 代理Bean注册到Spring容器
类的调用是不能直接调用没有实现的接口的，所以需要通过==代理==的方式给接口生成对应的实现类。接下来再通过把代理类放到 Spring 的 FactoryBean 的实现中，最后再把这个 FactoryBean 实现类注册到 Spring 容器。那么现在你的代理类就已经被注册到 Spring 容器了，接下来就可以通过注解的方式注入到属性中。


[面经手册 · 第28篇《你说，怎么把Bean塞到Spring容器？》 | 小傅哥 bugstack 虫洞栈](https://bugstack.cn/md/java/interview/2021-03-30-%E9%9D%A2%E7%BB%8F%E6%89%8B%E5%86%8C%20%C2%B7%20%E7%AC%AC28%E7%AF%87%E3%80%8A%E4%BD%A0%E8%AF%B4%EF%BC%8C%E6%80%8E%E4%B9%88%E6%8A%8ABean%E5%A1%9E%E5%88%B0Spring%E5%AE%B9%E5%99%A8%E3%80%8B.html)






spring-framework\spring-beans\src\main\java\org\springframework\beans\factory\support\InstantiationStrategy：实例化策略抽象接口
spring-framework\spring-beans\src\main\java\org\springframework\beans\factory\support\SimpleInstantiationStrategy.java：简单的对象实例化功能


# 参考
[Java 反射机制详解 | JavaGuide(Java面试+学习指南)](https://javaguide.cn/java/basis/reflection.html#%E5%8F%8D%E5%B0%84%E7%9A%84%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF%E4%BA%86%E8%A7%A3%E4%B9%88)