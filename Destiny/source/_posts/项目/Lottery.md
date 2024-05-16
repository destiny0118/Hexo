# 表设计
- 活动表：是一个用于配置抽奖活动的总表，用于存放活动信息，包括：ID、名称、描述、时间、库存、参与次数等。
- 奖品表
- 策略表
- 规则表
- 用户参与表
- 中奖信息表

- 抽奖活动配置（活动ID、活动名称、活动描述、活动库存、每人可参与次数、活动状态）
- 奖品策略配置（策略ID、策略描述、策略方式、发放奖品方式）
- 奖品策略明细配置（==策略ID、奖品ID==、奖品描述、奖品库存、奖品剩余库存、中奖概率）
- 奖品信息配置（奖品ID、奖品类型、奖品名称、奖品内容）
- 用户活动参与次数（用户ID、活动ID、可参与次数、已参与次数）


首先要有活动信息表，记录活动可参与次数等信息；其次，对于每个抽奖活动配置抽奖策略。抽奖策略有具体明细，可以抽取的奖品。最后，抽奖明细中可以抽取到哪些奖品。
- 活动配置，activity：提供活动的基本配置
- 策略配置，strategy：用于配置抽奖策略，概率、玩法、库存、奖品
- 策略明细，strategy_detail：抽奖策略的具体明细配置
- 奖品配置，award：用于配置具体可以得到的奖品
- 用户参与活动记录表，user_take_activity：每个用户参与活动都会记录下他的参与信息，时间、次数
- 用户活动参与次数表，user_take_activity_count：用于记录当前参与了多少次
- 用户策略计算结果表，user_strategy_export_001~004：最终策略结果的一个记录，也就是奖品中奖信息的内容
# DDD
## lottery-rpc
- DTO
- req
- res
- 服务接口
rpc提供接口，供外界调用，在req中封装请求对象，res封装请求结果（将查询的结果DTO对象也封装其中）
## lottery-interfaces
- interfaces
- LotteryApplication
- resources
	- mybatis
		- config
		- mapper
	- application.yml
	
在interfaces下实现rpc层定义的接口，调用infrastructure层提供的仓储服务进行查询


## lottery-domain
定义仓储接口


## lottery-infrastructure
- dao：访问数据库，封装数据库操作
- po

实现仓储接口



# 抽奖策略领域（策略模式、模板模式）
两种抽奖算法描述，场景A20%、B30%、C50%
- **总体概率**：如果A奖品抽空后，B和C奖品的概率按照 `3:5` 均分，相当于B奖品中奖概率由 `0.3` 升为 `0.375`
- **单项概率**：如果A奖品抽空后，B和C保持目前中奖概率，用户抽奖扔有20%中为A，因A库存抽空则结果展示为未中奖。(将奖品依据抽奖概率分配到长度为100的字符数组中，每一个位置可能对应多个奖品，只要该位置奖品没抽完，即算抽中。可以通过哈希散列的方式，将奖品信息分散到数组中去)

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403112313092.png)

- domain
	- strategy
		- model：用于提供vo、req、res 和 aggregates 聚合对象。
		- repository：提供仓储服务，其实也就是对Mysql、Redis等数据的统一包装。
		- service：是具体的业务领域逻辑实现层
			- algorithm
				- impl
					- SingleRateRandomDrawAlgorithm
					- EntiretyRateRandomDrawAlgorithm
				- IDrawAlgorithm
			- draw
				- impl
					- DrawExecImpl
				- AbstractDrawBase（模板模式定义抽象抽奖过程，涉及到具体差异实现的则在impl下实现）
				- DrawConfig（策略配置，哈希表映射到IDrawAlgorithm的具体实现类）
				- DrawStrategySupport（提供抽奖策略数据支持，便于查询策略配置、奖品信息。）
				- IDrawExel（接口，暴漏抽奖方法接口给外界使用）

抽奖策略使用了策略模式，即提供一个接口，供抽奖调用，同时不同策略方式分别实现该接口

