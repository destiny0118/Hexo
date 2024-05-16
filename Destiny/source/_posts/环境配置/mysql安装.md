# MySql安装
## linux安装
https://blog.csdn.net/New_codeline/article/details/123142931

```shell
dpkg -i mysql-common_5.7.29-1ubuntu18.04_amd64.deb
dpkg -i libmysqlclient20_5.7.29-1ubuntu18.04_amd64.deb
dpkg -i libmysqlclient-dev_5.7.29-1ubuntu18.04_amd64.deb
dpkg -i libmysqld-dev_5.7.29-1ubuntu18.04_amd64.deb 

dpkg -i mysql-community-source_5.7.29-1ubuntu18.04_amd64.deb 


apt-get install libaio1 libtinfo5
dpkg -i mysql-community-client_5.7.29-1ubuntu18.04_amd64.deb 

dpkg -i mysql-client_5.7.29-1ubuntu18.04_amd64.deb 
apt-get install libmecab2
dpkg -i mysql-community-server_5.7.29-1ubuntu18.04_amd64.deb 
dpkg -i mysql-server_5.7.29-1ubuntu18.04_amd64.deb 


启动mysql服务:
service mysql start

进入MySQL:
mysql -u root -p

退出MySQL(三种方法):
mysql > exit;
mysql > quit;
mysql > \q;
```
## windows安装
[Windows安装mysql详细步骤（通俗易懂，简单上手）_华夏之威的博客-CSDN博客](https://blog.csdn.net/weixin_43423484/article/details/124408565)
[MySQL 安装 | 菜鸟教程 (runoob.com)](https://www.runoob.com/mysql/mysql-install.html)

[MySQL安装配置（可视化安装界面），可视化工具安装，连接IDEA，JDBC安装配置，在IDEA中书写第一个MySQL程序，超简单教程（超详细）。_mysql可视化界面_喝咖啡的皮卡丘的博客-CSDN博客](https://blog.csdn.net/lyl1234564/article/details/120647573)


# 其他问题


[mysql | 修改MySQL密码的四种方法 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/616953225)


# mysql问题
## 
>Error Code: 1366. Incorrect string value: '\xE5\x93\x87' for column 'name' at row 1

```mysql
修改数据库的字符集：alter database `dbname` character set utf8;
修改表的字符集：alter table `tablename` **convert to** character set utf8;
```

[解决MySQL5.7版本以上不支持中文问题 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/115331180#:~:text=%E5%8F%AF%E4%BB%A5%E5%8E%BB%E4%BB%BB%E5%8A%A1%E7%AE%A1%E7%90%86%E5%99%A8%E4%B8%AD%EF%BC%8C%E9%87%8D%E5%90%AFmysql%E7%9A%84%E6%9C%8D%E5%8A%A1%20%E7%AC%AC%E5%9B%9B%E6%AD%A5%EF%BC%9A%E5%86%8D%E6%AC%A1%E6%9F%A5%E7%9C%8B%E4%B8%80%E4%B8%8B%E5%BD%93%E5%89%8D%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E5%AD%97%E7%AC%A6%E9%9B%86%20SQL%E8%AF%AD%E5%8F%A5%EF%BC%9A%20show%20variables%20like%20%27character%25%27%3B%20%E7%AC%AC%E4%BA%94%E6%AD%A5%EF%BC%9A%E5%8F%AF%E4%BB%A5%E6%B5%8B%E8%AF%95%E6%98%AF%E5%90%A6%E5%8F%AF%E4%BB%A5%E6%8F%92%E5%85%A5%E4%B8%AD%E6%96%87%EF%BC%8C%E5%A6%82%E6%9E%9C%E8%BF%98%E4%B8%8D%E8%A1%8C%EF%BC%8C%E4%BF%AE%E6%94%B9%E6%95%B0%E6%8D%AE%E5%BA%93%E5%92%8C%E8%A1%A8%E7%9A%84%E5%AD%97%E7%AC%A6%E9%9B%86,table%20tablename%20convert%20to%20character%20set%20utf8%3B%20%E7%84%B6%E5%90%8E%E5%B0%B1%E5%8F%AF%E4%BB%A5%E5%95%A6%E3%80%82)
[MySql报 java.sql.SQLException: Incorrect string value 乱码解决方法_se. cause fava.sqlsqlexception inicorrect string w-CSDN博客](https://blog.csdn.net/xj13829061922/article/details/106123694)

# mysql返回值

[【转】详解mybatis的insert，update，delete返回值_mybatis的insert返回什么-CSDN博客](https://blog.csdn.net/beidaol/article/details/90961349)