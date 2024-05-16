---
title: SQL
date: 2023-08-10
categories: 数据库
description: DDL、DML、连接
---


# 基本概念
DBMS(Datebase Managment System)
DBS(Datebase System)
DBA(Datebase Administrator)

## SQL分类
- Data Definition Language(DDL)
- Data Manipulation Language(DML)
- Data Control Language(DCL)

- `数据库（database）` - 保存有组织的数据的容器（通常是一个文件或一组文件）。
- `数据表（table）` - 某种特定类型数据的结构化清单。
- `模式（schema）` - 关于数据库和表的布局及特性的信息。模式定义了数据在表中如何存储，包含存储什么样的数据，数据如何分解，各部分信息如何命名等信息。数据库和表都有模式。
- `列（column）` - 表中的一个字段。所有表都是由一个或多个列组成的。
- `行（row）` - 表中的一个记录。
- `主键（primary key）` - 一列（或一组列），其值能够唯一标识表中每一行。

关系型数据库
关系型数据库（RDB，Relational Database）就是一种建立在关系模型的基础上的数据库。关系模型表明了数据库中所存储的数据之间的联系（一对一、一对多、多对多）。

# 创建表

```sql
create table <table_name>(
	`id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '自增ID', 
	`create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间', 
	element type [unique],
	primary key(a,b),
	UNIQUE KEY ``
);
DROP table R;


ALTER table <table_name> ADD ele float;
ALTER table <table_name> ADD ele float DEFAULT 0.0;
ALTER table <table_name> DROP ele;
```
primary key vs unique
- 主键只能有一个
- 主键属性不能为NULL
- UNQIUE可以为NULL

- `NOT NULL` - 指示某列不能存储 NULL 值。
- `UNIQUE` - 保证某列的每行必须有唯一的值。
- `PRIMARY KEY` - NOT NULL 和 UNIQUE 的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录。
- `FOREIGN KEY` - 保证一个表中的数据匹配另一个表中的值的参照完整性。
- `CHECK` - 保证列中的值符合指定的条件。
- `DEFAULT` - 规定没有给列赋值时的默认值。

# Select查询
`DISTINCT` 用于返回唯一不同的值。它作用于所有列，也就是说所有列的值都相同才算相同。
`LIMIT` 限制返回的行数。可以有两个参数，第一个参数为起始行，从 0 开始；第二个参数为返回的总行数。

```sql
#属性重命名
select a as a_,b as b_
from 
where

AND OR NOT
=, <> < > <= >=
```

| 运算符           | 描述                                     |
| ------------- | -------------------------------------- |
| =             | 等于                                     |
| <>            | 不等于。注释：在 SQL 的一些版本中，该操作符可被写成 !=        |
| >             | 大于                                     |
| <             | 小于                                     |
| >=            | 大于等于                                   |
| <=            | 小于等于                                   |
| BETWEEN       | 在某个范围内                                 |
| LIKE/not like | 搜索某种模式，%(any string) \_(any character) |
| IN            | 指定针对某个列的多个可能值                          |
| AND/OR.NOT    |                                        |


## 查询结果重排序
- ascending (ASC)：升序
- descending(DESC)
```sql
select * from sells where bar='' order by price ASC;
```


## 子查询
>子查询就是指将一个 `select` 查询（子查询）的结果作为另一个 SQL 语句（主查询）的数据来源或者判断条件。
>子查询可以被用作a value or set
>子查询可以用在from和where子句中

子查询可以嵌入 `SELECT`、`INSERT`、`UPDATE` 和 `DELETE` 语句中，也可以和 `=`、`<`、`>`、`IN`、`BETWEEN`、`EXISTS` 等运算符一起使用。

子查询常用在 `WHERE` 子句和 `FROM` 子句后边：

- 当用于 `WHERE` 子句时，根据不同的运算符，子查询可以返回单行单列、多行单列、单行多列数据。子查询就是要返回能够作为 `WHERE` 子句查询条件的值。
- 当用于 `FROM` 子句时，一般返回多行多列数据，相当于返回一张临时表，这样才符合 `FROM` 后面是表的规则。这种做法能够实现多表联合查询。

### IN

```
<tuple> IN (<subquery>) 
NOT IN (<subquery>)
in表达式可以出现在where子句中
```

### EXISTS
```
EXISTS(<subquery>) is true if and only if the subquery result is not empty.
```

