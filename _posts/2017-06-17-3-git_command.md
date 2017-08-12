---


layout: post


category : post


tagline: "Supporting tagline"


tags : [git, repo , beginner]


---

{% include JB/setup %}


## Git

### 查看历史
git blame 文件1
git show 426eb1b84// 打开某次记录

### Git：（Non-fast-forward）
出现原因在于：git仓库中已经有一部分代码，所以它不允许你直接把你的代码覆盖上去。于是你有2个选择方式：
1，强推，即利用强覆盖方式用你本地的代码替代git仓库内的内容
git push -f
2，先把git的东西fetch到你本地然后merge后再push
$ git fetch
$ git merge
这2句命令等价于
$ git pull


### Git撤销修改
(1) git checkout
恢复某个已修改的文件（撤销未提交的修改）：
$ git checkout file-name
例如：git checkout src/com/android/.../xxx.java
比如修改的都是java文件，不必一个个撤销，可以使用
$ git checkout *.java
撤销所有修改
$ git checkout .

(2) git revert
撤销某次操作，此次操作之前和之后的commit和history都会保留，并且把这次撤销
作为一次最新的提交
    * git revert HEAD                  撤销前一次 commit
    * git revert HEAD^               撤销前前一次 commit
    * git revert commit-id （比如：fa042ce57ebbe5bb9c8db709f719cec2c58ee7ff）撤销指定的版本，撤销也会作为一次提交进行保存。
git revert是提交一个新的版本，将需要revert的版本的内容再反向修改回去，版本会递增，不影响之前提交的内容


### Git 返回以前某个状态代码
第一种返回方法
$git log
commit 5ff8ee...........75044
Author:yangyk3 <yangyk3@zuk.com>
Date:        Wed Aug.....
         commit 信息
         Change-Id: I6758345234.....e65501
commit 5328ee...........75044
Author:yangyk3 <yangyk3@zuk.com>
Date:        Wed Aug.....
         commit 信息
         Change-Id: I6758345234.....e65501
$git reset 5328ee...........75044
### 第二种返回方法
$git reflog
5428ee5 HEAD@{0}:commit
842ffe5 HEAD@{1}:commit
$git reset 842ffe5   //{0}:commit 的但没有 push 的，不会丢掉。


### git查看某个文件的修改历史
git log -p filename
如：某次提交
commit 432dfasd786a5s86fs6d8a5fas6asf5
Author：xxxx@xxxxx
Date： Thu May
使用git log -p a.java
会显示某次的哈希值
git show 哈希值 文件名
git show 432dfasd786a5s86fs6d8a5fas6asf5  a.java

### git 撤销add
git reset .
撤销 add 某个 fileA
git reset fileA

### git 撤销 Comment
git reset  commit_id  ( commit id 需要通过 git log  查看)

### git 回退
http://blog.csdn.net/yaoming168/article/details/38777763

### Patch
1 生成Patch
git diff > diff.patch
2 合入Patch
git apply diff.patch

### 回滚某个文件
首先查看该文件的历史版本信息：git log Default@2x.png
记录下需要恢复的commit版本号：如 9aa51d89799716aa68cff3f30c26f8815408e926
恢复该文件：git reset 9aa51d89799716aa68cff3f30c26f8815408e926 Default@2x.png
提交git:git commit -m "revert old file"


### Git冲突：commit your changes or stash them before you can merge.
1.stash
通常遇到这个问题，你可以直接commit你的修改；但我这次不想这样。
看看git stash是如何做的。
git stash
git pull
git stash pop
接下来diff一下此文件看看自动合并的情况，并作出相应修改。
git stash: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。
git stash pop: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。
git stash list: 显示Git栈内的所有备份，可以利用这个列表来决定从那个地方恢复。
git stash clear: 清空Git栈。此时使用gitg等图形化工具会发现，原来stash的哪些节点都消失了。

2.放弃本地修改，直接覆盖之
git reset --hard
git pull

git revert 和 git reset的区别
1. git revert是用一次新的commit来回滚之前的commit，git reset是直接删除指定的commit。
2. 在回滚这一操作上看，效果差不多。但是在日后继续merge以前的老版本时有区别。因为git revert是用一次逆向的commit“中和”之前的提交，因此日后合并老的branch时，导致这部分改变不会再次出现，但是git reset是之间把某些commit在某个branch上删除，因而和老的branch再次merge时，这些被回滚的commit应该还会被引入。
3. git reset 是把HEAD向后移动了一下，而git revert是HEAD继续前进，只是新的commit的内容和要revert的内容正好相反，能够抵消要被revert的内容。
git reset
git reset --hard e377f60e28c8b84158
-f 强制推送，否则出错
git push -f origin master


## Repo

### 同步代码出现错误
提示需要checkout，但是checkout 后又不管用，那就删掉这个目录后，在用repo sync同步代码

### error: prior sync framework/base failed; rebase still in progress
framework/base 目录下 .git/ 目录删除后重新 sync

### repo sync 某个目录
1. 打开.repo/manifiest.xml文件（这是隐藏文件夹，可用Ctrl+h显示），找到所要下载的project，使用path或者name字段的值都可以，直接跟在repo sync后作为参数即可。
2. 在要修改的文件的目录，找到最早祖先目录， repo sync 这个祖先目录


### Git与Repo

<http://www.cnblogs.com/angeldevil/archive/2013/11/26/3238470.html>

<http://blog.csdn.net/gjswxhb/article/details/23221197>

<http://blog.csdn.net/huwei2003/article/details/40707139>