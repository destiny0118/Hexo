# Bomb Lab

查看bomb.c可以看到源代码的大致结构

![image-20211204202050832](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042020986.png)

反汇编main函数：

```sh
disas main
```

![image-20211204202454298](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042024386.png)

根据汇编代码和原函数的对应关系，可以猜测方框处为对应的printf函数，打印出相应位置的字符串。

![image-20211204202746414](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042027447.png)

## phase_1

phase_1首先调用read_line函数，调用函数时返回值保存至%rax寄存器中，之后调用phase_1(input)，在汇编调用中mov %rax, %rdi，%rdi寄存器用于函数调用时的参数传递，因此可以猜测%rdi中为输入的字符串的首地址。

反汇编phase_1，目标是要拆除bomb，则%eax的值应该为0，否则会调用explode_bomb。

![image-20211204203646413](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042036445.png)

反汇编strings_not_equal和其中的string_length

![image-20211204204919118](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042049188.png)

![image-20211204204331046](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042043079.png)

0对应于ASCII码的空字符，空字符是字符串的结束符，因此string_length为判断以%rdi为首地址的字符串的字符串的长度，并用%rax寄存器返回。

所以在strings_not_equal中，首先判断%rsi ，%rdi为首地址的字符串长度是否相同，长度相同的话逐字符比较是否相等，如果两个字符串相等，%rax=0，并返回。%rdi为输入的字符串，则%rsi为要判断是否相等的字符串，找出其地址，在汇编代码phase_1中，为0x402400，gdb查看内容。

![image-20211204205320626](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042053647.png)

![image-20211204205419707](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042054731.png)

## phase_2

查看phase_2的汇编代码

![image-20211204205903327](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042059377.png)

查看调用的read_six_numbers函数汇编代码

![image-20211204213232445](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042132486.png)

![image-20211204213314955](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042133975.png)

可以发现函数读入6个int型数据，其中四个数据的存储位置通过寄存器传递，另外两个通过栈进行传递。

传递函数参数的寄存器

![image-20211204213138427](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042131451.png)

在read_six_numbers中（下图为16进制）：

![image-20211204213209675](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042132770.png)

分析phase_2的汇编代码，先判断(%rsp)处的值是否为1，然后与自己相加，即乘以2后与下一个数比较，依次向下，直到数据末尾。因此要输入的字符串为

```
1 2 4 8 16 32
```

![image-20211204213743046](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112042137075.png)

## phase_3

查看phase_3的汇编代码

![image-20211205112242562](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051122677.png)

在蓝色框中调用了scanf函数，并且返回值要大于1，否则炸弹会爆炸。

![image-20211205112527233](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051125258.png)

可以看到scanf接受两个int值。

设置断点，随便输入两个数测试，发现0xc(%rsp)处为输入的第二个数，0x8(%rsp)处为输入的第一个数。

![image-20211205113446648](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051134698.png)

则phase_3的汇编代码读入两个数后，将输入的第一个数与7比较，小于则跳转到*0x402470(, x, 8)处，x为输入的第一个数。

![image-20211205114250947](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051142973.png)

输入的第一数为1时，跳转到0x400fb9，此处的指令将%eax寄存器的值设为0x137=311，并与第二个数比较。不同则调用explode_bomb。

因此输入为1 311，也可选择输入别的数。

![image-20211205114821636](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051148670.png)

## phase_4

![image-20211205130044173](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051300220.png)

与phase_3一样，这里也要输入两个数，同时还可以看出第一个数小于等于14，第二个数为0。通过寄存器给func4传递了三个参数%edi（输入的第一个数），%esi（0），%edx（0xe=14）

![image-20211205131746044](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051317090.png)

这里递归调用了func4函数，将汇编代码转换成等价的C代码，用a表示%edi的值，b表示%esi的值，c表示%edx的值

用标记t表示蓝色框最后%eax的值，则[（c-b）>>31+（c-b）] >> 1

下面用tmp表示执行完0x400fdf后%ecx的值：

```c++
#include <iostream>
using namespace std;
int fun4(int a, int b, int c)
{
    int tmp = (((c - b) + ((c - b) >> 31)) >> 1) + b;
    if (tmp <= a)
    {
        if (tmp >= a)
            return 0;
        else
            return (fun4(a, tmp + 1, c) * 2 + 1);
    }
    else
        return fun4(a, b, tmp - 1) * 2;
}
int main()
{
    for (int i = 0; i <= 14; i++)
    {
        if (fun4(i, 0, 14) == 0)
            cout << i << " ";
    }
    getchar();
}
```

得到第一个数可能为：0，1，3，7

![image-20211205115347016](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051153046.png)

## phase_5

![image-20211205154040157](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051540229.png)

先从上往下看，第一个红色框设置金丝雀值，然后判断输入的字符串长度是否为6。忽略其中部分代码，蓝色框是可以使函数正常退出的流程，其中比较了0x10(%rsp)处开始的字符串与0x40245e处开始的字符串是否相等。

![image-20211205154748217](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051547240.png)

则函数栈中相应位置为：

![image-20211205160209218](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051602264.png)

分析绿色框中的汇编代码，因为%rbx的值为%rdi，即我们输入的字符串首地址，进行一系列操作，实际到红色框时，%rdx的低位字节的低4位对应于对应的字符值，其余全部置0。重要的是这条指令：

```assembly
and    $0xf,%edx
```

这时是以输入的字符数值大小作为索引，来到0x40108b查找相应的字符，放到位置0x10(%rsp, %rax, 1)处。

![image-20211205161111520](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051611546.png)

这个实验的设置很有意思，哈哈，我们在这个数组中逐字符找到flyers的相应位置下标为：9 F E 5 6 7

这时只要到ASCII码中查找X9, XF, XE, X5, X6, X7相应的可输入字符即可，比如第一个字符可以为 ) (29)    9(39)    I(49)等。

![image-20211205115408234](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051154265.png)

## phase_6

终于到了最后阶段。依旧对汇编代码进行分割，划分成几个阶段。

![image-20211205182158385](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051821451.png)

与phase_2一样，读入6个int型数据，放到%rsi为首地址的地方，下面包含两个循环，外循环依次遍历6个数，内循环将这个数与之后的数比较。

外循环对原数据减1后$\leq$5，则原数据$\leq$6，同时这6个数据相互之间不想等。

![image-20211205190200537](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051902625.png)

![image-20211205191611260](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051937578.png)

这里首先对输入的六个数据进行7-x处理后在放到原位置。下面反复出现0x6032d0这个值。查看内存内容，可以发现其结构类似于结构体链表：

%rdx初始值为0x6032d0，则`mov 0x8(%rdx),%rdx`指令取下一个节点的地址。`	mov    (%rsp,%rsi,1),%ecx`取输入的数据值，如果不是1的话，则递增%eax，同时%rdx指向下一个节点的地址，指导%eax的值与当前的输入数据值相等。并将相应的节点位置放到

`mov    %rdx,0x20(%rsp,%rsi,2)`，因此输入的数据对应了内存位置中的第几个节点。

![image-20211205191926752](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051919795.png)

%rbx=0x20(%rsp)，这段代码保证了%rbx后面相应地址指针所指向的地址内容的低4位字节是递减顺序，这里就很坑，因为高四位字节就是1，2，3，4，5，6，看起来很顺眼。

![image-20211205200728497](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112052007599.png)

得到上图，相应的数据为3 4 5 6 1 2，则原输入数据为：4 3 2 1 6 5

![image-20211205115430850](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202112051154882.png)

