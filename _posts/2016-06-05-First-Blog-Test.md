---
layout:     post
title:      "Git常用命令"
subtitle:   " \"Hello World! Hello Jekyll!\""
date:       2016-06-04 12:00:00
author:     "Lushun"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 技术
    - Git
---

### 初始化仓库
    进入新建的仓库目录下，输入命令 `git init`  

### 添加文件：
    git add readme.txt

### 查看状态：
    git status

### 提交文件到仓库   
    git commit -m "wrote a readme file"
    $ git add file1.txt
    $ git add file2.txt file3.txt
    $ git commit -m "add 3 files."

### 查看最近修改情况
    $ git diff readme.txt
    diff --git a/readme.txt b/readme.txt
    index 46d49bf..9247db6 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -1,2 +1,2 @@
    -Git is a version control system.
    +Git is a distributed version control system.
     Git is free software.

### 查看最近提交日志
    $ git log --pretty=oneline
    3628164fb26d48395383f8f31179f24e0882e1e0 append GPL
    ea34578d5496d7dd233c827ed32a8cd576c5ee85 add distributed
    cb926e7ea50ad11b8f9e909c05226233bf755030 wrote a readme file

### 版本回退
首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交3628164...882e1e0，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

    $git log --pretty=oneline
    cfa7f0953b79d2aac038b11181128b99d774b989 也增加了‘6666’
    d3b917ca86efa78a9563e4b9f8499a31e634d93e 增加了‘5555’， 不知道是否也增加了‘6666’
    517c44ccf7e10ea5fc34cb7745d489462c2c991e 增加了“4444”； 增加了manman.txt文件
    999d0ba26a507f2c60d6402863d334d3667f60bb 增加了“3333”
    e04c2b27ace3955f2e85508aead1849a991fa001 增加 “2222” line
    e105761d5978f322e8e6392a24074d0bf6d205fe delete readme.txt
    64cb44fc5eaefe852cb36a4037b20e33ac8d61cc create test.txt file
    f126275141bfab88bbc47cfe035f71b2b30e9b83 清空内容
    36ff724e98687f1ebe2e13a10b6c4190f87baa13 加了“哈哈哈”内容
    96509025409caabd6beaec14a50d8c1aa493e9c1 add a new line
    f1a2f5e24e6094bba8c953ef4dd148651bcb5125 add readme file
    $ git reset --hard cfa7f0953

HEAD is now at cfa7f09 也增加了‘6666’

### 删除文件
    git rm file 或者
    rm file ; git add .

### 版本库
![img](/img/in-post/post-introduce-git/1.png)

前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：
第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区（stage)；  
第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。
因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，git commit就是往master分支上提交更改。  
你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。  
用git diff HEAD -- readme.txt命令可以查看工作区和版本库里面最新版本的区别：

### 撤销修改
 **在git bash中**
![img](/img/in-post/post-introduce-git/2.png)
**在git bash中，修改工作区文件后，git status看到红色字体的“modified: 1.txt" 的提示；  
git add命令将修改提交到暂存区后，git status看到绿色字体的"modified: 1.txt"的提示。（git gui的Stage changed相当于git add).  
    当git add将修改提交到暂存区后，需要用git reset head 1.txt的命令来恢复到仓库中的最新版本。然后在用git status查看如下图所示，此时为，git reset head命令将覆盖暂存区的修改信息。  
    此时，可以用git checkout 1.txt，将仓库的最新版本拉取到工作区，覆盖工作区文件。（但是git gui的checkout好像不管用，还没有效使用到）。**

![img](/img/in-post/post-introduce-git/3.png)

    $ git checkout -- readme.txt  

命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：  
一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；  
一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。  
总之，就是让这个文件回到最近一次git commit或git add时的状态。


### 同步github网站仓库和本地仓库

要关联一个远程库，使用命令

    git remote add origin https://github.com/lushuner/repository.git
    git push -u origin master    

关联后，使用命令git push -u origin master第一次推送master分支的所有内容；  
此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；  
分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！    

 
你也许还注意到，GitHub给出的地址不止一个，还可以用https://github.com/michaelliao/gitskills.git这样的地址。
实际上，Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。  

使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。  
`git remote add origin git@github.com:michaelliao/learngit.git` 

### 从远程库克隆 
    $ git clone git@github.com:michaelliao/gitskills.git
    Cloning into 'gitskills'...
    remote: Counting objects: 3, done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Receiving objects: 100% (3/3), done.
    $ cd gitskills
    $ ls
    README.md

### fetch和pull的区别

Git中从远程的分支获取最新的版本到本地有这样2个命令：

* git fetch：相当于是从远程获取最新版本到本地，不会自动merge

    git fetch origin master
    git log -p master..origin/master
    git merge origin/master

以上命令的含义：
   首先从远程的origin的master主分支下载最新的版本到origin/master分支上
   然后比较本地的master分支和origin/master分支的差别
   最后进行合并
   上述过程其实可以用以下更清晰的方式来进行：  

    git fetch origin master:tmp
    git diff tmp    
    git merge tmp

从远程获取最新的版本到本地的test分支上
   之后再进行比较合并

* git pull：相当于是从远程获取最新版本并merge到本地

    git pull origin master

上述命令其实相当于git fetch 和 git merge
    在实际使用中，git fetch更安全一些
   因为在merge前，我们可以查看更新情况，然后再决定是否合并

### 分支
Git鼓励大量使用分支：

    查看分支：git branch
    创建分支：git branch <name>
    切换分支：git checkout <name>
    创建+切换分支：git checkout -b <name>
    合并某分支到当前分支：git merge <name>
    删除分支：git branch -d <name>
 
### 标签
	• 命令git tag <name>用于新建一个标签，默认为HEAD，也可以指定一个commit id；
	• git tag -a <tagname> -m "blablabla..."可以指定标签信息；
	• git tag -s <tagname> -m "blablabla..."可以用PGP签名标签；
	• 命令git tag可以查看所有标签。
	• 命令gitpush origin <tagname>可以推送一个本地标签；
	• 命令gitpushorigin--tags可以推送全部未推送过的本地标签；
	• 命令gittag -d <tagname>可以删除一个本地标签；

命令gitpush origin :refs/tags/<tagname>可以删除一个远程标签。