在draw下处理抽奖流程
- DrawConfig：配置抽奖策略，SingleRateRandomDrawAlgorithm、EntiretyRateRandomDrawAlgorithm
- DrawStrategySupport：提供抽奖策略数据支持，便于查询策略配置、奖品信息。通过这样的方式隔离职责。
- AbstractDrawBase：抽象类定义模板方法流程，在抽象类的 `doDrawExec` 方法中，处理整个抽奖流程，并提供在流程中需要使用到的抽象方法，由 `DrawExecImpl` 服务逻辑中做具体实现。
- IDrawExec#doDrawExec
	- `获取抽奖策略`、`校验抽奖策略是否已经初始化到内存`、`获取不在抽奖范围内的列表，包括：奖品库存为空、风控策略、临时调整等`、`执行抽奖算法`、`包装中奖结果`
![Lottery-Draw抽奖流程.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403121148213.png)

# 搭建发奖领域（简单工厂设计模式）
>定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。
>
>利用工厂模式+接口封装奖品，让每一块功能都更加清晰易于扩展和维护。
>
>简单工厂模式避免创建者与具体的产品逻辑耦合、满足单一职责，每一个业务逻辑实现都在所属自己的类中完成、满足开闭原则，无需更改使用调用方就可以在程序中引入新的产品类型。但这样也会带来一些问题，比如有非常多的奖品类型，那么实现的子类会极速扩张，对于这样的场景就需要在引入其他设计手段进行处理，例如抽象通用的发奖子领域，自动化配置奖品发奖。

```
└── domain
	└──award
		├── model
		├── repository
		│   ├── impl
		│   │   └── AwardRepository
		│   └── IAwardRepository
		└── service
			├── factory
			│   ├── DistributionGoodsFactory.java（通过提供的奖品类型获取具体的奖品实现类）
			│   └── GoodsConfig.java（配置文件，通过map将奖品类型映射到具体实现类）
			└── goods
				├── impl
				│   ├── CouponGoods.java
				│   ├── DescGoods.java
				│   ├── PhysicalGoods.java
				│   └── RedeemCodeGoods.java
				├── DistributionBase.java（发奖涉及到的对数据仓储的修改）
				└── IDistributionGoodsc.java(接口类，奖品配送接口)
```

# 活动领域的配置与状态
## 活动创建
活动的创建操作主要包括：添加活动配置、添加奖品配置、添加策略配置、添加策略明细配置

## 状态变更（设计模式：状态模式）
>状态模式：类的行为是基于它的状态改变的，这种类型的设计模式属于行为型模式。它描述的是一个行为下的多种状态变更，比如我们最常见的一个网站的页面，在你登录与不登录下展示的内容是略有差异的(不登录不能展示个人信息)，而这种登录与不登录就是我们通过改变状态，而让整个行为发生了变化。

```
domain.activity
├── model
├── repository
│   └── IActivityRepository
└── service
	├── deploy
	├── partake [待开发]
	└── stateflow
		├── event（都实现了AbstractState接口，当前状态在相应事件下的响应）
		│   ├── ArraignmentState.java
		│   ├── CloseState.java
		│   ├── DoingState.java
		│   ├── EditingState.java
		│   ├── OpenState.java
		│   ├── PassState.java
		│   └── RefuseState.java
		├── impl
		│   └── StateHandlerImpl.java（实现类，获取当前状态的实现类，调用相应事件操作）
		├── AbstractState.java（接口，提供了各项状态之间流转服务的接口，即一个状态到另一个状态的事件）
		├── IStateHandler.java（接口，暴露给外界可用于改变状态的事件操作）
		└── StateConfig.java（提供map，整形到具体event下状态实现类的映射）
```





# ID生成策略（策略模式）
>策略模式是行为模式的一种：外部的调用方会需要根据不同的场景来选择出适合的ID生成策略

