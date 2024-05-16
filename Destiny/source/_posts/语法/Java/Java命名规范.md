# package

indi ：

个体项目，指个人发起，但非自己独自完成的项目，可公开或私有项目，copyright主要属于发起者。

包名为“indi.发起者名.项目名.模块名.……”。

pers ：

个人项目，指个人发起，独自完成，可分享的项目，copyright主要属于个人。

包名为“pers.个人名.项目名.模块名.……”。

priv ：

私有项目，指个人发起，独自完成，非公开的私人使用的项目，copyright属于个人。

包名为“priv.个人名.项目名.模块名.……”。

team ：

团队项目，指由团队发起，并由该团队开发的项目，copyright属于该团队所有。

包名为“team.团队名.项目名.模块名.……”。

com ：

公司项目，copyright由项目发起的公司所有。

包名为“com.公司名.项目名.模块名.……”。

[java 项目的包命名规则是什么](https://www.zhihu.com/question/450664324)

# 命名规范
[10分钟搞定令人头疼的代码命名规范 | JavaGuide - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/448253937)





# Java对象概念：PO BO VO DTO DAO

PO 是 Persistant Object 的缩写，用于表示数据库中的一条记录映射成的 java 对象。PO 仅仅用于表示数据，没有任何数据操作。通常遵守 Java Bean 的规范，拥有 getter/setter 方法。

DAO 是 Data Access Object 的缩写，用于表示一个数据访问对象。使用 DAO 访问数据库，包括插入、更新、删除、查询等操作，与 PO 一起使用。DAO 一般在持久层，完全封装数据库操作，对外暴露的方法使得上层应用不需要关注数据库相关的任何信息。(==Maper映射，封装数据库操作的一个映射对象==)

VO 是 Value Object 的缩写，用于表示一个与前端进行交互的 java 对象。有的朋友也许有疑问，这里可不可以使用 PO 传递数据？实际上，这里的 VO 只包含前端需要展示的数据即可，对于前端不需要的数据，比如数据创建和修改的时间等字段，出于减少传输数据量大小和保护数据库结构不外泄的目的，不应该在 VO 中体现出来。通常遵守 Java Bean 的规范，拥有 getter/setter 方法。

DTO 是 Data Transfer Object 的缩写，用于表示一个数据传输对象。DTO 通常用于不同服务或服务不同分层之间的数据传输。DTO 与 VO 概念相似，并且通常情况下字段也基本一致。但 DTO 与 VO 又有一些不同，这个不同主要是设计理念上的，比如 API 服务需要使用的 DTO 就可能与 VO 存在差异。通常遵守 Java Bean 的规范，拥有 getter/setter 方法。

BO 是 Business Object 的缩写，用于表示一个业务对象。BO 包括了业务逻辑，常常封装了对 DAO、RPC 等的调用，可以进行 PO 与 VO/DTO 之间的转换。BO 通常位于业务层，要区别于直接对外提供服务的服务层：BO 提供了基本业务单元的基本业务操作，在设计上属于被服务层业务流程调用的对象，一个业务流程可能需要调用多个 BO 来完成。

POJO 是 Plain Ordinary Java Object 的缩写，表示一个简单 java 对象。上面说的 PO、VO、DTO 都是典型的 POJO。而 DAO、BO 一般都不是 POJO，只提供一些调用方法。

![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202312052259482.png)




  
[PO BO VO DTO POJO DAO DO这些Java中的概念分别指一些什么？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/39651928)