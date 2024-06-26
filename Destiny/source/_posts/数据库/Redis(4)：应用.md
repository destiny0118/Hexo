---
title: Redis(4)：应用
date: 2024-03-24
description: 分布式锁、消息队列、分布式事务、延时任务
top: 7
categories: 数据库
tags:
  - Redis
---
- **分布式锁**：通过 Redis 来做分布式锁是一种比较常见的方式。通常情况下，我们都是基于 Redisson 来实现分布式锁。
- **限流**：一般是通过 Redis + Lua 脚本的方式来实现限流。
- **消息队列**：Redis 自带的 List 数据结构可以作为一个简单的队列使用。Redis 5.0 中增加的 Stream 类型的数据结构更加适合用来做消息队列。它比较类似于 Kafka，有主题和消费组的概念，支持消息持久化以及 ACK 机制。
- **延时队列**：Redisson 内置了延时队列（基于 Sorted Set 实现的）。
- **分布式 Session** ：利用 String 或者 Hash 数据类型保存 Session 数据，所有的服务器都可以访问。
- **复杂业务场景**：通过 Redis 以及 Redis 扩展（比如 Redisson）提供的数据结构，我们可以很方便地完成很多复杂的业务场景比如通过 Bitmap 统计活跃用户、通过 Sorted Set 维护排行榜。

# 分布式锁
## 分布式锁实现
`SETNX` 是 Redis 中的一个命令，用于设置键的值，但仅当键不存在时才设置成功。在分布式环境中，可以利用 `SETNX` 命令来实现分布式锁。具体步骤如下：

1. 客户端通过 `SETNX` 命令尝试将一个特定的键作为锁的标识，并设置一个唯一的值作为锁的持有者标识。
2. 如果 `SETNX` 命令成功执行（返回值为 1），表示当前客户端成功获取了锁，可以执行后续操作。
3. 如果 `SETNX` 命令执行失败（返回值为 0），表示当前锁已被其他客户端持有，当前客户端未获取到锁，需要等待一段时间后重新尝试获取锁。

虽然 `SETNX` 命令在某些情况下可以用来实现简单的分布式锁，但是它也存在一些问题：

1. **无法设置过期时间**：`SETNX` 命令本身不支持设置键的过期时间，因此当持有锁的客户端发生异常或程序出现问题时，可能导致锁无法被释放，造成死锁或锁泄露问题。
2. **非原子性操作**：尽管 `SETNX` 命令本身是原子性的，但是获取锁和释放锁通常需要多个命令的组合，例如获取锁时需要执行 `SETNX`，释放锁时需要执行 `DEL`。这种组合操作不是原子性的，可能会导致锁的不一致性问题。
3. Redis集群，设置锁到主节点，主节点同步锁时挂掉


为了解决这些问题，可以采用以下方法：

1. **配合 `EXPIRE` 命令设置过期时间**：在获取锁成功后，使用 `EXPIRE` 命令为锁设置一个合理的过期时间，确保即使持有锁的客户端发生异常，锁也能在一定时间后自动释放。
2. **使用 Lua 脚本确保原子性**：将获取锁和释放锁的操作封装在 Lua 脚本中执行，Lua 脚本可以在 Redis 中以原子性的方式执行多个命令，确保获取锁和释放锁的操作是原子性的，避免了竞态条件的发生。
3. 考虑使用**Redlock红锁**算法等更复杂的分布式锁方案：如果应用场景要求更高的分布式锁安全性和可靠性，可以考虑使用 Redlock 算法等更复杂的分布式锁方案，这些方案通常基于多个 Redis 实例，并结合超时机制和复制机制来保证分布式锁的安全性和可靠性。

  

## 分布式锁挂掉（无法获取锁）
1. 检查Redis服务器状态：首先需要确保Redis服务器正常工作。可以尝试通过telnet命令连接Redis服务器，并使用PING命令检查连接是否正常。如果无法连接或者连接异常，可以尝试重启Redis服务器来恢复正常工作。
    
