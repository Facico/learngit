## git学习



### 创建版本库(仓库，repository)



#### 初始设定 

创建一个文件之后

```bash
~$ git init
```

就能把这个目录设置git可以管理的仓库



#### 将文件加到暂存区

```bash
$ git add readme.txt
```

将工作区的文件readme.txt添加到暂存区，这个文件必须存在

```bash
$ git add readme.txt readme1.txt
```

可以一次添加多个



#### 将文件添加到版本库

```bash
$ git commit -m"wrote a readme.txt"
```

双引号里面的东西是解释，方便别人或者自己后面看这个添加的简介



### 版本库中的操作



#### 掌握版本库动态

```bash
$ git status
位于分支 master
尚未暂存以备提交的变更：
  （使用 "git add <文件>..." 更新要提交的内容）
  （使用 "git checkout -- <文件>..." 丢弃工作区的改动）

	修改：     readme.txt

```

可以看到版本库是否做了改动



#### 查看文件改动了什么地方

```bash
$ git diff readme.txt
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.

```

diff能比较修改之前与现在的状态，并找出不同(-表示删掉了什么，+表示增加了什么)

```bash
$ git diff readme.txt
```

这条语句能看版本库的最新版本与工作区里最新版本的区别





#### 查看版本库修改时间线

```bash
$ git log
commit 804602b56aa7a41f98d6ce286125b241c087d65e (HEAD -> master)
Author: Facico <845161327@qq.com>
Date:   Fri Oct 25 18:13:39 2019 +0800

    add distributed

commit 8e57ed773b3907fa7ad7af8f2defb39065f84d59
Author: Facico <845161327@qq.com>
Date:   Fri Oct 25 17:57:32 2019 +0800

    wrote a readme.txt

```

git log命令显示从最近到最远的提交日志

如果嫌得到的东西太多可以

```bash
$ git log --pretty=oneline
804602b56aa7a41f98d6ce286125b241c087d65e (HEAD -> master) add distributed
8e57ed773b3907fa7ad7af8f2defb39065f84d59 wrote a readme.txt

```

这样就可以把很多行压成一行，这时每行前面得到的一大串东西就是commit id(版本号)，它不是用1,2,3这样的东西来标号的，因为如果很多人一起用这个东西就会产生冲突。



#### 版本回退

```bash
$ git reset --hard HEAD^
HEAD 现在位于 8e57ed7 wrote a readme.txt
```

HEAD表示当前的版本，因为知道了我的版本号和别人的版本号肯定不一样，所以直接用链表的形式往上跳，HEAD^表示上一个，HEAD^^表示上两个，HEAD～100表示上一百个

执行上面的操作之后，就会发现readme.txt被还原了

版本回退的速度很快，git内部是有一个指针可以快速指向(就相当于打了个可持久化的数据结构)



#### 版本恢复(回退完之后想恢复)

```bash
$ git reset --hard 80460
HEAD 现在位于 804602b add distributed

```

80460是原来版本的版本号，找到这个版本号就能恢复了，版本号没有必要写完，能识别就行



#### 查看历史命令

```bash
$ git reflog
804602b (HEAD -> master) HEAD@{0}: reset: moving to 80460
8e57ed7 HEAD@{1}: reset: moving to HEAD^
804602b (HEAD -> master) HEAD@{2}: commit: add distributed
8e57ed7 HEAD@{3}: commit (initial): wrote a readme.txt

```



#### 撤销修改

##### 撤销工作区修稿

```bash
$ git checkout -- readme.txt
```

**可以撤销在工作区中的修改**

（使用 "git add <文件>..." 更新要提交的内容）
  （使用 "git checkout -- <文件>..." 丢弃工作区的改动）

就是status的下面那句

**这样的撤销有两种情况**：

- -------

  - 1、文件修改前后还没有被放入暂存区---->那么就修改成和版本库一样:回到git add

  - 2、文件已经添加到暂存区后又做了修改----->那么就修改和暂存区一样回到git commit

#####  **撤销暂存区修改**

```bash
git reset HEAD  readme.txt
```

可以撤销暂存区中的修改

##### 撤销版本的修改

直接回到历史版本



#### 删除文件

```bash
$ rm readme.txt
```

status一下，git就发现工作区和版本库不一致；

```bash
$ git rm readme.txt
rm 'readme.txt'
$ git commit -m"shandiaole"
```

remove在commit上传的是删除信息

特别的：只在工作区删掉之后，可以直接git checkout可以直接还原文件



### git的特点

**1、**git与其他版本控制系统不同的地方就是它有暂存区的概念

- 工作区(working directory)：电脑上能看到的目录就是工作区，如learngit
- 版本库(repository)：工作区有一个隐藏目录.git，这个不是工作区而是git的版本库
  - stage(或叫index)：在版本库里的暂存区
  - master：我们自己创建的第一个分支
  - HEAD：指向master的一个指针

![git-repo](https://www.liaoxuefeng.com/files/attachments/919020037470528/0)

每次status的时候就有一个“位于分支master”

工作原理就像上面说的一样，先add到stage，再commit到版本库，所以如果改了一次add了，又改了一次在commit，第二次的修改不会被交到版本库

**2、**git比其他设计的优秀的原因就是git跟踪管理的不是文件，而是修改

如上：如果改了一次add了，又改了一次在commit，第二次的修改不会被交到版本库



### 远程仓库

“有了远程仓库，妈妈再也不用担心我的硬盘了。”——Git点读机

把自己的仓库推送到github上

需要设置SSH key

```bash
$ ssh-keygen -t rsa -C "youremail@example.com"
```

#### 将本题仓库推送到远程仓库

```bash
$ git remote add origin https://github.com/Facico/learngit.git

```

origin是远程库的名字的默认叫法(也可以改成别的)

#### 推送

```bash
$ git push -u origin master
```

把当前分支master推送到远程

由于远程库是空的，我们第一次推送master分支是，加上-u参数，git不但会把本地的master分支内容推送到远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后会的推送或者拉取是就可以简化命令



#### 克隆

```bash
$ git clone https://github.com/Facico/Linux-skill
```