- 订单号：唯一、大量、订单创建时使用、分库分表
- 活动号：唯一、少量、活动创建时使用、单库单表
- 策略号：唯一、少量、活动创建时使用、单库单表
```
domain.support.ids
├── policy（IIdGenerator具体实现类）
│   ├── RandomNumeric.java
│   ├── ShortCode.java
│   └── SnowFlake.java
├── IdContext.java（map，记录枚举类型到ID生成具体实现类的映射）
└── IIdGenerator.java(接口类，定义生成ID的策略接口，供外界调用)
```



# 分库分表
由于业务体量较大，数据增长较快，所以需要把用户数据拆分到不同的库表中去，减轻数据库压力。

分库分表操作主要有垂直拆分和水平拆分：

- 垂直拆分：指按照业务将表进行分类，分布到不同的数据库上，这样也就将数据的压力分担到不同的库上面。最终一个数据库由很多表的构成，每个表对应着不同的业务，也就是专库专用。
- 水平拆分：如果垂直拆分后遇到单机瓶颈，可以使用水平拆分。相对于垂直拆分的区别是：垂直拆分是把不同的表拆到不同的数据库中，而本章节需要实现的水平拆分，是把同一个表拆到不同的数据库中。如：user_001、user_002

数据库路由设计
- 是关于 AOP 切面拦截的使用，这是因为需要给使用数据库路由的方法做上标记，便于处理分库分表逻辑。
- 数据源的切换操作，既然有分库那么就会涉及在多个数据源间进行链接切换，以便把数据分配给不同的数据库。
- 数据库表寻址操作，一条数据分配到哪个数据库，哪张表，都需要进行索引计算。在方法调用的过程中最终通过 ThreadLocal 记录。
- 为了能让数据均匀的分配到不同的库表中去，还需要考虑如何进行数据散列的操作，不能分库分表后，让数据都集中在某个库的某个表，这样就失去了分库分表的意义。
- Mybatis 拦截器处理分表 SQL

![router.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403142000474.png)


# 领取活动（模板模式）
>用于领取活动领域功能开发中用户领取活动信息，在一个事务下记录多张表数据。
>领取活动的流程作为模板实现，具体调用的方法在实现类实现。


```
domain.activity.service.partake
|-impl
|	|-ActivityPartakeImpl
|-ActivityPartakeSupport：根据uid,activityID查询活动信息和用户领取信息
|-BaseActivityPartake：doPartake处理活动领取，调用抽象方法在实现类进行具体实现
|-IActivityPartake
```

领取活动流程：
- 根据uid、activityId查询活动账单
- 活动信息校验处理（抽象方法）
- 扣减活动库存(activity表，可优化为Redis扣减)
- 领取活动（个人用户把活动信息写入到用户表）（扣减user_take_activity_count剩余可领取次数，将领取活动信息插入user_take_activity）
- 返回策略ID，用于后续抽奖步骤

# 应用层：抽奖过程

在 application 应用层调用领域服务功能，编排抽奖过程，包括：领取活动、执行抽奖、落库结果

分别在两个分库的表 lottery_01.user_take_activity、lottery_02.user_take_activity 中添加 state`【活动单使用状态 0未使用、1已使用】` 状态字段，这个状态字段用于写入中奖信息到 user_strategy_export_000~003 表中时候，两个表可以做一个幂等性的事务。

同时还需要加入 strategy_id 策略ID字段，用于处理领取了活动单但执行抽奖失败时，可以继续获取到此抽奖单继续执行抽奖，而不需要重新领取活动。_其实领取活动就像是一种活动镜像信息，可以在控制幂等反复使用_

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404152300246.png)

- 抽奖整个活动过程的流程编排，主要包括：对活动的领取、对抽奖的操作、对中奖结果的存放，以及如何处理发奖，对于发奖流程我们设计为MQ触发，后续再补全这部分内容。
- 对于每一个流程节点编排的内容，都是在领域层开发完成的，而应用层只是做最为简单的且很薄的一层。_其实这块也很符合目前很多低代码的使用场景，通过界面可视化控制流程编排，生成代码_


