![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202401101026534.png)

# BeanFactory
>生产 bean 的工厂，它负责生产和管理各个 bean 实例。

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202401101029274.png)

- ListableBeanFactory：通过这个接口，我们可以获取多个 Bean
- HierarchicalBeanFactory：在应用中起多个 BeanFactory，然后可以将各个 BeanFactory 设置为父子关系。
- AutowireCapableBeanFactory：自动装配Bean
- ConfigurableListableBeanFactory