Git是目前世界上最先进的**分布式版本控制系统**

## 版本库与文件操作

#### 初始化创建版本库

使用<font color = "red">**`git init`**</font> 命令将这个目录成为Git可以管理的仓库

执行完此命令后当前目录下回出现**`.git`** 的目录。此目录是Git用来跟踪管理版本库的

#### 查看仓库状态

<font color = "red">**`git status`**</font> 命令可以随时查看 **工作区/仓库** 当前的状态

 1. *Changes not staged for commit*：文件更改了，但是还未进入暂存区 ，需要**`git add`** ；
  2. *Changes to be committed*：文件已进入暂存区，但还未提交到版本库，需要**`git commit`** ；
  3. *Untracked files*：表示该文件还从来没有被添加进版本库，这是新添加的文件；

#### 文件操作

使用<font color = "red">**`git add 文件名`**</font>命令将文件放进**暂存区**，此命令课反复多次使用，也可同时添加多个文件

使用<font color = "red">**`git commit -m "备注(描述)"`**</font>命令，将**暂存区**的所有内容提交到**当前分支**

#### 查看修改内容

<font color = "red">**`git diff 文件名`**</font>  命令可以查看指定文件修改内容(暂存区和工作区的差异)

## 版本回退

<font color = "red">**`git log`**</font> 可以查看从最近到最远的提交日志（即查看版本库的状态 ）

<font color = "red">**`git diff 文件名`**</font>查看修改的内容

<font color = "red">**`git diff HEAD -- 文件名 `**</font>可以查看工作区和版本库里面最新版本的区别

<font color = "red">**`git reset --hard HEAD^`**</font> 表示回退一个版本**`HEAD`**其实是个指向当前版本的指针

<font color = "red">**`git reset --hard HEAD^^`**</font>表示回退两个版本

<font color = "red">**`git reset --hard HEAD~100`**</font>表示回退100个版本

<font color = "red">**`git reset --hard 版本号`**</font>将head指针指向那个版本号(版本号可以只写前几位)

<font color = "red">**`git reflog`**</font>查看历史每一次命令，可以查看每一次提交时的**`commit id`**

#### 工作区和暂存区

**`工作区`** 比如电脑中看到的目录

**`版本库`** 工作区有一个隐藏目录**`.git`** 这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`

![](.\image\01.png)

#### 撤销修改

<font color = "red">**`git checkout -- 文件名`**</font>把文件在工作区的修改全部撤销

<font color = "red">**`git reset HEAD 文件名`**</font>把文件在暂存区的修改撤销掉，重新放回工作区

#### 删除文件

如果在文件管理器中吧文件删除了，这个时候工作区和版本库不一致。可以使用**`git checkout -- 文件名`**可以将版本库中文件恢复到工作区。

<font color = "red">**`git rm 文件名`**</font>  删除版本库中的文件

## 远程仓库

#### 添加远程库

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有**`id_rsa`**和**`id_rsa.pub`**这两个文件。创建SSH Key：

~~~
ssh-keygen -t rsa -C "youremail@example.com"
~~~

然后在github上添加SSH，将**`id_rsa.pub`**内容复制进去



<font color = "red">**`git remote add origin git库的地址`**</font>将本地库与远程库连接

