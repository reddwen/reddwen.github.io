---
title: GIT基础操作
date: 2018-05-09 08:51:25
tags: GIT GIT基本操作 备忘
---
# GIT 基本操作
本文针对初级用户，从最简单的讲起，但是需要读者对Git的基本用法有所了解。同时，本文覆盖了下面5个命令的几乎所有的常用用法，所以对于熟练用户也有参考价值。
* git clone
* git remote
* git fetch
* git pull
* git push

<!-- more -->

## 一、git clone
远程操作的第一步，通常是从远程主机克隆一个版本库，这时就要用到git clone命令。  
```
$ git clone <版本库的网址>
```  
该命令会在本地主机生成一个目录，与远程主机的版本库同名。如果要指定不同的目录名，可以将目录名作为git clone命令的第二个参数。
```
$ git clone <版本库的网址> <本地目录名>
```
git clone支持多种协议，除了HTTP(s)以外，还支持SSH、Git、本地文件协议等。常用的有如下一些：
```
$ git clone http[s]://example.com/path/to/repo.git/
$ git clone ssh://example.com/path/to/repo.git/
$ git clone git://example.com/path/to/repo.git/
$ git clone file:///opt/git/project.git
$ git clone ftp[s]://example.com/path/to/repo.git/
```

## 二、git remote
为了便于管理，Git要求每个远程主机都必须指定一个主机名。  
git remote 命令就用于管理主机名。

* 不带选项的时候，git remote命令列出所有远程主机。
```
$ git remote
origin
```
* 使用 -v 选项，可以查看远程主机的网址。
```
$ git remote -v
origin  git@github.com:example/example.git (fetch)
origin  git@github.com:example/example.git (push)
```
面命令表示，当前只有一台远程主机，叫做origin，以及它的网址。

克隆版本库的时候，所使用的远程主机自动被Git命名为origin。  
如果想用其他的主机名，需要用git clone命令的-o选项指定。
```
$ git clone -o example https://github.com/example/example.git
$ git remote
example
```
上面命令表示，克隆的时候，指定远程主机叫做 example 。

* git remote show命令加上主机名，可以查看该主机的详细信息
```
$ git remote show <主机名>
```
* git remote add命令用于添加远程主机。
```
$ git remote add <主机名> <网址>
```
* git remote rm命令用于删除远程主机
```
$ git remote rm <主机名>
```
## 三、git fetch
一旦远程主机的版本库有了更新（Git术语叫做commit），需要将这些更新取回本地，这时就要用到git fetch命令。
```
$ git fetch <远程主机名>
```

* 默认情况下，git fetch取回所有分支（branch）的更新。如果只想取回特定分支的更新，可以指定分支名。
```
$ git fetch <远程主机名> <分支名>

//比如，取回origin主机的master分支
$ git fetch origin master
```
* git branch命令的-r选项，可以用来查看远程分支，-a选项查看所有分支。
```
$ git branch -r
origin/master

$ git branch -a
* master
  remotes/origin/master
```
上面命令表示，本地主机的当前分支是master，远程分支是origin/master。

* 取回远程主机的更新以后，可以在它的基础上，使用git checkout命令创建一个新的分支。
```
$ git checkout -b newBrach origin/master
```
* 此外，也可以使用git merge命令或者git rebase命令，在本地分支上合并远程分支。
```
$ git merge origin/master
# 或者
$ git rebase origin/master
```
上面命令表示在当前分支上，合并origin/master。

## 四、git pull
git pull命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。它的完整格式稍稍有点复杂。
```
$ git pull <远程主机名> <远程分支名>:<本地分支名>
```
比如，取回origin主机的next分支，与本地的master分支合并，需要写成下面这样。

```
$ git pull origin next:master
```
如果远程分支是与当前分支合并，则冒号后面的部分可以省略。
```
$ git pull origin next
```
上面命令表示，取回origin/next分支，再与当前分支合并。实质上，这等同于先做git fetch，再做git merge。
```
$ git fetch origin
$ git merge origin/next
```
如果远程主机删除了某个分支，默认情况下，git pull 不会在拉取远程分支的时候，删除对应的本地分支。这是为了防止，由于其他人操作了远程主机，导致git pull不知不觉删除了本地分支。

但是，你可以改变这个行为，加上参数 -p 就会在本地删除远程已经删除的分支。
```
$ git pull -p

// 等同于下面的命令
$ git fetch --prune origin 
$ git fetch -p

```
## 五、git push
git push命令用于将本地分支的更新，推送到远程主机。它的格式与git pull命令相仿。
```
$ git push <远程主机名> <本地分支名>:<远程分支名>
```
注意，分支推送顺序的写法是<来源地>:<目的地>。

**git pull** 是 <远程分支>:<本地分支>  
**git push**是 <本地分支>:<远程分支>

* 如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。

```
$ git push origin hexo
//上面命令表示，将本地的hexo分支推送到origin主机的hexo分支。如果后者不存在，则会被新建。
```

* 如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支
```
$ git push origin :master
// 等同于
$ git push origin --delete master

//上面命令表示删除origin主机的master分支。
```
* 如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。
```
$ git push origin
//上面命令表示，将当前分支推送到origin主机的对应分支。
```
* 如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做git pull合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用--force选项
```
$ git push --force origin 

//上面命令使用--force选项，结果导致远程主机上更新的版本被覆盖。除非你很确定要这样做，否则应该尽量避免使用--force选项
```
* git push不会推送标签（tag），除非使用--tags选项
```
$ git push origin --tags
```
## 六、git branch
```
$ git branch //不带参数：列出本地已经存在的分支，并且在当前分支的前面加“*”号标记

$ git branch -r //列出远程分支

$ git branch -a //列出本地分支和远程分支

$ git branch newbranch(newname)	//创建一个新的本地分支，需要注意，此处只是创建分支，不进行分支切换

$ git branch -m | -M oldbranch newbranch //重命名分支，如果newbranch名字分支已经存在，则需要使用-M强制重命名，否则，使用-m进行重命名

$ git branch -d | -D branchname //删除branchname分支

$ git branch -d -r branchname //删除远程branchname分支
```

## 七、git tag
```
$ git tag //不带参数：列出本地已经存在的tag，并且在当前tag的前面加“*”号标记

$ git show tag //列出tag 的详细信息

```

## 八、git checkout
```
$ git checkout master //进入master分支

$ git checkout -b fromb1 //为源创建分支fromb1，并切换到fromb1

```
## 九、git diff
```
$ git diff //可以产看当前没有add 的内容修改

$ git diff --cached //查看已经add 没有commit 的改动

$ git diff master origin/master //比较两个分支或看看本地分支和远程分支的差异

$ git log test.txt //查看某个文件的日志
```

查看当前git仓库的状态用  
```
$ git status
```