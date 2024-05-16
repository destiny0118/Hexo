---
title: Spring源码：getBean流程
categories: 源码分析
date: 2024-1-10
top: 9
description: getBean的流程
---
![spring-获取bean.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403132110955.png)

# doGetBean
- transformedBeanName，处理别名BeanName、处理带&符的工厂BeanName。
- getSingleton，先尝试从缓存中获取Bean实例，这个位置就是三级缓存解决循环依赖的方法。
- getObjectForBeanInstance，如果 sharedInstance 是普通的 Bean 实例，则下面的方法会直接返回。另外 sharedInstance 是工厂Bean类型，则需要获取 getObject 方法，可以参考关于 FactoryBean 的实现类。
- isPrototypeCurrentlyInCreation，循环依赖有三种，setter注入、多实例和构造函数，Spring 只能解决 setter 注入，所以这里是 Prototype 则会抛出异常。
- getParentBeanFactory，父 bean 工厂存在，当前 bean 不存在于当前bean工厂，则到父工厂查找 bean 实例。
- originalBeanName，获取 name 对应的 beanName，如果 name 是以 & 开头，则返回 & + beanName
- args != null，根据 args 参数是否为空，调用不同的父容器方法获取 bean 实例
- !typeCheckOnly，typeCheckOnly，用于判断调用 getBean 方法时，是否仅是做类型检查，如果不是只做类型检查，就会调用 markBeanAsCreated 进行记录
- mbd.getDependsOn，处理使用了 depends-on 注解的依赖创建 bean 实例
- isDependent，监测是否存在 depends-on 循环依赖，若存在则会抛出异常
- registerDependentBean，注册依赖记录
- getBean(dep)，加载 depends-on 依赖（dep 是 depends-on 缩写）
- mbd.isSingleton()，创建单例 bean 实例
- mbd.isPrototype()，创建其他类型的 bean 实例
- return (T) bean，返回 Bean 实例
```java
	/**
	 * Return an instance, which may be shared or independent, of the specified bean.
	 * @param name the name of the bean to retrieve
	 * @param requiredType the required type of the bean to retrieve
	 * @param args arguments to use when creating a bean instance using explicit arguments
	 * (only applied when creating a new instance as opposed to retrieving an existing one)
	 * @param typeCheckOnly whether the instance is obtained for a type check,
	 * not for actual use
	 * @return an instance of the bean
	 * @throws BeansException if the bean could not be created
	 */
	protected <T> T doGetBean(
			String name, @Nullable Class<T> requiredType, @Nullable Object[] args, boolean typeCheckOnly)
			throws BeansException {
		//处理别名name，获取规范名beanName
		//处理带&符的工厂(FactoryBean)beanName
		String beanName = transformedBeanName(name);
		Object beanInstance;

		// Eagerly check singleton cache for manually registered singletons.
		//从缓存中获取Bean实例，三级缓存解决循环依赖(singletonObjects, earlySingletonObjects, singletonFactories)
		Object sharedInstance = getSingleton(beanName);
		//args 传参其实是 null 的，但是如果 args 不为空的时候，那么意味着调用方不是希望获取 Bean，而是创建 Bean
		if (sharedInstance != null && args == null) {
			//1. sharedInstance是普通的bean实例，直接返回
			//2. sharedInstance是FactoryBean类型，则通过FactoryBean实现类的getObject方法获取
			beanInstance = getObjectForBeanInstance(sharedInstance, name, beanName, null);
		}

		else {
			// Fail if we're already creating this bean instance:
			// We're assumably within a circular reference.
			//循环依赖有三种：setter注入(singleton)，setter注入(prototype)，构造函数
			//spring解决setter注入(singleton)，提前暴露创建中的bean
			if (isPrototypeCurrentlyInCreation(beanName)) {
				throw new BeanCurrentlyInCreationException(beanName);
			}

			// Check if bean definition exists in this factory.
			//1. 父bean工厂存在
			//2. beanName不存在于当前Bean工厂，则到父工厂查找bean实例
			BeanFactory parentBeanFactory = getParentBeanFactory();
			if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
				// Not found -> check parent.
				// 获取 name 对应的 beanName，如果 name 是以 & 开头，则返回 & + beanName
				String nameToLookup = originalBeanName(name);
				if (parentBeanFactory instanceof AbstractBeanFactory abf) {
					return abf.doGetBean(nameToLookup, requiredType, args, typeCheckOnly);
				}
				else if (args != null) {
					// Delegation to parent with explicit args.
					return (T) parentBeanFactory.getBean(nameToLookup, args);
				}
				else if (requiredType != null) {
					// No args -> delegate to standard getBean method.
					return parentBeanFactory.getBean(nameToLookup, requiredType);
				}
				else {
					return (T) parentBeanFactory.getBean(nameToLookup);
				}
			}


			// 1. typeCheckOnly，用于判断调用 getBean 方法时，是否仅是做类型检查
        	// 2. 如果不是只做类型检查，就会调用 markBeanAsCreated 进行记录
			if (!typeCheckOnly) {
				markBeanAsCreated(beanName);
			}


			//创建bean
			//singleton：容器中还没有创建过此bean
			//prototype：每次调用都要创建一个新的bean
			try {
				if (requiredType != null) {
					beanCreation.tag("beanType", requiredType::toString);
				}


				RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
				// 检查当前创建的 bean 定义是否为抽象 bean 定义
				checkMergedBeanDefinition(mbd, beanName, args);

				// Guarantee initialization of beans that the current bean depends on.
				// 处理使用了 depends-on 注解的依赖创建 bean 实例
				String[] dependsOn = mbd.getDependsOn();
				if (dependsOn != null) {
					for (String dep : dependsOn) {
						//检测是否存在depends-on循环依赖
						if (isDependent(beanName, dep)) {
							throw new BeanCreationException(mbd.getResourceDescription(), beanName,
									"Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
						}
						//注册依赖记录
						registerDependentBean(dep, beanName);
						try {
							getBean(dep);
						}
						catch (NoSuchBeanDefinitionException ex) {
							throw new BeanCreationException(mbd.getResourceDescription(), beanName,
									"'" + beanName + "' depends on missing bean '" + dep + "'", ex);
						}
					}
				}

				// Create bean instance.
				//创建bean实例(singleton)
				if (mbd.isSingleton()) {

					// 把 beanName 和 new ObjectFactory 匿名内部类传入回调
					sharedInstance = getSingleton(beanName, () -> {
						try {
							return createBean(beanName, mbd, args);
						}
						catch (BeansException ex) {
							// Explicitly remove instance from singleton cache: It might have been put there
							// eagerly by the creation process, to allow for circular reference resolution.
							// Also remove any beans that received a temporary reference to the bean.
							destroySingleton(beanName);
							throw ex;
						}
					});
					beanInstance = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
				}
				//创建bean实例(prototype)
				else if (mbd.isPrototype()) {
					// It's a prototype -> create a new instance.
					Object prototypeInstance = null;
					try {
						beforePrototypeCreation(beanName);
						prototypeInstance = createBean(beanName, mbd, args);
					}
					finally {
						afterPrototypeCreation(beanName);
					}
					beanInstance = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
				}
				//创建bean实例
				else {
					String scopeName = mbd.getScope();
					Scope scope = this.scopes.get(scopeName);
					try {
						Object scopedInstance = scope.get(beanName, () -> {
							beforePrototypeCreation(beanName);
							try {
								return createBean(beanName, mbd, args);
							}
							finally {
								afterPrototypeCreation(beanName);
							}
						});
						beanInstance = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
					}
					catch (IllegalStateException ex) {
						throw new ScopeNotActiveException(beanName, scopeName, ex);
					}
				}
			}
			catch (BeansException ex) {
				cleanupAfterBeanCreationFailure(beanName);
				throw ex;
			}
		}

		// 如果需要类型转换，这里会进行操作
		if (requiredType != null && bean != null && !requiredType.isInstance(bean)) {
			try {
				return getTypeConverter().convertIfNecessary(bean, requiredType);
			}
			catch (TypeMismatchException ex) {
				if (logger.isDebugEnabled()) {
					logger.debug("Failed to convert bean '" + name + "' to required type '" +
							ClassUtils.getQualifiedName(requiredType) + "'", ex);
				}
				throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
			}
		}    
	
	    // 返回 Bean
		return (T) bean;
	}
```


