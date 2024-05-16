
`java.lang.IllegalStateException: Failed to load ApplicationContext`

`Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'stateHandlerImpl': Injection of resource dependencies failed; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'indiv.lottery.domain.activity.service.stateflow.event.ArraignmentState' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@javax.annotation.Resource(shareable=true, lookup=, name=, description=, authenticationType=CONTAINER, type=class java.lang.Object, mappedName=)}`

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202312141928179.png)

没有定义component组件

`org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'indiv.lottery.test.domain.ActivityTest': Injection of resource dependencies failed; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: `
没有定义service


```shell
org.springframework.jdbc.BadSqlGrammarException: 
### Error updating database.  Cause: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Unknown column 'activityId' in 'field list'
### The error may exist in file [E:\IDEA\MyLottery\lottery-interfaces\target\classes\mybatis\mapper\Activity_Mapper.xml]
### The error may involve indiv.lottery.infrastructure.dao.IActivityDAO.insert-Inline
### The error occurred while setting parameters
### SQL: INSERT INTO activity         (activityId, activityName, activityDesc, beginDateTime, endDateTime,          stockCount, takeCount, state, creator, createTime, updateTime)         VALUES (?, ?, ?, ?, ?,                 ?, ?, ?, ?, now(), now())
### Cause: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Unknown column 'activityId' in 'field list'
; bad SQL grammar []; nested exception is com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Unknown column 'activityId' in 'field list'
```
insert参数名与数据库不匹配


```shell
org.apache.ibatis.binding.BindingException: Invalid bound statement (not found): indiv.lottery.infrastructure.dao.IAwardDAO.insertList
```


# Mysql
## Error querying database. Cause: org.springframework.jdbc.CannotGetJdbcConnectionException

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202312252241556.png)



# 依赖冲突
lottery-application: Could not resolve dependencies for project cn.itedus.lottery:lottery-application:jar:1.0-SNAPSHOT: The following artifacts could not be resolved: cn.bugstack.middleware:db-router-spring-boot-starter:jar:1.0.0-SNAPSHOT (absent): Could not find artifact cn.bugstack.middleware:db-router-spring-boot-starter:jar:1.0.0-SNAPSHOT -> [Help 1]

application依赖domain域，但却是在Lottery顶层模块出现版本问题
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202312261102257.png)