抽奖过程：
- 用户请求抽奖(doPartake)（uId, activityId），领取活动
	- `查询是否存在未执行抽奖领取活动单【user_take_activity 存在 state = 0，领取了但抽奖过程失败的，可以直接返回领取结果继续抽奖】`
- 执行抽奖（`doDrawExec`）（uId, strategyId, takeId）
- 结果落库（uId, activityId，strategyId, takeId）
- 发送MQ，触发发奖流程
- 返回结果



# 量化人群参与活动（规则引擎、组合模式）
>使用组合模式搭建用于量化人群的规则引擎，用于用户参与活动之前，通过规则引擎过滤性别、年龄、首单消费、消费金额、忠实用户等各类身份来量化出具体可参与的抽奖活动。通过这样的方式控制运营成本和精细化运营。

组合模式的特点就像是搭建出一棵二叉树，而库表中则需要把这样一颗二叉树存放进去，那么这里就需要包括：树根、树茎、子叶、果实。在具体的逻辑实现中则需要通过子叶判断走哪个树茎以及最终筛选出一个果实来。

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404161042433.png)

- 基于量化决策引擎，筛选用户身份标签，找到符合参与的活动号。拿到活动号后，就可以参与到具体的抽奖活动中了。
- 通常量化决策引擎也是一种用于差异化人群的规则过滤器，不只是可以过滤出活动，也可以用于活动唯独的过滤，判断是否可以参与到这个抽奖活动中。

![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404161057494.png)

```
Lottery
└── src
    └── main
       └── java
          └── cn.itedus.lottery.domain.rule
              ├── model
              │   ├── aggregates
              │   │   └── TreeRich.java
              │   ├── req
              │   │   └── DecisionMatterReq.java
              │   ├── res
              │   │   └── EngineResult.java
              │   └── vo
              │       ├── TreeNodeLineVO.java
              │       ├── TreeNodeVO.java
              │       └── TreeRootVO.java	
              └── service
                  ├── engine
                  │   ├── impl	
                  │   │   └── TreeEngineHandle.java（实现类，先查询树对应的所有节点以及节点间的连线）
                  │   ├── EngineBase.java （engineDecisionMaker，根据过滤条件筛选出叶子节点）
                  │   ├── EngineConfig.java（配置不同条件使用的过滤器）
                  │   └── IEngine.java	（暴露process接口，调用参数：treeRootId，过滤条件）
                  └── logic
                      ├── impl	
                      │   ├── UserAgeFilter.java
                      │   └── UserGenderFilter.java
                      ├── BaseLogic.java（根据决策值，对所有连线进行判断）
                      └── LogicFilter.java（暴露接口）
```



# MQ：抽奖发货流程

使用MQ消息的特性，把用户抽奖到发货到流程进行解耦。这个过程中包括了消息的发送、库表中状态的更新、消息的接收消费、发奖状态的处理等。

在数据库表 `user_strategy_export` 添加字段 `mq_state` 这个字段用于发送 MQ 成功更新库表状态，如果 MQ 消息发送失败则需要通过定时任务补偿 MQ 消息。PS：你可以使用本章节分支下的 sql 更新自己的库表。
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404172253392.png)



     // 1. 领取活动
    // 2. 执行抽奖
    // 3. 结果落库
    // 4. 发送MQ，触发发奖流程
	    发奖（消费者收到消息进行发奖）
	`// 4.1 MQ 消息发送完成，更新数据库表 user_strategy_export.mq_state = 1`
	`// 4.2 MQ 消息发送失败，更新数据库表 user_strategy_export.mq_state = 2 【等待定时任务扫码补偿MQ消息】`


消息发送完毕后进行回调处理，更新数据库中 MQ 发送的状态，如果有 MQ 发送失败则更新数据库 mq_state = 2 **这里还有可能在更新库表状态的时候失败**，但没关系这些都会被 worker 补偿处理掉，一种是发送 MQ 失败，另外一种是 MQ 状态为 0 但很久都没有发送 MQ 那么也可以触发发送。