# transformedBeanName
```java
 protected String transformedBeanName(String name) {
        return this.canonicalName(BeanFactoryUtils.transformedBeanName(name));
}


//处理FactoryBean的&标记
public static String transformedBeanName(String name) {  
    Assert.notNull(name, "'name' must not be null");  
  
    String beanName;  
    for(beanName = name; beanName.startsWith("&"); beanName = beanName.substring("&".length())) {  
    }  
  
    return beanName;  
}

//处理别名
public String canonicalName(String name) {  
    String canonicalName = name;  
  
    String resolvedName;  
    do {  
        resolvedName = (String)this.aliasMap.get(canonicalName);  
        if (resolvedName != null) {  
            canonicalName = resolvedName;  
        }  
    } while(resolvedName != null);  
  
    return canonicalName;  
}
```
- 使用 FactoryBean 创建出的对象，会在 DefaultListableBeanFactory 初始化的时候，使用 getBean(FACTORY_BEAN_PREFIX + beanName) 给 beanName 加上 & `(String FACTORY_BEAN_PREFIX = "&")`
```java
preInstantiateSingletons(){
	if (this.isFactoryBean(beanName)) {  
    final FactoryBean<?> factory = (FactoryBean)this.getBean("&" + beanName);  
    boolean isEagerInit;  
    if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {  
        isEagerInit = (Boolean)AccessController.doPrivileged(new PrivilegedAction<Boolean>() {  
            public Boolean run() {  
                return ((SmartFactoryBean)factory).isEagerInit();  
            }  
        }, this.getAccessControlContext());  
    } else {  
        isEagerInit = factory instanceof SmartFactoryBean && ((SmartFactoryBean)factory).isEagerInit();  
    }  
  
    if (isEagerInit) {  
        this.getBean(beanName);  
    }  
}
}
```

