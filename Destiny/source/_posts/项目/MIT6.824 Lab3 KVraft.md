---
title: MIT6.824 Lab3 KVraft
date:
---

# Bug
### 1. `panic: close of closed channel`
在server中，只使用了一个全局的管道来接收命令应用结果，PutAppend和Get共享一个管道，两个分别打开管道，随后一个关闭，另一个在关闭时出现问题
- 给两个操作加锁，只有操作执行完（成功执行，超时）才解锁
- 对Start返回的index，每一个添加一个管道
## 2. command被复制到半数以上服务器，但是还没有在状态机上执行，然后选举出新的leader，新的leader具有添加的命令

在Lab2就想到的问题，一直考虑已经被添加的command如何被再次添加，发现在raft层无法解决这个问题。

在Lab3中遇到了此问题，考虑在server中记录client已经添加的命令（命令可能未执行成功），在收到更小的command时，则不调用Start添加到leader。这需要启用新的server作为leader时，快速将已有的命令执行完毕（在添加任何新命令之前），这一步难以实现。

因此考虑在command中添加client的命令标志，重传的命令也可以被添加到log中，但是在执行时会发现该命令已经执行过



命令没有被执行 TestManyPartitionsManyClients3A

```
=== RUN   TestManyPartitionsManyClients3A
Test: partitions, many clients (3A) ...

```



命令结果有额外部分（多执行了？）TestConcurrent3A



```
Test: unreliable net, restarts, partitions, random keys, many clients (3A) ...
info: wrote history visualization to /tmp/1021001197.html
    test_test.go:382: history is not linearizable
--- FAIL: TestPersistPartitionUnreliableLinearizable3A (30.13s)
```

## 3. 并发读写map

`fatal error: concurrent map read and map write`

```go
replyCh := make(chan ApplierResult, 1)
kv.appliedCh[index] = replyCh
//kv.appliedCh[index] = make(chan ApplierResult, 1)
```

