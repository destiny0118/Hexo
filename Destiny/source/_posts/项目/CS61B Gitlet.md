---
title: "CS61B: Gitlet"
date: 2023-1-18
description: Gitlet是一个版本控制系统，实现了git的一些特性和功能，相比git在部分功能和实现上进行了简化。支持add、commit、log、checkout、merge等本地仓库操作，同时实现push、fetch、pull等远端仓库命令操作。利用java序列化的方法实现commit和stage对应数据结构的持久化存储，通过sha1算法计算相应的哈希值实现内容可寻址。实现字典树存储commit哈希值，实现根据6位前缀快速查找对应的commit哈希值。
categories: project
top: 100
mathjax: true
---

Gitlet支持的主要功能：
- Saving the contents of entire directories of files. In Gitlet, this is called ==committing==, and the saved contents themselves are called ==commits==.

- Restoring a version of one or more files or entire commits. In Gitlet, this is called ==checking out== those files or that commit.

- Viewing the history of your backups. In Gitlet, you view this history in something called the ==log==.

- Maintaining related sequences of commits, called ==branches==.

- ==Merging== changes made in one branch into another.

# 概述
HEAD(the head pointer)跟踪当前分支的最新commit

线性的提交commit是链表数据结构
![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202307271134364.png)
可以通过Branch构成Tree结构
![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202307271136987.png)

detatched head state：当checkout到一个特定当前分支过时的commit

# 内部结构(Internal Structures)

- **blobs**：保存的文件内容，Gitlet会保存文件的不同版本，一个文件可能对应多个`blob`。
- **trees**：目录结构将文件名映射到`blob`和其他`tree`（子目录）
- **commits**：包含`log`信息，其他元数据（提交日期、作者等），对`tree`的引用，对父节点的引用

Gitlet在Git基础上进行了简化：
- 将`trees`嵌入到`commits`中；即在commits中通过哈希表记录文件名到`blob`的映射关系
- 只能对两个节点进行`merge`操作；
- 限制`commits`包括的数据信息。包括log message、timestamp、(file name, blob reference)、parent reference、second parent reference（for merge）

![image-20230728103620289](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202307281036355.png)
每个blob和每个commit都有一个独特的id作为对对象的引用，将blob和commit序列化(将任意的结构变为连续字节流)为字节流，通过加密哈希函数(SHA-1)对于一个序列的字节流计算160bit的整数哈希值。哈希值根据文件内容计算而来，可以用于文件寻址，或者比较文件内容是否相同。
- repository：版本库，存储提交的不同版本文件
- **working directory**：工作区，除了`.gitlet`之外的文件
- **staging area**

# gitlet仓库结构
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202403112034856.png)

# merge
![CS61B-pro2.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202311151547732.png)

# Gitlet Commands

`java gitlet.Main command args`
## init
创建新的版本控制系统，自动以一个默认提交`initial commit`开始，默认有一个`master`分支。`HEAD`指向`heads/master`分支，即存储`heads/master`内容；创建默认`commit`，将`id`存入`refs/heads/master`分支。

当前目录已经存在一个版本控制系统时，报错`A Gitlet version-control system already exists in the current directory.`。

## add
- 将当前工作区的文件副本添加到暂存区(staging area)，不存储文件实体，只记录根据文件内容得到的SHA值
- 文件已经在暂存区，用新内容覆盖暂存区的文件
- 工作区文件内容与head commit跟踪的一样，不添加到暂存区，暂存区如果存在则移除；如果文件在暂存区处于待移除状态，文件不在被暂存为移除状态(removal)
  $$
  version\:1 \rightarrow version\:2 \rightarrow add \rightarrow version\:1 \rightarrow add
  $$
- 文件不处于工作区：`File does not exist.`
- 区别：gitlet一次只能添加一个文件


> 得到文件内容的hash值
> 得到暂存区跟踪文件条目(addition、removal)
> 文件内容与head跟踪的文件不一样(或head没有跟踪此文件)
> ​		更新暂存区(addition)相关条目
> 否则
> ​		将文件移除暂存区(addition)
> 
>(如果文件存在于暂存区(removal)，移除文件)
> 将暂存区状态写回文件

## commit

> 保存HEAD和暂存区中跟踪文件的快照以便将来恢复，创建一个新的`commit`

- 每个commit跟踪的文件默认与父节点相同
- 通过add添加的文件更新文件版本(更新了父节点跟踪的文件)
- 添加添加到暂存区但未被父节点跟踪的文件
- 通过rm移除的文件解除跟踪
失败情况：
- 没有文件处于暂存区：`No changes added to the commit.`
- 提交信息为空：`Please enter a commit message.`
- commit命令不添加、修改、移除工作区中的文件
- ==在add、rm命令后对文件的修改将被commit忽略==(此时checkout为head时，不改变工作区内容，实际HEAD commit内容与当前工作区不同)

