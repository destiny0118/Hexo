---
title: Git使用
categories: 工具
---

# 初始化

[聊下git pull --rebase - 王清培 - 博客园 (cnblogs.com)](https://www.cnblogs.com/wangiqngpei557/p/6056624.html)

```shell
git init 初始化
git config --global user.name "yuyg"
git config --global user.email "1632508233@qq.com"


#添加文件到暂存区
git add .			当前目录所有文件
git add filename

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

### 推送到远程仓库

[git push 命令 | 菜鸟教程 (runoob.com)](https://www.runoob.com/git/git-push.html)

**git push** 命用于从将本地的分支版本上传到远程并合并。

命令格式如下：

```
git push <远程主机名> <本地分支名>:<远程分支名>
```

如果本地分支名与远程分支名相同，则可以省略冒号：

```
git push <远程主机名> <本地分支名>
```



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

## 分支

[Git branch命令的用法总结 | 精彩每一天 (hangdaowangluo.com)](http://www.hangdaowangluo.com/archives/491)

- git branch -a

  查看所有分支

- git branch 分支名

  仅创建分支

- git checkout 分支名

  切换分支

![git命令思维导图1](https://gitee.com/destiny0118/picgo/raw/master/pic/git%E5%91%BD%E4%BB%A4%E6%80%9D%E7%BB%B4%E5%AF%BC%E5%9B%BE1.png)

