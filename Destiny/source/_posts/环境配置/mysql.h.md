---
title: mysql.h
description: mysql函数介绍
---

# mysql_init()
`MYSQL *mysql_init(MYSQL *mysql)`
- 初始化MYSQL数据结构，返回MYSQL指针。
- 成功时返回指向MYSQL结构体的指针，失败时返回NULL，并设置错误号（通过mysql_errno()获取）。
# mysql_real_connect()
`MYSQL *mysql_real_connect(MYSQL *mysql, const char *host, const char *user, const char *passwd, const char *db, unsigned int port, const char *unix_socket, unsigned long clientflag)`
- 建立Mysql服务器连接
- mysql：MYSQL结构体指针变量。
- host：MySQL服务器名称或IP地址。
- user：登录MySQL服务器的用户名。
- passwd：登录MySQL服务器的密码。
- db：要连接的数据库名称。
- port：MySQL服务器端口号。
- unix_socket：UNIX套接字文件路径。
- clientflag：客户端标志位。
- 返回值说明：成功时返回指向MYSQL结构体的指针，失败时返回NULL，并设置错误号（通过mysql_errno()获取）。
# mysql_query()
`int mysql_query(MYSQL *mysql, const char *stmt_str)`
- 执行一条SQL查询语句或更新语句。
- mysql：MYSQL结构体指针变量。
- stmt_str：要执行的SQL语句字符串。
- 返回值说明：成功时返回0，失败时返回非0错误代码（通过mysql_errno()获取）。
# mysql_real_query()
`int mysql_real_query(MYSQL *mysql, const char *stmt_str, unsigned long length)`
- 执行一条SQL查询语句或更新语句。
- mysql：MYSQL结构体指针变量。
- stmt_str：要执行的SQL语句字符串。
- length：SQL语句长度。
- 返回值说明：成功时返回0，失败时返回非0错误代码（通过mysql_errno()获取）。

# mysql_store_result()
`MYSQL_RES *mysql_store_result(MYSQL *mysql)`
- 说明：将查询结果集保存在客户端内存中。
- mysql：MYSQL结构体指针变量。
- 返回值说明：成功时返回一个MYSQL_RES结构体指针，失败时返回NULL，并设置错误号（通过mysql_errno()获取）。
# mysql_use_result()
`MYSQL_RES *mysql_use_result(MYSQL *mysql)`
>说明：逐行获取查询结果集。
- mysql：MYSQL结构体指针变量。
- 返回值说明：成功时返回一个MYSQL_RES结构体指针，失败时返回NULL，并设置错误号（通过mysql_errno()获取）。

# 结果集相关函数
## mysql_fetch_row()
`MYSQL_ROW mysql_fetch_row(MYSQL_RES *result)`
>说明：逐行获取查询结果集，并返回一个MYSQL_ROW结构体指针。

- result：MYSQL_RES结构体指针变量。
- 返回值说明：成功时返回一个MYSQL_ROW结构体指针，失败时返回NULL。

## mysql_num_rows()
`unsigned long mysql_num_rows(MYSQL_RES *result)`
>说明：获取查询结果集中的行数。

- result：MYSQL_RES结构体指针变量。
- 返回值说明：成功时返回查询结果集中行的数量，失败时返回0。

## mysql_num_fields()
`unsigned int mysql_num_fields(MYSQL_RES *result)`
>说明：获取查询结果集中的列数。

- result：MYSQL_RES结构体指针变量。
- 返回值说明：成功时返回查询结果集中列的数量，失败时返回0。

## mysql_field_count()
`unsigned int mysql_field_count(MYSQL *mysql)`
>说明：获取最近一次执行的SQL语句所返回的列数。

- mysql：MYSQL结构体指针变量。
- 返回值说明：成功时返回最近一次执行的SQL语句所返回的列数，失败时返回0。


# 参考
[mysql.h函数详解-CSDN博客](https://blog.csdn.net/A_salted_fisher/article/details/131117697)