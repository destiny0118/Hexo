>在Spring中用BeanDefinition来代表一个Spring对象的定义。因此有BeanDefinitionBuilder、BeanDefinitionParser这些常见工具类。简单来说，整个Spring容器也就是读取文件、构造BeanDefinition对象、实例化成Bean的过程。BeanDefinition也可以认为是一个Bean元数据的容器，包含了一个bean实例化和初始化所需要的各种参数，比如class、name、scope、properties等等。

```java
public BeanDefinition getBeanDefinition(String beanName) throws NoSuchBeanDefinitionException {  
    BeanDefinition bd = (BeanDefinition)this.beanDefinitionMap.get(beanName);  
    if (bd == null) {  
        if (this.logger.isTraceEnabled()) {  
            this.logger.trace("No bean named '" + beanName + "' found in " + this);  
        }  
  
        throw new NoSuchBeanDefinitionException(beanName);  
    } else {  
        return bd;  
    }  
}
```



```java
//beanname(child)
//bd(child BeanDefinition)
protected RootBeanDefinition getMergedBeanDefinition(String beanName, BeanDefinition bd) throws BeanDefinitionStoreException {  
    return this.getMergedBeanDefinition(beanName, bd, (BeanDefinition)null);  
}
```


```java
protected RootBeanDefinition getMergedBeanDefinition(String beanName, BeanDefinition bd, BeanDefinition containingBd) throws BeanDefinitionStoreException {  
    synchronized(this.mergedBeanDefinitions) {  
        RootBeanDefinition mbd = null;  
        if (containingBd == null) {  
            mbd = (RootBeanDefinition)this.mergedBeanDefinitions.get(beanName);  
        }  
  
        if (mbd == null) {  
            if (bd.getParentName() == null) {  
                if (bd instanceof RootBeanDefinition) {  
                    mbd = ((RootBeanDefinition)bd).cloneBeanDefinition();  
                } else {  
                    mbd = new RootBeanDefinition(bd);  
                }  
            } else {  
                BeanDefinition pbd;  
                try {  
	                //得到parent BeanName
                    String parentBeanName = this.transformedBeanName(bd.getParentName());  
                    if (!beanName.equals(parentBeanName)) {  
	                    //parent(BeanDefinition
	                    )
                        pbd = this.getMergedBeanDefinition(parentBeanName);  
                    } else {  
                        BeanFactory parent = this.getParentBeanFactory();  
                        if (!(parent instanceof ConfigurableBeanFactory)) {  
                            throw new NoSuchBeanDefinitionException(parentBeanName, "Parent name '" + parentBeanName + "' is equal to bean name '" + beanName + "': cannot be resolved without an AbstractBeanFactory parent");  
                        }  
  
                        pbd = ((ConfigurableBeanFactory)parent).getMergedBeanDefinition(parentBeanName);  
                    }  
                } catch (NoSuchBeanDefinitionException var10) {  
                    throw new BeanDefinitionStoreException(bd.getResourceDescription(), beanName, "Could not resolve parent bean definition '" + bd.getParentName() + "'", var10);  
                }  
  
                mbd = new RootBeanDefinition(pbd);  
                mbd.overrideFrom(bd);  
            }  
  
            if (!StringUtils.hasLength(mbd.getScope())) {  
                mbd.setScope("singleton");  
            }  
  
            if (containingBd != null && !containingBd.isSingleton() && mbd.isSingleton()) {  
                mbd.setScope(containingBd.getScope());  
            }  
  
            if (containingBd == null && this.isCacheBeanMetadata()) {  
                this.mergedBeanDefinitions.put(beanName, mbd);  
            }  
        }  
  
        return mbd;  
    }  
}
```



# 参考
1. [什么是MergedBeanDefinition?-CSDN博客](https://blog.csdn.net/m0_37607945/article/details/107411096)