### ANY
```
x = ANY(<subquery>) is a boolean condition that is true iff x equals at least one
tuple in the subquery result.
= could be any comparison operator.
Example: x >= ANY(<subquery>) means x is not the uniquely smallest tuple
produced by the subquery.
```
### ALL
```
x <> ALL(<subquery>) is true iff for every tuple t in the relation, x is not equal to
t.
That is, x is not in the subquery result.
<> can be any comparison operator.
Example: x >= ALL(<subquery>) means there is no tuple larger than x in the
subquery result. 
```

### Bag语义
- Sslect-from-where：bag semantics
- union 、intersection、difference：set semantic
### 控制Duplicate Elimination
```
Force the result to be a set by SELECT DISTINCT . . .
Force the result to be a bag (i.e., don't eliminate duplicates) by ALL, as in . . .
UNION ALL . . .
•
```

# 连接（join）

>SQL JOIN 子句用于将两个或者多个表联合起来进行查询。连接表时需要在每个表中选择一个字段，并对这些字段的值进行比较，值相同的两条记录将合并为一条。**连接表的本质就是将不同表的记录合并起来，形成一张新表。当然，这张新表只是临时的，它仅存在于本次查询期间**。

### theat join
```sql
Drinkers(name, addr)
Frequents(drinker, bar)
SELECT *
FROM Drinkers JOIN Frequents ON name = drinker
(d,a,d,b)
drinker d住在a，并且经常去b
```

inner join：2表值都存在
outer join：附表中值可能存在null的情况。

　①A inner join B：取交集

　②A left join B：取A全部，B没有对应的值，则为null

　③A right join B：取B全部，A没有对应的值，则为null

　④A full outer join B：取并集，彼此没有对应的值为null

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403282002516.png)

|连接类型|说明|
|---|---|
|INNER JOIN 内连接|（默认连接方式）只有当两个表都存在满足条件的记录时才会返回行。|
|LEFT JOIN / LEFT OUTER JOIN 左(外)连接|返回左表中的所有行，即使右表中没有满足条件的行也是如此。|
|RIGHT JOIN / RIGHT OUTER JOIN 右(外)连接|返回右表中的所有行，即使左表中没有满足条件的行也是如此。|
|FULL JOIN / FULL OUTER JOIN 全(外)连接|只要其中有一个表存在满足条件的记录，就返回行。|
|SELF JOIN|将一个表连接到自身，就像该表是两个表一样。为了区分两个表，在 SQL 语句中需要至少重命名一个表。|
|CROSS JOIN|交叉连接，从两个或者多个连接表中返回记录集的笛卡尔积。|


where>group by>having>order by


添加更新

 if not exists (select 1 from t where id = 1)
      insert into t(id, update_time) values(1, getdate())
   else
      update t set update_time = getdate() where id = 1


# 聚合函数
sum、avg、count、min、max：can be applied to a column in
a SELECT clause to produce that aggregation on the column

使用 `DISTINCT` 可以让汇总函数值汇总不同的值。
# 分组（group by）
SELECT-FROM-WHERE-GROUP BY
>查询出的关系

If any aggregation is used, then each element of the SELECT list
must be either:
1. Aggregated, or
2. An attribute on the GROUP BY list.

# HAVING
SELECT-FROM-WHERE-GROUP BY-HAVING
- `having` 用于对汇总的 `group by` 结果进行过滤。
- `having` 一般都是和 `group by` 连用。
- `where` 和 `having` 可以在相同的查询中。

# 组合
## union、intersection、except
满足任一条件
满足所有条件
满足指定条件（排除满足条件的部分）

>From Likes(drinker, beer) and Sells(bar, beer, price), Find the beer which is the favorite of 'Lynn Conway' or the price is above 40
```sql
(SELECT beer FROM Likes
WHERE drinker='Lynn Conway')
UNION
(SELECT beer FROM Sells
WHERE price>40);
```

# 数据库修改

```sql
INSERT INTO <relation>(属性列表) VALUES ( <list of values>);
INSERT INTO <relation> (...) ( <subquery> );

DELETE FROM <relation> WHERE <condition>;

UPDATE <relation>
SET <list of attribute assignments>
WHERE <condition on tuples>;
```
# 第一范式、第二范式、第三范式、BCNF

- 第一范式：列不可再分
- 第二范式：建立在第一范式基础上，消除部分依赖
- 第三范式：建立在第二范式基础上，消除传递依赖。
