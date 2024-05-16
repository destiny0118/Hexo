## strpbrk
>检索字符串 **str1** 中第一个匹配字符串 **str2** 中字符的字符，不包含空结束字符。依次检验字符串 str1 中的字符，当被检验字符在字符串 str2 中也包含时，则停止检验，并返回该字符位置。
```cpp
char *strpbrk(const char *str1, const char *str2)
```
- **str1** -- 要被检索的 C 字符串。
- **str2** -- 该字符串包含了要在 str1 中进行匹配的字符列表。
## strcasecmp
>判断字符串是否相等的函数，忽略大小写。s1和s2中的所有字母字符在比较之前都转换为小写。该strcasecmp()函数对空终止字符串进行操作。
>函数的字符串参数应包含一个(’\0’)标记字符串结尾的空字符。
```cpp
int strcasecmp (const char *s1, const char *s2);
```

## strspn
>检索字符串 **str1** 中第一个不在字符串 **str2** 中出现的字符下标
```cpp
size_t strspn(const char *str1, const char *str2)
```
- **str1** -- 要被检索的 C 字符串。
- **str2** -- 该字符串包含了要在 str1 中进行匹配的字符列表。
- 返回 str1 中第一个不在字符串 str2 中出现的字符下标。

```cpp
#include <iostream>
#include <string.h>
using namespace std;
int main() {
    char *s1 = "abcdef ghj\tio";
    char *s2 = " \t";
    char *s3 = "ghj";
    cout << *(s1 + 7) << endl;
    char *res = strpbrk(s1 + 7, s2);

    cout << strspn(s1 + 7, s3) << endl;
    cout << res << endl;
    return 0;
}

g
3
\tio
```
## strchr
>在参数 **str** 所指向的字符串中搜索第一次出现字符 **c**（一个无符号字符）的位置。

```cpp
char *strchr(const char *str, int c)
```
- **str** -- 要查找的字符串。
- **c** -- 要查找的字符。
- 如果在字符串 str 中找到字符 c，则函数返回指向该字符的指针，如果未找到该字符则返回 NULL。

## strrchr
>在参数 **str** 所指向的字符串中搜索最后一次出现字符 **c**（一个无符号字符）的位置。
```cpp
char *strrchr(const char *str, int c)
```
- **str** -- C 字符串。
- **c** -- 要搜索的字符，通常以整数形式传递（ASCII 值），但是最终会转换回 char 形式。
- strrchr() 函数从字符串的末尾开始向前搜索，直到找到指定的字符或搜索完整个字符串。如果找到字符，它将返回一个指向该字符的指针，否则返回 NULL。
## strcpy
>把 **src** 所指向的字符串复制到 **dest**。
>需要注意的是如果目标数组 dest 不够大，而源字符串的长度又太长，可能会造成缓冲溢出的情况。
```cpp
char *strcpy(char *dest, const char *src)
```

## strncpy
>把 **src** 所指向的字符串复制到 **dest**，最多复制 **n** 个字符。当 src 的长度小于 n 时，dest 的剩余部分将用空字节填充。

```cpp
char *strcpy(char *dest, const char *src)
```
- **dest** -- 指向用于存储复制内容的目标数组。
- **src** -- 要复制的字符串。
- **n** -- 要从源中复制的字符数。
- 该函数返回最终复制的字符串。




## stdio.h
## flush
>刷新流 stream 的输出缓冲区，将缓冲区中的内容写到stream所指的文件中区

```cpp
#include <stdio.h>
int fflush(FILE *stream)
```
- **stream** -- 这是指向 FILE 对象的指针，该 FILE 对象指定了一个缓冲流
- 如果成功，该函数返回零值。如果发生错误，则返回 EOF，且设置错误标识符（即 feof）。