>读取暂存区addition，removal
>读取父节点commit跟踪的文件parentFile，文件项为(filename, id)，id为SHA-1值
>将addition中的文件项添加到parentFile中
>将removal中的文件从parentFile中移除
>
>清空暂存区
>将commit和stage写回文件
## rm
> rm时，只是暂时记录在暂存区，commit时，才根据stage修改commit跟踪的文件
- 文件位于暂存区，处于待添加状态，将文件移除暂存区(addition)
- 文件被head commit跟踪，将文件从工作区移除，添加到暂存区(removal)  
- 文件不处于暂存区，也未被head commit跟踪，`No reason to remove the file.`

- 实现时暂存区(removal)采用哈希集合实现，文件如果被修改过，和head commit不一样，这时无法判断，因为没有记录文件内容SHA-1值
- 被移除的文件(removal)是否可以再次添加（无法直接通过文件名添加，但可以再次创建同样文件内容文件添加）

>得到暂存区跟踪文件条目(addition、removal)
>文件不在暂存区(addition)，也未被父节点跟踪
>	`No reason to remove the file.`
>文件在暂存区(addition)中
>	从addition中移除
>否则
>	添加到removal中
>	==从工作区中移除文件==
## log
>从HEAD指向的commit开始，沿着commit tree；对于merge commit(由两个commit合并得来)，则沿着第一个父节点commit，打印commit历史记录
![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202311052138169.png)

>读取HEAD指向的commit id
>从当前commit id进行深度优先搜索`CommitGraph.DFS(commit)`

## global-log
>打印所有的commit信息

因为实现时没有将commit和blob分开存放，所以无法通过迭代一个目录下所有文件的方式实现。作为替代，存储了数据结构`commit-graph`数据结构，包含所有的commit

`Map<String, LinkedList<String>> g`包含了节点到父节点的信息

## find
>输出具有相同commit message信息的commit id

复用了global-log相关代码，添加`findMessage`标记

## status
>显示所有分支和暂存区信息
- Branches
- Staged Files(addition)
- Removed Files(removal)
- Modifications Not Staged For Commit
	- 添加到addition，后续修改文件内容导致与工作区中文件不同(modified)
	- 添加到addition，但从工作区删除(deleted)
	- ==被current commit跟踪，在工作区中修改后，没有暂存==（没有添加到removal）(modified)
	- 被current commit跟踪，没有添加到removal，从工作区删除(deleted)
- Untracked Files
	- 存在于工作区，没有添加到addition，也没有被current commit跟踪
	- 添加到removal，后续重新创建了文件

## checkout
>1. `java gitlet.Main checkout -- [file name]`：用HEAD commit中的文件替换工作区中的文件
>2. `java gitlet.Main checkout [commit id] -- [file name]`：用指定id的commit中的文件替换工作区中的文件
>3. `java gitlet.Main checkout [branch name]`：将给定branch的最新提交的文件签出到工作区，给定的branch签出后将被认为当前branch。被当前branch跟踪但没有被check-out branch跟踪的文件将被删除。

工作区中的文件未被跟踪，在签出后将被覆盖
- `There is an untracked file in the way; delete it, or add and commit it first.`
- 被cur commit跟踪，但后续修改文件导致文件内容变化，同时被checkout commit跟踪
- 文件在工作区，但cur commit未跟踪，checkout commit跟踪

 **Differences from real git**: Real git does not clear the staging area and stages the file that is checked out. Also, it won’t do a checkout that would overwrite or undo changes (additions or removals) that you have staged.

- 针对第二种命令格式，实现`checkoutCommit(String commitID, String filename)`，签出指定commit的对应文件到当前工作区
- 第一种命令，先查询当前分支的commitID，再调用`checkoutCommit(String commitID, String filename)`
- 第三种命令
	- 先检查是否存在未被跟踪的文件
	- 读取branch对应的commit所跟踪的文件列表
	- 一次签出每一个文件
	- 将branch信息写回到HEAD中

## branch
>用指定名字创建一个新的分支，新的分支指向HEAD commit，一个分支仅仅是对commit节点的引用
![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202311061020077.png)


![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202311061021668.png)
> All that creating a branch does is to give us a new pointer. At any given time, one of these pointers is considered the currently active pointer, also called the HEAD pointer (indicated by *). We can switch the currently active head pointer with `checkout [branch name]`. Whenever we commit, it means we add a child commit to the currently active HEAD commit even if there is already a child commit.


