# Linux环境配置

# Linux

## VMware Workstation 16 永久激活密钥

ZF3R0-FHED2-M80TY-8QYGC-NPKYF

YF390-0HF8P-M81RQ-2DXQE-M2UT6

ZF71R-DMX85-08DQY-8YMNC-PPHV8
链接：https://www.jianshu.com/p/002ede3e0b17

## 1. 创建root用户

```
sudo passwd root
su root				//切换到root
```

## 2. 内存管理

| free | 内存容量统计信息 |
| ---- | ---------------- |
|      |                  |

## 3. 进程管理命令



## 虚拟机

windows安装wsl与vmware兼容问题

关闭windows的Hyper-V功能重启电脑就好了。

![img](https://gitee.com/destiny0118/picgo/raw/master/202112311754154.jpeg)

传输 (VMDB)错误 -14: Pipe connection has been broken。

```
bcdedit /set hypervisorlaunchtype off
bcdedit /set hypervisorlaunchtype auto
bcdedit /enum {current}			查看是否启动
```

wsl -l -v

[(10条消息) WSL修改默认安装目录到其他盘_Login5&R的博客-CSDN博客](https://blog.csdn.net/qq_33157391/article/details/120728021)



# Ubuntu root@localhost‘s password: Permission denied,please try again.

> - 修[ssh](https://so.csdn.net/so/search?q=ssh&spm=1001.2101.3001.7020)改配置文件，设置为允许root远程登录：
>
>   root@[ubuntu](https://so.csdn.net/so/search?q=ubuntu&spm=1001.2101.3001.7020):~# vim /etc/ssh/sshd_config
>
>   新增语句：PermitRootLogin yes 即可。
>
> - 保存退出，重启ssh服务：
>
>   root@ubuntu:~# /etc/init.d/ssh restart

# ubuntn

## 1. 问题

> 无法在宿主环境与虚拟环境之间复制粘贴
> 解决方案
>
> 在Ubuntu的命令行中执行一下命令

```shell
sudo apt-get autoremove open-vm-tools
sudo apt-get install open-vm-tools
sudo apt-get install open-vm-tools-desktop
#重启虚拟机
```



## 2. 字体太小

> sudo apt-get install unity-tweak-tool

## 3. fatal error: bits/libc-header-start.h: 没有那个文件或目录

> sudo apt-get install gcc-multilib

## 4. 更新镜像源

> sudo apt-get update
> 这个命令，会访问源列表里的每个网址，并读取软件列表，然后保存在本地电脑。我们在新立得软件包管理器里看到的软件列表，都是通过update命令更新的。
>
> update后，可能需要upgrade一下。
>
> sudo apt-get upgrade
> 这个命令，会把本地已安装的软件，与刚下载的软件列表里对应软件进行对比，如果发现已安装的软件版本太低，就会提示你更新。如果你的软件都是最新版本，会提示：

## 5. 右键创建文件

![image-20211212214603969](https://gitee.com/destiny0118/picgo/raw/master/202112122146080.png)

### 3. vmware-tools为灰色

<img src="https://raw.githubusercontent.com/destiny0118/picgo/master/img/image-20220922095348465.png" alt="image-20220922095348465" style="zoom: 50%;" />



登录虚拟机后安装

> - tar -zxvf 加压缩相应安装包
> - 进入解压缩后的安装包
> - ./vmware-install.pl安装



## Centos7

### 更新阿里源

[(6条消息) CentOS 更新yum源及yum命令详解_TigerwolfC的博客-CSDN博客_centos更新yum源](https://blog.csdn.net/wade3015/article/details/94494929)

[(6条消息) 修改CentOS默认yum源为国内镜像_qq_41954264的博客-CSDN博客](https://blog.csdn.net/qq_41954264/article/details/107392579?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1.no_search_link)

# docker

[docker镜像原理 镜像制作 dockerfile - 李狗蛋+1 - 博客园 (cnblogs.com)](https://www.cnblogs.com/lyx666/p/12764442.html)

[Docker在ubuntu和centos的安装 - 弑小君 - 博客园 (cnblogs.com)](https://www.cnblogs.com/dyb0204/p/11345129.html)

## 1. CentOS Docker 安装

[CentOS Docker 安装](https://www.runoob.com/docker/centos-docker-install.html)

[DockerFile简介]([docker-通过编辑dockerfile自动生成镜像 - An.amazing.rookie - 博客园 (cnblogs.com)](https://www.cnblogs.com/dongzhanyi123/p/13301748.html))

[Docker基本概念]([(6条消息) Docker基本概念_useradd的博客-CSDN博客_docker useradd](https://blog.csdn.net/sehejs_a/article/details/104857028))

[Docker在ubuntu和centos的安装]([Docker在ubuntu和centos的安装 - 弑小君 - 博客园 (cnblogs.com)](https://www.cnblogs.com/dyb0204/p/11345129.html))

docker免密登录

[(6条消息) 新建centos7的虚拟机安装docker后无法启动，报错：Failed to start docker.service: Unit not found_zytmaster的博客-CSDN博客](https://blog.csdn.net/zytmaster/article/details/106170908)

## 2. Singularity Container

[Singularity Container]([Quick Start — Singularity container 2.6 documentation (sylabs.io)](https://sylabs.io/guides/2.6/user-guide/quick_start.html))

[singularity]([singularity - 简书 (jianshu.com)](https://www.jianshu.com/p/5b5e2f1bd057?from=singlemessage))

[Singularity and MPI applications 官网文件](https://sylabs.io/guides/3.3/user-guide/mpi.html))

[Singularity和MPI应用 简书]([Singularity和MPI应用 - 简书 (jianshu.com)](https://www.jianshu.com/p/0882e65c5e0e))

[openMPI安装系统环境变量、个人环境变量Open MPI安装使用 ](http://hmli.ustc.edu.cn/doc/mpi/openmpi-install.htm))

def文件更新慢

[(6条消息) 从零开始制作PyTorch的Singularity容器镜像_nidongla的博客-CSDN博客](https://blog.csdn.net/nidongla/article/details/117930231?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~default-1.no_search_link&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~default-1.no_search_link)