[fflush()函数-CSDN博客](https://blog.csdn.net/ShenHang_/article/details/114968557)


## snprintf
>用于格式化输出字符串，并将结果写入到指定的缓冲区，与 sprintf() 不同的是，snprintf() 会限制输出的字符数，避免缓冲区溢出。
>将可变参数**(...)**按照 **format** 格式化成字符串，并将字符串复制到 **str** 中，**size** 为要写入的字符的最大数目，超过 **size** 会被截断，最多写入 size-1 个字符，并在字符串的末尾添加一个空字符（\0）以表示字符串的结束。

```cpp
int snprintf ( char * str, size_t size, const char * format, ... );
```
- **str** -- 目标字符串，用于存储格式化后的字符串的字符数组的指针。
- **size** -- 字符数组的大小。
- **format** -- 格式化字符串。
- **...** -- 可变参数，可变数量的参数根据 format 中的格式化指令进行格式化。
- snprintf() 函数的返回值是输出到 str 缓冲区中的字符数，不包括字符串结尾的空字符 \0。
## stat
>获取文件状态

[C语言stat()函数：获取文件状态 - 极客先锋 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jikexianfeng/p/5742887.html)
```cpp
#include<sys/stat.h>  
#include<uninstd.h>
//将参数file_name 所指的文件状态, 复制到参数buf 所指的结构中。
int stat(const char * file_name, struct stat *buf)
struct stat {
    dev_t st_dev; //device 文件的设备编号
    ino_t st_ino; //inode 文件的i-node
    mode_t st_mode; //protection 文件的类型和存取的权限
    nlink_t st_nlink; //number of hard links 连到该文件的硬连接数目, 刚建立的文件值为1.
    uid_t st_uid; //user ID of owner 文件所有者的用户识别码
    gid_t st_gid; //group ID of owner 文件所有者的组识别码
    dev_t st_rdev; //device type 若此文件为装置设备文件, 则为其设备编号
    off_t st_size; //total size, in bytes 文件大小, 以字节计算
    unsigned long st_blksize; //blocksize for filesystem I/O 文件系统的I/O 缓冲区大小.
    u nsigned long st_blocks; //number of blocks allocated 占用文件区块的个数, 每一区块大小为512 个字节.
    time_t st_atime; //time of lastaccess 文件最近一次被存取或被执行的时间, 一般只有在用mknod、 utime、read、write 与tructate 时改变.
    time_t st_mtime; //time of last modification 文件最后一次被修改的时间, 一般只有在用mknod、 utime 和write 时才会改变
    time_t st_ctime; //time of last change i-node 最近一次被更改的时间, 此参数会在文件所有者、组、 权限被更改时更新
};
```





# 函数参数处理
## va_start
>初始化 **ap** 变量，它与 **va_arg** 和 **va_end** 宏是一起使用的。**last_arg** 是最后一个传递给函数的已知的固定参数，即省略号之前的参数。
```cpp
#include <stdarg.h>
void va_start(va_list ap, last_arg);
```
- **ap** -- 这是一个 **va_list** 类型的对象，它用来存储通过 **va_arg** 获取额外参数时所必需的信息。
- **last_arg** -- 最后一个传递给函数的已知的固定参数。

```cpp
//sum(3,10,20,30)
int sum(int num_args, ...)
{
   int val = 0;
   va_list ap;
   int i;

   va_start(ap, num_args);
   for(i = 0; i < num_args; i++)
   {
      val += va_arg(ap, int);
   }
   va_end(ap);
 
   return val;
}
```

## vsnprintf
[sprintf、snprintf、vsprintf、vsnprintf格式化函数分析_c++ sprintf vsprintf详解-CSDN博客](https://blog.csdn.net/u012351051/article/details/106414950)
>使用参数列表发送格式化输出到字符串

```cpp
int vsnprintf(char *str, size_t size, const char *format, va_list ap);
```
- str -- 目标字符串。 
- size -- 最大格式化的字符长度。 
- format -- 格式化模式 
- arg -- 可变参数列表对象，应由\<stdarg\>中定义的va_start 宏初始化。