# getSingleton：从缓存中获取 bean 实例

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
- singletonObjects：用于存放初始化好的 bean 实例。这里的bean是已经创建完成的，该bean经历过**实例化->属性填充->初始化**以及各类的后置处理。
- earlySingletonObjects，用于存放初始化中的 bean，来解决循环依赖。
- singletonFactories，用于存放 bean 工厂，bean 工厂所生成的 bean 还没有完成初始化 bean。
#  depends-on 依赖 Bean
>AbstractBeanFactory -> isDependent(beanName, dep) -> DefaultSingletonBeanRegistry.class

```java
//isDependent(beanName, dep)
protected boolean isDependent(String beanName, String dependentBeanName) {  
    synchronized(this.dependentBeanMap) {  
        return this.isDependent(beanName, dependentBeanName, (Set)null);  
    }  
}  

//transitiveDependency依赖beanName
//如果transitiveDependency被dependentBeanName依赖
//又因为beanName依赖dependentBeanName

//beanName<-transitiveDependency<-dependentBeanName
//beanName->dependentBeanName
private boolean isDependent(String beanName, String dependentBeanName, Set<String> alreadySeen) {  
    if (alreadySeen != null && ((Set)alreadySeen).contains(beanName)) {  
        return false;  
    } else {  
        String canonicalName = this.canonicalName(beanName);  
        Set<String> dependentBeans = (Set)this.dependentBeanMap.get(canonicalName);  
        if (dependentBeans == null) {  
            return false;  
        } else if (dependentBeans.contains(dependentBeanName)) {  
            return true;  
        } else {  
            Iterator var6 = dependentBeans.iterator();  
  
            String transitiveDependency;  
            do {  
                if (!var6.hasNext()) {  
                    return false;  
                }  
  
                transitiveDependency = (String)var6.next();  
                if (alreadySeen == null) {  
                    alreadySeen = new HashSet();  
                }  
  
                ((Set)alreadySeen).add(beanName);  
            } while(!this.isDependent(transitiveDependency, dependentBeanName, (Set)alreadySeen));  
  
            return true;        }  
    }  
}

//this.registerDependentBean(dep, beanName);
public void registerDependentBean(String beanName, String dependentBeanName) {  
    String canonicalName = this.canonicalName(beanName);  
    Object dependenciesForBean;  
    synchronized(this.dependentBeanMap) {  
        dependenciesForBean = (Set)this.dependentBeanMap.get(canonicalName);  
        if (dependenciesForBean == null) {  
            dependenciesForBean = new LinkedHashSet(8);  
            this.dependentBeanMap.put(canonicalName, dependenciesForBean);  
        }  
  
        ((Set)dependenciesForBean).add(dependentBeanName);  
    }  
  
    synchronized(this.dependenciesForBeanMap) {  
        dependenciesForBean = (Set)this.dependenciesForBeanMap.get(dependentBeanName);  
        if (dependenciesForBean == null) {  
            dependenciesForBean = new LinkedHashSet(8);  
            this.dependenciesForBeanMap.put(dependentBeanName, dependenciesForBean);  
        }  
  
        ((Set)dependenciesForBean).add(canonicalName);  
    }  
}
```
dependentBeanMap：依赖bean的map，即key是被依赖的bean。记录一个bean被多少bean依赖；（该bean被多少bean当做成员变量用@Resource、@Autowired修饰）
dependenciesForBeanMap：bean的依赖，即value是被依赖的bean。记录一个bean依赖了多少bean；（通俗点：一个bean里面有多少个@Atuwowired、@Resource）
[java - dependentBeanMap和dependenciesForBeanMap的区别 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000041468353)

