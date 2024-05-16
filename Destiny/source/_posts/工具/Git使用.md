---
title: Git使用
categories: 工具
---
>distributed versoin control system（分布式版本控制系统）
# 初始化
### 配置
```shell
git config --global user.name "yuyg"
git config --global user.email "1632508233@qq.com"
```

[Git Intro - Part 2 - YouTube](https://www.youtube.com/watch?v=CnMpARAOhFg)
```shell
git status                          # To see what needs to be added or committed.
git add <filepath>                  # To add, or stage, any modified files.
git commit -m "Commit message"      # To commit changes.
git push

git log                             #查看commit提交日志
git checkout (commit ID)  #切换分支
git checkout master

touch a.txt
cat a.txt
```

![image-20230329200310434](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2013/202303292003505.png)

![image-20230329201305806](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2013/202303292013895.png)

[聊下git pull --rebase - 王清培 - 博客园 (cnblogs.com)](https://www.cnblogs.com/wangiqngpei557/p/6056624.html)

```shell
git init 初始化

#添加文件到暂存区
git add .			当前目录所有文件
git add filename

#撤销添加到暂存区的文件
git reset .

#查看添加状态
git status

#把文件提交到仓库
 git commit -m message
 git commit -m "[base] commit"
 
#查看提交状态
git status

#从最近到最远显示提交日志
git log
git log -pretty=oneline		每次提交显示为一行

还未commit的文件
git rm --cached . -r  删除add的文件
git rm --cached “文件路径”，不删除物理文件，仅将该文件从缓存中删除；
git rm --f “文件路径”，不仅将该文件从缓存中删除，还会将物理文件删除（不会回收到垃圾桶）
```

![image-20230329205129717](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2013/202303292051791.png)

![image-20230329205152756](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2013/202303292051832.png)

文件回退

```git
git checkout aa60287c2f031974fb034290bfa3c2f24823f9cb -f lab1/Collatz.java
git checkout aa60287c2f031974fb034290bfa3c2f24823f9cb -- lab1/Collatz.java
```

## 版本回退

```shell
git reset --hard HEAD^		回退到上个版本
git reset --hard HEAD^^		回退到上上个版本
git reset --hard HEAD~1

git reset --hard 版本号	 	回退到最新版本

git reflog					查看版本号
```



## 远程仓库

```shell
git remote add [shortname] [url]
#origin为远程仓库的别名
git remote add origin git@github.com:destiny0118/Few-shot-Image-Segmentation.git

git branch -M master
#-u参数，把本地的master分支推送到远程新的master分支，将本地master分支与远程master分支关联起来
git push -u origin master




git remote -v			查看远程仓库信息
git remote rm origin	删除已有的远程仓库
```

## git push

[git push 命令 | 菜鸟教程 (runoob.com)](https://www.runoob.com/git/git-push.html)

**git push** 命用于从将本地的分支版本上传到远程并合并。

命令格式如下：

```shell
git push <远程主机名> <本地分支名>:<远程分支名>

```

如果本地分支名与远程分支名相同，则可以省略冒号：

```
git push <远程主机名> <本地分支名>
```

1. git push origin master：refs/for/master 
	即是将本地的master分支推送到远程主机origin上的对应master分支， origin 是远程主机名  第一个master是本地分支名，第二个master是远程分支名。
2. git push origin master
	如果远程分支被省略，如上则表示将本地分支推送到与之存在追踪关系的远程分支（通常两者同名），如果该远程分支不存在，则会被新建
3. git push origin ：refs/for/master
	如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支，等同于 git push origin --delete master

- 克隆GitHub的仓库到本地

  `git clone + 仓库地址`

- 查看本地仓库关联的远程仓库信息

  `git remote -v`

- 查看分支

  `git branch`

- 创建分支

  `git branch+分支名`

- 设置别名

  `git config --global alias.br branchcat -n ~/.gitconfig`

## git branch

[Git branch命令的用法总结 | 精彩每一天 (hangdaowangluo.com)](http://www.hangdaowangluo.com/archives/491)

- git branch -a

  查看所有分支

- git branch 分支名

  仅创建分支

- git checkout 分支名

  切换分支

![git命令思维导图1](https://gitee.com/destiny0118/picgo/raw/master/pic/git%E5%91%BD%E4%BB%A4%E6%80%9D%E7%BB%B4%E5%AF%BC%E5%9B%BE1.png)



pulling and collaborating with other people

![image-20230329211657074](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2013/202303292116146.png)

![image-20230329211738590](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2013/202303292117667.png)

# git reset

> git add撤销

git reset：用于将当前HEAD复位到指定状态。一般用于撤消之前的一些操作(如：git add,git commit等)。

[如何在 Git 中取消暂存文件？ - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/638297266#:~:text=git restore --staged <文件名>,替换 <文件名> 为要取消暂存的文件名。 执行该命令后，Git将会将文件从暂存区移除，但保留对文件的修改。)


# detached游离节点提交

[Git ---游离状态下的commit 分支切换与找回_git 代码提交到了游离分支怎么挽回-CSDN博客](https://blog.csdn.net/zyb2017/article/details/78307688)

git branch callback commit_id   //利用commit_id创建新分支  
git checkout dev  
git merge callback  
git push   
git branch -d callback    //删除分支





# branch

删除分支：git branch -d [branch name]