## rm-branch
>删除指定分支，即删除heads目录下存储的与分支相关联的指针

`java gitlet.Main rm-branch [branch name]`
删除指定的branch，只是删除与branch相关联的指针（存储在refs/heads/\*下的文件，指向当前branch的最新提交)。
- branch不存在时，`A branch with that name does not exist.`
- 移除当前所在的分支，`Cannot remove the current branch.`





## reset
`java gitlet.Main reset [commit id]`
>签出指定commit的文件
>移除工作区中被tracked但不在指定签出commit的文件
>当前branch的头节点指向指定commit

- 与checkout第三种一样，也许检查，``There is an untracked file in the way; delete it, or add and commit it first.``
- 移除被当前commit跟踪但没有出现在check out commit中的文件
- 签出指定commit中的文件
- 将当前commit写入HEAD指向的分支
- 清空暂存区

以下属于未被tracked的两中情况
- 工作区中的文件不存在commit中时
- 工作区中的文件与commit不同，但与reset中相同

## merge
>将given branch合并到current branch
`If an untracked file in the current commit would be overwritten or deleted by the merge：`

首先找到两个分支的split point（最近公共祖先）
![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202311151036122.png)

- split point与given branch相同：`Given branch is an ancestor of the current branch.`
- split point与current branch相同：相当于签出given branch，`Current branch fast-forwarded.`

![CS61B-pro2.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202311151058707.png)
修改方式不一样：
- 对文件内容修改，并且修改后内容不一样
- 一个文件内容修改，另一个删除
- split point不存在该文件，current branch和given branch具有该文件的不同内容
此处也是与git不一致的地方，显示冲突时，显示完整文件内容
 Real Git does a more subtle job of merging files, displaying conflicts only in places where both files have changed since the split point.

- 遍历split point跟踪的文件
	- cur和given都有该文件
		- given修改了文件
			- cur没修改文件(情况1)
			- cur也修改了文件，并且两者改动不一样
			
	- cur不存在该文件，given存在	
		- given如果修改了文件，则两者修改方式不一样

	- given不存在该文件，cur存在
		- cur没有修改该文件，因为given将文件移除，所以移除该文件
		- cur修改了该文件，则两者修改方式不一致

- 遍历given跟踪的文件（given新增了文件(cur可能有该文件但内容不一样，也可能没有)）
	- split中没有该文件
		- cur没有该文件（checkout, tracked）
		- 两者文件内容不一样，则两者修改方式不一样
## add-remote
`java gitlet.Main add-remote [remote name] [name of remote directory]/.gitlet`
>添加远端仓库地址

读取跟踪的远端仓库
判断name是否存在，不存在则记录映射关系
## rm-remote
>移除远端仓库地址

读取跟踪的远端仓库
判断是否存在，若存在
- 解除跟踪，`remoteDir`
- 删除`refs/remotes/name`跟踪的分支
## push
`java gitlet.Main push [remote name] [remote branch name]`
>将本地仓库的修改推送到远端对应的分支（remote name记录的时远端.gitlet目录地址）
>当前分支包括远端分支未知的一些提交

DFS搜索当前分支的commit记录
判断远端仓库对应分支的最新commit是否在记录中
- 将中间的commit推送到远端

修改远端仓库对应分支的最新commit
修改本地仓库`refs/remote/name/branch`跟踪的commit
## fetch
`java gitlet.Main fetch [remote name] [remote branch name]`
>将远端仓库commit拉到本地仓库，`[remote name]/[remote branch name]`指向head commit

遍历远端仓库分支的commit记录
读取本地跟踪的`refs/remote/name/branch`最新的commit
- 将中间的commit拉取到本地
修改`refs/remote/name/branch`跟踪最新的commit
## pull
>与fetch相同，不过还需与当前分支merge



`Modifications Not Staged For Commit` and `Untracked Files`



# 类及其功能
## Main

> 处理命令行输入的命令参数，根据输入的命令调用`Repository`的相关方法，同时验证命令行参数输入的有效性。如果输入不正确的命令，或者命令对应的参数不正确，则打印相关错误信息。

### Method

1. `public static void main(String[] args)`：程序入口，根据输入的命令参数执行相应的过程
2. `public static void validateNumArgs(String cmd, String[] args, int n)`：验证参数个数是否正确，对于`checkout`命令单独处理，因为该命令可以多种不同参数个数的调用格式
## Commit

> 处理每次commit任务，包括创建commit数据结构信息(包括提交信息，提交日期，父节点，跟踪的文件名)

### Fields
1. `private String message`
2. `private String date`
3. `private LinkedList<String> parentID`
4. `private Map<String, String> dict`