# getSingleton：获取单实例bean
```java
if (mbd.isSingleton()) {  
    sharedInstance = this.getSingleton(beanName, new ObjectFactory<Object>() {  
        public Object getObject() throws BeansException {  
            try {  
                return AbstractBeanFactory.this.createBean(beanName, mbd, args);  
            } catch (BeansException var2) {  
                AbstractBeanFactory.this.destroySingleton(beanName);  
                throw var2;  
            }  
        }  
    });  
    bean = this.getObjectForBeanInstance(sharedInstance, name, beanName, mbd);  
}


public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {  
    Assert.notNull(beanName, "'beanName' must not be null");  
    synchronized(this.singletonObjects) {  
        Object singletonObject = this.singletonObjects.get(beanName);  
        if (singletonObject == null) {  
            if (this.singletonsCurrentlyInDestruction) {  
                throw new BeanCreationNotAllowedException();  
            }  
  
            this.beforeSingletonCreation(beanName);  
            boolean newSingleton = false;  
            boolean recordSuppressedExceptions = this.suppressedExceptions == null;  
            if (recordSuppressedExceptions) {  
                this.suppressedExceptions = new LinkedHashSet();  
            }  
  
            try {  
                singletonObject = singletonFactory.getObject();  
                newSingleton = true;  
            } catch (IllegalStateException var16) {  
            } catch (BeanCreationException var17) {  
            } finally {  
                this.afterSingletonCreation(beanName);  
            }  
  
            if (newSingleton) {  
                this.addSingleton(beanName, singletonObject);  
            }  
        }  
  
        return singletonObject != NULL_OBJECT ? singletonObject : null;  
    }  
}

//创建好的bean单例加入singletonObjects
protected void addSingleton(String beanName, Object singletonObject) {  
    synchronized(this.singletonObjects) {  
        this.singletonObjects.put(beanName, singletonObject != null ? singletonObject : NULL_OBJECT);  
        this.singletonFactories.remove(beanName);  
        this.earlySingletonObjects.remove(beanName);  
        this.registeredSingletons.add(beanName);  
    }  
}
```
- this.singletonObjects.get(beanName)，先尝试从缓存池中获取对象，没有就继续往下执行
- beforeSingletonCreation(beanName)，标记当前 bean 被创建，如果有构造函数注入的循环依赖会报错
- singletonObject = singletonFactory.getObject()，创建 bean 过程就是调用 createBean() 方法
- afterSingletonCreation(beanName)，最后把标记从集合中移除
- addSingleton(beanName, singletonObject)，新创建的会加入缓存集合


