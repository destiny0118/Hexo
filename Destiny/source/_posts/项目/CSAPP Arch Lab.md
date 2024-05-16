# Arch Lab

## 环境准备

![image-20211130101913363](https://gitee.com/destiny0118/picgo/raw/master/202112022026699.png)

需要先安装flex、bison，直接安装就好。

安装tcl/tk

```
sudo apt install tcl tcl-dev tk tk-dev
```

需要修改相应makefile文件，不需要GUI界面的话第一个就注释掉，第二个需要修改路径。通过命令行直接安装的话时8.6版本，而原来的路径为8.5的，会出现找到到相应文件的问题。后面相应实验出现问题都要修改相应的makefile文件。

![image-20211202210850852](https://gitee.com/destiny0118/picgo/raw/master/202112022108921.png)

## Part A

### sum.ys 迭代求和

```assembly
.pos 0
	irmovq stack, %rsp
	call main
	halt

# Sample linked list 
.align 8
ele1:
	.quad 0x00a 
	.quad ele2
ele2:
	.quad 0x0b0 
	.quad ele3
ele3:
	.quad 0xc00 
	.quad 0

main:
	irmovq ele1, %rdi
	call sum_list
	ret

sum_list:
	xorq %rax, %rax     #sum=0
	andq %rdi, %rdi	    #set CC, test is pointer=0
	jmp test
loop:
	mrmovq (%rdi), %r9	    #element value
	addq %r9, %rax
	mrmovq 8(%rdi), %rdi
	andq %rdi, %rdi
test:
	jne loop
	ret

	.pos 0x200
stack:
```

![image-20211202202601014](https://gitee.com/destiny0118/picgo/raw/master/202112022026098.png)

### rsum.ys 递归求和

```assembly
	.pos 0
	irmovq stack, %rsp
	call main
	halt

# Sample linked list 
.align 8
ele1:
	.quad 0x00a 
	.quad ele2
ele2:
	.quad 0x0b0 
	.quad ele3
ele3:
	.quad 0xc00 
	.quad 0

main:
	irmovq ele1, %rdi
	andq %rax, %rax
	call rsum_list
	ret

rsum_list:
	jmp test
add:	mrmovq (%rdi),%r8
	mrmovq 8(%rdi), %rdi
	pushq %r8
	call rsum_list
	popq %r8
	addq %r8, %rax
test:
	andq %rdi, %rdi
	jne add
	ret	


	.pos 0x200
stack:
```

在递归调用前先保存当前内存位置值到%r8寄存器中，再保存到栈中。

![image-20211202202836226](https://gitee.com/destiny0118/picgo/raw/master/202112022028268.png)

### copy.ys 复制到目标位置

```assembly
	.pos 0
	irmovq stack, %rsp
	call main
	halt

	.align 8
# Source block 
src:
	.quad 0x00a 
	.quad 0x0b0 
	.quad 0xc00

# Destination block 
dest:
	.quad 0x111 
	.quad 0x222 
	.quad 0x333


main:
	irmovq src, %rdi
	irmovq dest, %rsi
	irmovq $3, %rdx

	irmovq $1, %r9
	irmovq $8, %r10

	call copy_block
	halt

copy_block:
	xorq %rax, %rax		# result=0
	andq %rdx, %rdx		# set CC
	jmp test
loop:
	mrmovq (%rdi), %r8
	addq %r10, %rdi		# val=*src++

	rmmovq %r8, (%rsi)
	addq %r10, %rsi
	xorq %r8, %rax
	subq %r9, %rdx

test:
	jne loop
	ret

	.pos 0x200
stack:
	
```

![image-20211202203108136](https://gitee.com/destiny0118/picgo/raw/master/202112022031188.png)

## Part B

参考OPq rA, rB和irmovq V, rB指令的执行阶段

![image-20211124161712200](https://gitee.com/destiny0118/picgo/raw/master/202112022035063.png)

iaddq执行数据流

![image-20211202152108525](https://gitee.com/destiny0118/picgo/raw/master/202112021521713.png)

在instr_valid、need_regids、need_valC

srcB、dstE

aluA、aluB、setCC

相应位置添加IIADDQ

### 测试

![image-20211202204245751](https://gitee.com/destiny0118/picgo/raw/master/202112022042837.png)

```
make SIM=../seq/ssim TFLAGS=-i
```

![image-20211202204322969](https://gitee.com/destiny0118/picgo/raw/master/202112022043011.png)

## Part C

未修改：

Average CPE: 15.18

添加iaddq指令，向Part B那样添加iaddq指令后修改代码，得到CPE: 12.70

```assembly
# You can modify this portion
	# Loop header
	xorq %rax,%rax		# count = 0;
	andq %rdx,%rdx		# len <= 0?
	jle Done		# if so, goto Done:

Loop:	mrmovq (%rdi), %r10	# read val from src...
	rmmovq %r10, (%rsi)	# ...and store it to dst
	andq %r10, %r10		# val <= 0?
	jle Npos		# if so, goto Npos:
	iaddq $1, %rax		# count++
Npos:	
	iaddq $-1, %rdx		# len--
	iaddq $8, %rdi		# src++
	iaddq $8, %rsi		# dst++
	andq %rdx,%rdx		# len > 0?
	jg Loop			# if so, goto Loop:
```

Average CPE: 12.70

采用循环展开，通过增加每次迭代计算的元素的数量，减少循环的迭代次数。

要减少CPE，需要尽可能充分利用时钟周期，即让每个时钟周期执行对我们的结果有意义的指令。

```
mrmovq (%rdi), %r10	# read val from src...
rmmovq %r10, (%rsi)	# ...and store it to dst
```

第二条指令在译码阶段需要读取%r10寄存器中的值，但第一条指令直到访存阶段才能读取出应该放到%r10寄存器中的值，出现加载/使用数据冒险，编译器会在此间加入一个气泡，可以利用这个时钟周期，执行有意义的指令，预先读出后面内存中的值来提高时钟周期的利用率。

这里采用四次循环展开，前面4个为一组处理后，剩下的长度可能为1,2,3。通过在Recover中恢复剩余的元素个数。如果小于等于0的话，说明初始长度为4的倍数，直接返回，否则还要复制相应个数的元素。

```ass
# You can modify this portion
	# Loop header
	xorq %rax,%rax		# count = 0;
	iaddq $-4,%rdx		# len <= 0?
	jl Recover		# if so, goto Done:

Loop4:	mrmovq (%rdi), %r10	# read val from src...
	mrmovq 8(%rdi), %r11
	mrmovq 16(%rdi), %r12

	rmmovq %r10, (%rsi)	# ...and store it to dst
	andq %r10, %r10		# val <= 0?
	jle Npos		# if so, goto Npos:
	iaddq $1, %rax		# count++
Npos:	
	rmmovq %r11, 8(%rsi)
	andq %r11, %r11
	jle Npos1
	iaddq $1, %rax
Npos1:
	mrmovq 24(%rdi), %r13
	rmmovq %r12, 16(%rsi)
	andq %r12, %r12
	jle Npos2
	iaddq $1, %rax
Npos2:
	rmmovq %r13, 24(%rsi)
	andq %r13, %r13
	jle Npos3
	iaddq $1, %rax
Npos3:
	iaddq $32, %rdi		# src++
	iaddq $32, %rsi		# dst++
	iaddq $-4, %rdx		# len-- len > 0?
	jge Loop4		# if so, goto Loop:





Recover:
	iaddq $4,%rdx		# len <= 0?
	jle Done

	mrmovq (%rdi), %r10	# read val from src...
	mrmovq 8(%rdi), %r11
	
	rmmovq %r10, (%rsi)	# ...and store it to dst
	andq %r10, %r10		# val <= 0?
	jle Npos4		# if so, goto Npos:
	iaddq $1, %rax		# count++
Npos4:	
	iaddq $-1, %rdx
	jle Done 

	rmmovq %r11, 8(%rsi)
	andq %r11, %r11
	jle Npos5
	iaddq $1, %rax
Npos5:
	iaddq $-1, %rdx
	jle Done 
	mrmovq 16(%rdi), %r12
	rmmovq %r12, 16(%rsi)
	andq %r12, %r12
	jle Done
	iaddq $1, %rax
##################################################################
# Do not modify the following section of code
# Function epilogue.
Done:
	ret
```

这里会有条件跳转，在pipe-full.hcl的描述中，是跳转到跳转条件满足的位置，即跳转到Done，但Done后面紧跟ret返回，而大多数情况是跳转到跳转条件不满足的位置，继续向下执行。因此如果跳转到Done执行后面的指令会浪费时钟周期，而如果继续向下执行，读取内存中的值时，如果之后需要用到，则可以利用当前的时钟周期。修改pipe-full.hcl为分支条件不满足的位置，即jmp后面的那条指令位置。

![image-20211202210413232](https://gitee.com/destiny0118/picgo/raw/master/202112022104261.png)

![image-20211202210453153](https://gitee.com/destiny0118/picgo/raw/master/202112022104201.png)

![image-20211202210506511](https://gitee.com/destiny0118/picgo/raw/master/202112022105552.png)

最终Score: 60