### Method
1. `public Commit()`：默认构造方法，再`init`时候生成初始`commit`信息
2. ` public static String getCommitHashVal(), getCommitHashVal(String branch)`：无参数时默认读取当前HEAD指向的commit，否则读取相应分支下的最近一次commit
3. `public static Commit readCommit(String commitHashVal)`：根据commit的哈希值读取对应的文件，反序列化得到`Commit`数据结构
4. ` public void setInfo(String message, LinkedList<String> parentID)`：设置`Commit`的相关信息，在`Commit`时，首先读取父节点的数据结构，对父节点的数据结构进行处理得到当前`commit`
5. ` public void writeCommit()`：将`Commit`数据结构利用序列化存储到一个暂时的文件当中，计算文件的hash，然后再将文件根据hash移动到指定位置。更新`CommitTrie`和`CommitGraph`数据结构，将hash值写到当前分支中，以跟踪当前分支的最新一次提交。

## CommitGraph
### Fields
`private Map<String, LinkedList<String>> g`：邻接链表存储，存储当前commit的父节点信息
### Mehod
1. `public static void DFS(String cur)`：深度优先搜索，从当前cur表示的commit出发，不断递归其第一个父节点，直至到达初始提交
2. `public static void BFS(boolean findMessage, String message)`：参数作为标志，如果`findMessage`为`true`，对应`find`命令，找到是否有提交信息为`message`的`commit`；否则对应`global-log`命令，打印处所有的`commit`信息。
3. `private static void printCommit(String cur, Commit commit)`：格式打印当前cur表示的commit的相关信息
4. ` public static String latestCommonAncestor(String p, String q)`：找到p和q对应的commit的最近公共父节点。
5. `public void addEdge(String u, String v)`：添加u到v的边，其中u为当前`commit`，v为父节点
## CommitTrie
> 保存`commit`的hash值的数据结构，字典树实现，用于在给定`uid`时快速查找到对应的`commit`
### Fields
- `LinkedList<String> commit`：存在于当前节点的`commit`的hash值
- `CommitTrie[] son`：子节点
### Method
1. ` public static void insert(String commitID, CommitTrie root)`：将commit对应的hash插入到字典树当中
2. `public static String query(String commitID, CommitTrie root)`：查询具有给定`uid`的`commit`

## Repository


## remoteRepo
>处理`add-remote`、`rm-remote`、`push`、`fetch`、`pull`命令，完成本地仓库与远端仓库的交互逻辑，远端仓库在本地模拟，给定路径作为远端仓库地址。
### Fields
- `private Map<String, String> remoteDir`：保存(远端仓库名，远端地址)，这一数据结构保存在`CONFIG`中
### Method
1. `public static void addRemote(String name, String path)`：添加远端仓库，并在本地`.gitlet/refs/remotes/`目录下添加远端仓库名文件夹，用以存储远端仓库的分支
2. `public static void rmRemote(String name)`：从`remoteDir`数据结构中移除远端仓库，并将本地`.gitlet/refs/remotes/`目录下的用以跟踪远端仓库的文件夹及其内的分支删除
3. `public static void push(String name, String branch)`：将本地当前分支的修改推送到远端仓库，远端分支的头节点需为当前分支的历史节点。将远端分支和本地分支之间提交的commits推送到远端仓库，远端分支跟踪的头节点需进行更新。
4. `private static void copyCommit(String commitHash, File src, File dst)`：将`commitHash`跟踪的所有文件和存储commit数据结构的文件从`src`目录复制到`dst`目录
5. `public static void fetch(String name, String branch)`：将远端仓库的修改拉到本地仓库，根据远端仓库`refs/heads/branch`记录的commit与本地仓库`refs/remote/name/branch`下记录的commit，可以追踪本地没有的commit。
6. `public static void pull(String name, String branch)`：与fetch相比，该命令还需将拉取的分支与本地分支合并(代码复用，利用Repository.merge实现)。
# 测试
测试文件存放在`proj2\testing\samples`目录下
```shell
//运行所有测试，是否输出额外信息
make check
make check TESTER_FLAGS="--verbose"
//测试单个文件
python3 tester.py --verbose --keep FILE.in
```

# 参考
[Project 2: Gitlet | CS 61B Spring 2021 (datastructur.es)](https://sp21.datastructur.es/materials/proj/proj2/proj2#merge)
[Lab 6: Getting Started on Project 2 | CS 61B Spring 2021 (datastructur.es)](https://sp21.datastructur.es/materials/lab/lab6/lab6)（Serialization Details）
[CS61B Gitlet入坑指南 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/533852291)