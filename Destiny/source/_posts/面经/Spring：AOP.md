>AOP(aspect-oriented programming)，即面向切面编程
>Aspect是一种新的模块化机制，用来描述分散在对象、类或函数中的横切关注点（crosscutting concern）。从关注点中分离出横切关注点是面向切面的程序设计的核心概念。**分离关注点使解决特定领域问题的代码从业务逻辑中独立出来，业务逻辑的代码中不再含有针对特定领域问题代码的调用，业务逻辑同特定领域问题的关系通过切面来封装、维护**，这样原本分散在整个应用程序中的变动就可以很好地管理起来。



# 名词概念

|术语|概念|
|---|---|
|`Aspect`|切面是`Pointcut`和`Advice`的集合，一般单独作为一个类。`Pointcut`和`Advice`共同定义了关于切面的全部内容，它是**什么时候，在何时和何处**完成功能。|
|`Joinpoint`|这表示你的应用程序中可以插入AOP方面的一点。也可以说，这是应用程序中使用Spring AOP框架采取操作的实际位置。|
|`Advice`|这是在方法执行之前或之后采取的实际操作。 这是在Spring AOP框架的程序执行期间调用的实际代码片段。|
|`Pointcut`|这是一组一个或多个切入点，在切点应该执行`Advice`。 您可以使用表达式或模式指定切入点，后面示例会提到。|
|`Introduction`|引用允许我们向现有的类添加新的方法或者属性|
|`Weaving`|创建一个被增强对象的过程。这可以在编译时完成（例如使用AspectJ编译器），也可以在运行时完成。Spring和其他纯Java AOP框架一样，在运行时完成织入。|

# AOP实现机制
>动态代理机制和字节码生成都是在运行期间为目标对象生成一个代理对象，而将横切逻辑织入到这个代理对象中，系统最终使用的是织入了横切逻辑的代理对象，而不是真正的目标对象。
1. 动态代理
2. 动态字节码增强(CGLIB, Code Generation Library)
	CGLIB可以对实现了某种接口的类，或者没有实现任何接口的类进行扩展
1. Java代码生成

# 概念
- joinPoint
- pointcut
- advice



## 动态代理
动态代理（Dynamic Proxy）机制，可以在运行期间，为相应的接口（interface）动态生成对应的代理对象。

## 动态字节码增强
CGLIB可以对实现了某种接口的类，或者没有实现任何接口的类进行扩展。



```java
public Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) throws BeansException {  
    Object cacheKey = this.getCacheKey(beanClass, beanName);  
    if (beanName == null || !this.targetSourcedBeans.contains(beanName)) {  
        if (this.advisedBeans.containsKey(cacheKey)) {  
            return null;  
        }  
  
        if (this.isInfrastructureClass(beanClass) || this.shouldSkip(beanClass, beanName)) {  
            this.advisedBeans.put(cacheKey, Boolean.FALSE);  
            return null;        }  
    }  
  
    if (beanName != null) {  
        TargetSource targetSource = this.getCustomTargetSource(beanClass, beanName);  
        if (targetSource != null) {  
            this.targetSourcedBeans.add(beanName);  
            Object[] specificInterceptors = this.getAdvicesAndAdvisorsForBean(beanClass, beanName, targetSource);  
            Object proxy = this.createProxy(beanClass, beanName, specificInterceptors, targetSource);  
            this.proxyTypes.put(cacheKey, proxy.getClass());  
            return proxy;  
        }  
    }  
  
    return null;  
}
```


```java
protected boolean shouldSkip(Class<?> beanClass, String beanName) {  
	//获取切面信息
    List<Advisor> candidateAdvisors = this.findCandidateAdvisors();  
    Iterator var4 = candidateAdvisors.iterator();  
  
    Advisor advisor;  
    do {  
        if (!var4.hasNext()) {  
            return super.shouldSkip(beanClass, beanName);  
        }  
  
        advisor = (Advisor)var4.next();  
    } while(!(advisor instanceof AspectJPointcutAdvisor) || !((AbstractAspectJAdvice)advisor.getAdvice()).getAspectName().equals(beanName));  
  
    return true;}
```

