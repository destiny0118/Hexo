term(任期)

Leader发出heartbeat(AppendEntries RPC不带有log entries)

>Raft is a consensus algorithm that is designed to be easy to understand. It’s equivalent to Paxos in fault-tolerance and performance.

![QQ图片20240205215524.jpg](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202402052155568.jpg)

# 2A Leader election(领导人选举)

- 网络延迟、分区、包丢失、复制和重新排序。

> This election term will continue until a follower stops receiving heartbeats and becomes a candidate.

导致Follower进行选举的原因

- 网络延迟或者包丢失没有在选举超时前收到心跳
- 网络分区而导致收不到心跳（disconnect）
- Leader宕机、崩溃（crash）

## 1. 节点类型

- `Leader`：负责发起心跳，响应客户端，创建日志，同步日志。
- `Candidate`：Leader 选举过程中的临时角色，由 Follower 转化而来，发起投票参与竞选。
- `Follower`：接受 Leader 的心跳和日志同步数据，投票给 Candidate。态转换

![image-20240203110252535](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202402031102578.png)

- Follower$\rightarrow$Candidate（超时：一段时间内未收到heartbeat）



Leader(AppendEntries RPC)

- term过时：
	- 发送心跳发现Server的term号大于自身的term号（已经选出新的Leader，将自己状态变为Follower）
	- 收到RequestVote发现更高term号


Candidate(RequestVote RPC)

- term过时
	- 收到新的Leader的心跳，请求投票时发现大于自身的term号
	- 收到RequestVote RPC的response，返回的term号更大

Follower：被动的，对来自Leader和Candidate的请求进行相应

- 收到Leader的心跳，且term号大于自身term号（更新自身term号）
- 收到Candidate请求

Server：

- 拒绝过时term的请求

## 选举超时时间和心跳时间

- 心跳间隔时间(heartbeat timeout)：Leader 会向所有的 Follower 周期性发送心跳来保证自己的 Leader 地位。

- 选举超时时间(election timeout)：如果一个 Follower 在一个周期内没有收到心跳信息或者请求投票信息，就叫做选举超时，并开始一次新的选举。

这两个时间需要保持一定的关系，网络无故障时，在选举超时前应该收到心跳，以保持Leader不变。选举超时时间至少需要大于AppendEntries RPC发送到server所需的最长时间。


### 选举超时时间更新
- candidate成为leader
- candidate收到RequestVote response并变为follower
- server收到AppendEntries
- server收到RequestVote
- leader收到AppendEntries response发现更高term号

### 超时选举实现
在raft结构体中定义laskAcktime，在收到leader的heartbeat或者candidate的投票请求时，要更新选举超时时间
这里没有通过定时器在到达选举超时时间后触发选举操作，而是首先记录下当前时间，然后让ticker协程sleep一段时间。当再次唤醒后，如果laskAcktime在startTime之后，说明在选举超时前收到了相关信号。
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202402272153075.png)


## 注意点
### `sendAppendEntries`没有启用新的协程
在`sendHeartbeat`中，异步发送`sendAppendEntries`，向所有server发送heartbeat，无需等待RPC完成。因为leader可能无法与其他server通信，或者server不可达，由于等待rpc返回，造成超时重新选举

异步发送`RequestVote`，并且收到超过半数选票后就成为Leader，发送心跳


` go test -race -run 2A`
# 2B Log replication(日志复制)

>==**Leader Completeness**==: if a log entry is committed in a given term, then that entry will be present in the logs of the leaders for all higher-numbered terms. §5.4
>==**State Machine Safety**==: if a server has applied a log entry at a given index to its state machine, no other server will ever apply a different log entry for the same index. §5.4.3

- leader收到client请求，将entry添加到log
- leader将添加的entry复制到其他server
- 如果大多数server成功复制entry，则该entry已经committed
- 
- 已经committed的entry需要应用到机器上，lastApplied代表已经执行的命令

![Raft-第 1 页.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202402061214812.png)

## Election restriction(5.4.1)
![Raft-第 2 页.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202402041429181.png)

log backtracking

![Raft-加速log回溯](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202402061305538.png)

S1首先成为leader，成功发送了第一个entry

S1和S2与其他服务器断开后重连



## 注意点
>遇到的bug

s1断联，添加了一系列entry

重连后，新的leader通过heartbeat更新了s1的commitindex，而此时s1的log还未更新





# 2C

```go
persist
// Your code here (2C).
// Example:
// w := new(bytes.Buffer)
// e := labgob.NewEncoder(w)
// e.Encode(rf.xxx)
// e.Encode(rf.yyy)
// data := w.Bytes()
// rf.persister.SaveRaftState(data)
readPersist
// Your code here (2C).
// Example:
// r := bytes.NewBuffer(data)
// d := labgob.NewDecoder(r)
// var xxx
// var yyy
// if d.Decode(&xxx) != nil ||
//    d.Decode(&yyy) != nil {
//   error...
// } else {
//   rf.xxx = xxx
//   rf.yyy = yyy
// }
```



## Bug

同一任期选出两个leader

乱序收到RPC response

AppendEntries的RPC response与当前term不一致



在变成follower时都重置了election time





# 2D

![image-20240215120732611](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202402151207733.png)



## bug

1. 每10条command创建一个快照，快照会调用rf.mu这个互斥锁，当存在新的提交命令时，通过applyCh管道进行发送，而测试程序调用snapshot需要获取互斥锁，无法读取管道中的数据，则会产生死锁

2. `apply error: server 2 apply out of order, expected index 10, got 18`

   当snapshot存在未commit的命令时，snapshot和log分别放入applyCh，应当一次性放入applyCh

1. 






| result | the time that the test took in seconds | the number of Raft peers | the number of RPCs sent during the test | the total number of bytes in the RPC messages | the number of log entries that Raft reports were committed |
| ------ | -------------------------------------- | ------------------------ | --------------------------------------- | --------------------------------------------- | ---------------------------------------------------------- |
| PASSED | 3.9                                    | 3                        | 490                                     | 154736                                        | 207                                                        |

# 参考

[Raft Consensus Algorithm 官网介绍](https://raft.github.io/)

[Raft (thesecretlivesofdata.com)](http://thesecretlivesofdata.com/raft/)（Raft图示）

[Raft作者博士论文](https://raw.githubusercontent.com/ongardie/dissertation/master/book.pdf)

[Instructors' Guide to Raft](https://link.zhihu.com/?target=https%3A//thesquareplanet.com/blog/instructors-guide-to-raft/)[4]

[Students' Guide to Raft](https://link.zhihu.com/?target=https%3A//thesquareplanet.com/blog/students-guide-to-raft/)[5]

[Raft Q&A](https://link.zhihu.com/?target=https%3A//thesquareplanet.com/blog/raft-qa/)[6]

paxos 2015版的lab

[hashicorp/raft: Golang implementation of the Raft consensus protocol (github.com)](https://github.com/hashicorp/raft)

[如何的才能更好地学习 MIT6.824 分布式系统课程？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/29597104)