# FactoryBean 中获取 bean 实例
getObjectForBeanInstance->getObjectFromFactoryBean
```java
protected Object getObjectFromFactoryBean(FactoryBean<?> factory, String beanName, boolean shouldPostProcess) {  
	//单例
    if (factory.isSingleton() && this.containsSingleton(beanName)) {  
        synchronized(this.getSingletonMutex()) {  
            Object object = this.factoryBeanObjectCache.get(beanName);  
            if (object == null) {  
                object = this.doGetObjectFromFactoryBean(factory, beanName);  
                Object alreadyThere = this.factoryBeanObjectCache.get(beanName);  
                if (alreadyThere != null) {  
                    object = alreadyThere;  
                } else {  
                    if (object != null && shouldPostProcess) {  
                        if (this.isSingletonCurrentlyInCreation(beanName)) {  
                            return object;  
                        }  
  
                        this.beforeSingletonCreation(beanName);  
  
                        try {  
                            object = this.postProcessObjectFromFactoryBean(object, beanName);  
                        } catch (Throwable var14) {  
                            throw new BeanCreationException(beanName, "Post-processing of FactoryBean's singleton object failed", var14);  
                        } finally {  
                            this.afterSingletonCreation(beanName);  
                        }  
                    }  
  
                    if (this.containsSingleton(beanName)) {  
                        this.factoryBeanObjectCache.put(beanName, object != null ? object : NULL_OBJECT);  
                    }  
                }  
            }  
  
            return object != NULL_OBJECT ? object : null;  
        }  
    } 
    //非单例
    else {  
        Object object = this.doGetObjectFromFactoryBean(factory, beanName);  
        if (object != null && shouldPostProcess) {  
            try {  
                object = this.postProcessObjectFromFactoryBean(object, beanName);  
            } catch (Throwable var17) {  
                throw new BeanCreationException(beanName, "Post-processing of FactoryBean's object failed", var17);  
            }  
        }  
  
        return object;  
    }  
}


  private Object doGetObjectFromFactoryBean(final FactoryBean<?> factory, String beanName) throws BeanCreationException {
        Object object;
        try {
                try {
                    object = AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {
                        public Object run() throws Exception {
                            return factory.getObject();
                        }
                    }, acc);
                } catch (PrivilegedActionException var6) {
                    throw var6.getException();
                }
            } else {
                object = factory.getObject();
            }
        } 

        if (object == null && this.isSingletonCurrentlyInCreation(beanName)) {
            throw new BeanCurrentlyInCreationException();
        } else {
            return object;
        }
    }
```
# createBean
```java
protected Object createBean(String beanName, RootBeanDefinition mbd, Object[] args) throws BeanCreationException {  
    if (this.logger.isDebugEnabled()) {  
        this.logger.debug("Creating instance of bean '" + beanName + "'");  
    }  
  
    RootBeanDefinition mbdToUse = mbd;  
    Class<?> resolvedClass = this.resolveBeanClass(mbd, beanName, new Class[0]);  
    if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null) {  
        mbdToUse = new RootBeanDefinition(mbd);  
        mbdToUse.setBeanClass(resolvedClass);  
    }  
  
    try {  
        mbdToUse.prepareMethodOverrides();  
    } catch (BeanDefinitionValidationException var7) {  
        throw new BeanDefinitionStoreException(mbdToUse.getResourceDescription(), beanName, "Validation of method overrides failed", var7);  
    }  
  
    Object beanInstance;  
    try {  
        beanInstance = this.resolveBeforeInstantiation(beanName, mbdToUse);  
        if (beanInstance != null) {  
            return beanInstance;  
        }  
    } catch (Throwable var8) {  
        throw new BeanCreationException(mbdToUse.getResourceDescription(), beanName, "BeanPostProcessor before instantiation of bean failed", var8);  
    }  
  
    beanInstance = this.doCreateBean(beanName, mbdToUse, args);  
    if (this.logger.isDebugEnabled()) {  
        this.logger.debug("Finished creating instance of bean '" + beanName + "'");  
    }  
  
    return beanInstance;  
}
```