```java
protected List<Advisor> findCandidateAdvisors() {  
    List<Advisor> advisors = super.findCandidateAdvisors();  
    advisors.addAll(this.aspectJAdvisorsBuilder.buildAspectJAdvisors());  
    return advisors;  
}
```


```java
public List<Advisor> buildAspectJAdvisors() {  
    List<String> aspectNames = this.aspectBeanNames;  
    if (aspectNames == null) {  
        synchronized(this) {  
            aspectNames = this.aspectBeanNames;  
            if (aspectNames == null) {  
                List<Advisor> advisors = new LinkedList();  
                List<String> aspectNames = new LinkedList();  
                
                String[] beanNames = BeanFactoryUtils.beanNamesForTypeIncludingAncestors(this.beanFactory, Object.class, true, false);  
                String[] var18 = beanNames;  
                int var19 = beanNames.length;  
  
                for(int var7 = 0; var7 < var19; ++var7) {  
                    String beanName = var18[var7];  
                    if (this.isEligibleBean(beanName)) {  
                        Class<?> beanType = this.beanFactory.getType(beanName);  
                        //判断是否为切面
                        if (beanType != null && this.advisorFactory.isAspect(beanType)) {  
                            aspectNames.add(beanName);  
                            AspectMetadata amd = new AspectMetadata(beanType, beanName);  
                            if (amd.getAjType().getPerClause().getKind() == PerClauseKind.SINGLETON) {  
                                MetadataAwareAspectInstanceFactory factory = new BeanFactoryAspectInstanceFactory(this.beanFactory, beanName);  
                                //获取所有的切面列表
                                List<Advisor> classAdvisors = this.advisorFactory.getAdvisors(factory);  
                                if (this.beanFactory.isSingleton(beanName)) {  
                                    this.advisorsCache.put(beanName, classAdvisors);  
                                } else {  
                                    this.aspectFactoryCache.put(beanName, factory);  
                                }  
  
                                advisors.addAll(classAdvisors);  
                            } else {  
                                if (this.beanFactory.isSingleton(beanName)) {  
                                    throw new IllegalArgumentException("Bean with name '" + beanName + "' is a singleton, but aspect instantiation model is not singleton");  
                                }  
  
                                MetadataAwareAspectInstanceFactory factory = new PrototypeAspectInstanceFactory(this.beanFactory, beanName);  
                                this.aspectFactoryCache.put(beanName, factory);  
                                advisors.addAll(this.advisorFactory.getAdvisors(factory));  
                            }  
                        }  
                    }  
                }  
  
                this.aspectBeanNames = aspectNames;  
                return advisors;  
            }  
        }  
    }
```


```java
public Advisor getAdvisor(Method candidateAdviceMethod, MetadataAwareAspectInstanceFactory aspectInstanceFactory, int declarationOrderInAspect, String aspectName) {  
    this.validate(aspectInstanceFactory.getAspectMetadata().getAspectClass());  
    AspectJExpressionPointcut expressionPointcut = this.getPointcut(candidateAdviceMethod, aspectInstanceFactory.getAspectMetadata().getAspectClass());  
    return expressionPointcut == null ? null : new InstantiationModelAwarePointcutAdvisorImpl(expressionPointcut, candidateAdviceMethod, this, aspectInstanceFactory, declarationOrderInAspect, aspectName);  
}
```

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403132049981.png)


# 参考
[Spring AOP - 注解方式使用介绍（长文详解） - 掘金 (juejin.cn)](https://juejin.cn/post/6844903987062243341)
[深度剖析Spring AOP源码，图文详解，小白也能看明白。-CSDN博客](https://blog.csdn.net/ww2651071028/article/details/129486012)
[动态代理异常com.sun.proxy.$Proxy cannot be cast to-CSDN博客](https://blog.csdn.net/hys__handsome/article/details/121011807)