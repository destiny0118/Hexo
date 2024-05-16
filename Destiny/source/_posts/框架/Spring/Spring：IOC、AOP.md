
# IOC
>**IoC（Inversion of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动*创建对象的控制权*，交由 Spring 框架来管理。通过IOC，程序实现了*对象的解耦合*，使得对象与对象之间是低耦合的，提高了程序的灵活性和可维护性。简单来说，Spring IOC就是让开发者不再需要手动创建对象，而是通过配置或注解等方式，让Spring容器来负责**对象的创建、管理和依赖关系的注入**。  
>- **控制**：指的是对象创建（实例化、管理）的权力
>- **反转**：控制权交给外部环境（Spring 框架、IoC 容器）
>
>在传统的程序设计中，我们直接在对象内部通过new关键字来创建依赖的对象，这种方式会导致代码之间高度耦合，不利于测试和维护。而IoC的思想是，将这些对象的创建权交给Spring容器来管理，由Spring容器负责创建对象并注入依赖。这样，对象与对象之间的依赖关系就交由Spring来管理了，实现了程序的解耦合。在项目中，我们可以通过Spring的配置文件或者注解来定义Bean，然后由Spring容器来创建和管理这些Bean。在Mybatis中，我们可以将SqlSessionFactory、Mapper等对象的创建和依赖关系交由Spring来管理。  
  

## Spring Bean
>Bean指被IoC容器所管理的对象。

###  将一个类声明为Bean
- `@Component`：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 `Service` 层返回数据给前端页面。


### 注入Bean的注解

| Annotation   | Package                            | Source       |
| ------------ | ---------------------------------- | ------------ |
| `@Autowired` | `org.springframework.bean.factory` | Spring 2.5+  |
| `@Resource`  | `javax.annotation`                 | Java JSR-250 |
| `@Inject`    | `javax.inject`                     | Java JSR-330 |
- `@Autowired` 是 Spring 提供的注解，`@Resource` 是 JDK 提供的注解。
- `Autowired` 默认的注入方式为`byType`（根据类型进行匹配），`@Resource`默认注入方式为 `byName`（根据名称进行匹配）。
- 当一个接口存在多个实现类的情况下，`@Autowired` 和`@Resource`都需要通过名称才能正确匹配到对应的 Bean。`Autowired` 可以通过 `@Qualifier` 注解来显式指定名称，`@Resource`可以通过 `name` 属性来显式指定名称。
- `@Autowired` 支持在构造函数、方法、字段和参数上使用。`@Resource` 主要用于字段和方法上的注入，不支持在构造函数或参数上使用。

### Bean作用域
- **singleton** : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
- **prototype** : 每次获取都会创建一个新的 bean 实例。也就是说，连续 `getBean()` 两次，得到的是不同的 Bean 实例。
- **request** （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean（请求 bean），该 bean 仅在当前 HTTP request 内有效。
- **session** （仅 Web 应用可用） : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
- **application/global-session** （仅 Web 应用可用）：每个 Web 应用在启动时创建一个 Bean（应用 Bean），该 bean 仅在当前应用启动时间内有效。
- **websocket** （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

**如何配置 bean 的作用域呢？**

xml 方式：

```
<bean id="..." class="..." scope="singleton"></bean>
```

注解方式：

```
@Bean
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Person personPrototype() {
    return new Person();
}
```
### Bean生命周期
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404031055780.png)

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404031101107.png)

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404031103107.png)
[Spring常见面试题总结 | JavaGuide](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary.html#bean-%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E4%BA%86%E8%A7%A3%E4%B9%88)
## FactoryBean
>Spring容器提供的一种可以扩展容器对象实例化逻辑的接口。其本身与其他注册到容器的对象一样，只是一个Bean，只不过，这种类型的Bean本身就是生产对象的工厂（Factory）。

```java
public interface FactoryBean{
	//返回该FactoryBean生产的对象实例，实现该方法给出自己的对象实例化逻辑
	Object getObject() throws Exception;
	//返回getObject方法所返回的对象类型
	Class getObjectType();
	//getObject生产的对象是否以singleton形式存在于容器中
	boolean isSingleton();
}
```

FactoryBean类型的bean定义，通过正常的id引用，容器返回的是FactoryBean所生产的对应类型，而非FactoryBean实现本身。
可以通过在bean定义的id之前加前缀&来取得FactoryBean本身。


# AOP
>AOP，即面向切面编程，是Spring提供的另一个重要特性。它允许开发者在不修改原有业务逻辑的情况下，增强或扩展程序的功能。AOP通过预定义的切点（例如方法执行前、方法执行后等）来织入切面逻辑，从而实现对原有功能的增强。在项目中，我们可以使用AOP来实现日志记录、事务管理、安全检查等功能。在Mybatis中，我们可以使用AOP来实现对数据库操作的日志记录、性能监控等功能。  
  