# doCreateBean
```java
/**
	 * Actually create the specified bean. Pre-creation processing has already happened
	 * at this point, e.g. checking {@code postProcessBeforeInstantiation} callbacks.
	 * <p>Differentiates between default bean instantiation, use of a
	 * factory method, and autowiring a constructor.
	 * @param beanName the name of the bean
	 * @param mbd the merged bean definition for the bean
	 * @param args explicit arguments to use for constructor or factory method invocation
	 * @return a new instance of the bean
	 * @throws BeanCreationException if the bean could not be created
	 * @see #instantiateBean
	 * @see #instantiateUsingFactoryMethod
	 * @see #autowireConstructor
	 */
	protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException {

		// Instantiate the bean.
		BeanWrapper instanceWrapper = null;
		if (mbd.isSingleton()) {
			instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);
		}
		if (instanceWrapper == null) {
			instanceWrapper = createBeanInstance(beanName, mbd, args);
		}
		Object bean = instanceWrapper.getWrappedInstance();
		Class<?> beanType = instanceWrapper.getWrappedClass();
		if (beanType != NullBean.class) {
			mbd.resolvedTargetType = beanType;
		}

		// Allow post-processors to modify the merged bean definition.
		synchronized (mbd.postProcessingLock) {
			if (!mbd.postProcessed) {
				try {
					applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
				}
				catch (Throwable ex) {
					throw new BeanCreationException(mbd.getResourceDescription(), beanName,
							"Post-processing of merged bean definition failed", ex);
				}
				mbd.markAsPostProcessed();
			}
		}

		// Eagerly cache singletons to be able to resolve circular references
		// even when triggered by lifecycle interfaces like BeanFactoryAware.
		boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&
				isSingletonCurrentlyInCreation(beanName));
		if (earlySingletonExposure) {
			if (logger.isTraceEnabled()) {
				logger.trace("Eagerly caching bean '" + beanName +
						"' to allow for resolving potential circular references");
			}
			addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
		}

		// Initialize the bean instance.
		Object exposedObject = bean;
		try {
			populateBean(beanName, mbd, instanceWrapper);
			exposedObject = initializeBean(beanName, exposedObject, mbd);
		}
		catch (Throwable ex) {
			if (ex instanceof BeanCreationException bce && beanName.equals(bce.getBeanName())) {
				throw bce;
			}
			else {
				throw new BeanCreationException(mbd.getResourceDescription(), beanName, ex.getMessage(), ex);
			}
		}

		if (earlySingletonExposure) {
			Object earlySingletonReference = getSingleton(beanName, false);
			if (earlySingletonReference != null) {
				if (exposedObject == bean) {
					exposedObject = earlySingletonReference;
				}
				else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName)) {
					String[] dependentBeans = getDependentBeans(beanName);
					Set<String> actualDependentBeans = new LinkedHashSet<>(dependentBeans.length);
					for (String dependentBean : dependentBeans) {
						if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {
							actualDependentBeans.add(dependentBean);
						}
					}
					if (!actualDependentBeans.isEmpty()) {
						throw new BeanCurrentlyInCreationException();
					}
				}
			}
		}

		// Register bean as disposable.
		try {
			registerDisposableBeanIfNecessary(beanName, bean, mbd);
		}
		catch (BeanDefinitionValidationException ex) {
			throw new BeanCreationException(
					mbd.getResourceDescription(), beanName, "Invalid destruction signature", ex);
		}

		return exposedObject;
	}
```
# 参考：
1. [面经手册 · 第30篇《关于 Spring 中 getBean 的全流程源码解析》 | 小傅哥 bugstack 虫洞栈](https://bugstack.cn/md/java/interview/2021-04-18-%E9%9D%A2%E7%BB%8F%E6%89%8B%E5%86%8C%20%C2%B7%20%E7%AC%AC30%E7%AF%87%E3%80%8A%E5%85%B3%E4%BA%8E%20Spring%20%E4%B8%AD%20getBean%20%E7%9A%84%E5%85%A8%E6%B5%81%E7%A8%8B%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%E3%80%8B.html)