# 5 Linux基础API
## socket地址API
发送端总是把要发送的数据转化为大端字节序数据然后再发送，大端字节序也称为网络字节序。

## 5.8 数据读写
```cpp
#include <sys/types.h>
#include <sys/socket.h>
//TCP数据读写
ssize_t recv(int sockfd, void *buf, size_t len,int flags)
ssize_t send(int sockfd, const void *buf, size_t len,int flags)
```
- recv读取sockfd的数据，buf和len分别指定读缓冲区的位置和大小，flags通常设置为0；recv成功时返回实际读取到的数据的长度
- send往sockfd写数据，send成功时返回实际写入数据的长度
- flags为数据收发提供了额外的控制
## readv、writev






# 8. 高性能服务器程序框架
## 8.3 I/O模型

对accept、send和recv而言，事件未发生时error通常被设置为
- EAGAIN（再来一次）
- EWOULDBLOCK（期望阻塞）
对connect而言，error被设置为
- EINPROGRESS（在处理中）




# 11 定时器

## 信号
- SIGALRM：有alarm和setitimer函数设置的实时闹钟一旦超时，将触发SIGALARM信号。因此，可以利用该信号的信号处理函数来处理定时任务。
- SIGTERM：