2. 检查网络连接：如果Redis服务器正常工作，但是无法获取到锁，可能是网络连接存在问题。可以检查网络连接是否正常，包括网络延迟、带宽等。可以使用ping命令或者traceroute命令来检查网络连接是否正常。

3. 重启Redis：如果发现Redis分布式锁挂了，我们可以尝试重启Redis服务来解决问题。在重启之前，需要先确保已备份好重要的数据，并通知系统管理员进行操作。
    
4. ==采用备份机制==：在分布式系统中，可以考虑使用主从复制或者集群模式来提供高可用性和容错能力。当主节点出现故障时，从节点可以接管工作，避免分布式锁挂掉导致系统不可用。

5. ==重试机制==：在获取分布式锁时，可以设置重试机制，即当无法获取到锁时，进行一定次数的重试。这样可以增加获取到锁的机会。可以设置一个重试次数和重试间隔，当达到重试次数后，可以进行其他的处理逻辑。

6. 监控和告警：为了及时发现Redis分布式锁挂了的情况，可以设置监控和告警机制。可以监控Redis服务器的状态，包括连接数、内存使用情况等，并设置告警规则，在异常情况下及时通知管理员。


[Redis 常见面试题 | 小林coding (xiaolincoding.com)](https://xiaolincoding.com/redis/base/redis_interview.html#%E5%A6%82%E4%BD%95%E7%94%A8-redis-%E5%AE%9E%E7%8E%B0%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E7%9A%84)

# 消息队列

- List
- Stream：发布/订阅

# 搜索引擎

- RediSearch：基于 Redis 的搜索引擎模块

##   RediSearch vs Elasticsearch

# 延时任务(延迟队列)
>场景：订单超时取消、红包超时退回

1. Redis 过期事件监听
2. Redisson 内置的延时队列

延迟队列是指把当前要做的事情，往后推迟一段时间再做。延迟队列的常见使用场景有以下几种：

- 在淘宝、京东等购物平台上下单，超过一定时间未付款，订单会自动取消；
- 打车的时候，在规定时间没有车主接单，平台会取消你的单并提醒你暂时没有车主接单；
- 点外卖的时候，如果商家在10分钟还没接单，就会自动取消订单；

在 Redis 可以使用有序集合（ZSet）的方式来实现延迟消息队列的，ZSet 有一个 Score 属性可以用来存储延迟执行的时间。

使用 zadd score1 value1 命令就可以一直往内存中生产消息。再利用 zrangebysocre 查询符合条件的所有待处理的任务， 通过循环执行队列任务即可。


## Redis过期事件监听
Redis 2.0 引入了发布订阅 (pub/sub) 功能。在 pub/sub 中，引入了一个叫做 **channel（频道）** 的概念，有点类似于消息队列中的 **topic（主题）**。

pub/sub 涉及发布者（publisher）和订阅者（subscriber，也叫消费者）两个角色：

- 发布者通过 `PUBLISH` 投递消息给指定 channel。
- 订阅者通过`SUBSCRIBE`订阅它关心的 channel。并且，订阅者可以订阅一个或者多个 channel。
## Redisson延迟队列
Redisson 的延迟队列 RDelayedQueue 是基于 Redis 的 SortedSet 来实现的。SortedSet 是一个有序集合，其中的每个元素都可以设置一个分数，代表该元素的权重。Redisson 利用这一特性，将需要延迟执行的任务插入到 SortedSet 中，并给它们设置相应的过期时间作为分数。

Redisson 使用 `zrangebyscore` 命令扫描 SortedSet 中过期的元素，然后将这些过期元素从 SortedSet 中移除，并将它们加入到就绪消息列表中。就绪消息列表是一个阻塞队列，有消息进入就会被监听到。这样做可以避免对整个 SortedSet 进行轮询，提高了执行效率。

- 减少了丢消息的可能性
- 消息不存在重复消费问题






# 频繁点赞操作
采用redis的集合（set），通过用户ID判断点赞唯一性，利用Redis作为缓存更新，异步写入数据库；
异步通知点赞人，就将点击当成事件放到一个队列中 统一处理即可。