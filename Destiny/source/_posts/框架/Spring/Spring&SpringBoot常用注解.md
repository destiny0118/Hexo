---
title: Java注解含义
description: 
data: 2023-12-9
categories:
  - 开发框架
---
# @Component, @Repository, @Service, @Controller
```java
import org.springframework.stereotype.Component;
@Component
```
- `@Component`：标注一个类为Spring容器的Bean，把普通pojo实例化到spring容器中，相当于配置文件中的\<bean id="" class=""/\>
- `@Repository`：对应持久层即Dao层，主要用于数据库相关操作
- `@Service`：对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller`：对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。表示Web层实现。



