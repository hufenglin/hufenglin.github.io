---
layout: post
categories: 技术
tags: Git   
---

# 1 . 查看远程分支

      $ git branch -a  
	  * br-2.1.2.2  
	    master  
	    remotes/origin/HEAD -> origin/master  
	    remotes/origin/br-2.1.2.1  
	    remotes/origin/br-2.1.2.2  
	    remotes/origin/br-2.1.3  
	    remotes/origin/master 
 
# 2 . 查看本地分支

      $ git branch  
	  * br-2.1.2.2  
            master 

# 3 . 创建分支
    
      $ git checkout -b test     #git checkout命令加上-b参数表示创建并切换
	 Switched to a new branch 'test'
	
      git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
      $ git branch test    
      $ git branch  
        * br-2.1.2.2  
          master  
          test
	  
# 4 . 合并指定分支到当前分支

	$ git merge test

# 5 . 把分支推到远程分支

      $ git push origin test 

# 6 . 切换分支到test

      $ git branch  
        * br-2.1.2.2  
          master  
          test

      $ git checkout test  
        Switched to branch 'test'  

      $ git branch  
         br-2.1.2.2  
         master  
       * test
       
### 注意：M 表示从原来分支（上一次修改没有提交br-2.1.2.2）带过来的修改

# 7 . 删除本地分支  git branch -d xxxxx

      $ git checkout br-2.1.2.2  
        Switched to branch 'br-2.1.2.2'

      $ git branch    
       * br-2.1.2.2  
         master  
         test  

      $ git branch -d test  
        Deleted branch test (was 17d28d9).

      $ git branch    
        * br-2.1.2.2  
          master   

# 8 . 删除远程版本

      git push origin :br-1.0.0
      git branch -r -d origin/branch-name  
      git push origin :branch-name  

# 9 . 添加文件到Git仓库，分两步：

	git add <file>    #可反复多次使用，添加多个文件；
	git commit -m <message>
	
	加-a参数可以将前面两步合二为一：
	$ git commit -a

	
	$ git add file1.txt
	$ git add file2.txt file3.txt
	$ git commit -m "add 3 files."
	
# 10 . git操作的历史记录

	$ git log
	commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
	Author: fenglin Hu <fenglin.hu@intel.com>
	Date:   Fri May 18 21:06:15 2018 +0800

   	   append GPL

	commit e475afc93c209a690c39c13a46716e8fa000c366
	Author: fenglin Hu <fenglin.hu@intel.com>
	Date:   Fri May 18 21:03:36 2018 +0800

  	  add distributed


# 11 . 返回到指定的commit版本

	$ git reset --hard HEAD^  # 返档到前一次commit的版本  
	HEAD表示当前版本   HEAD^上一个版本   HEAD^^上上一个版本  HEAD~100往上100个版本
	
	$ git reset --hard commit-id  # 返档到指定commit版本 
	
# 12 .  查看某个commit号的patch

	$ git show commit-id  # 查看指定commit的改动的patch
	
# 13 .  查看命令历史，以便确定要回到未来的哪个版本

	$ git reflog
	  e475afc HEAD@{1}: reset: moving to HEAD^
	  1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
	  e475afc HEAD@{3}: commit: add distributed
	  eaadf4e HEAD@{4}: commit (initial): wrote a readme file
	  
# 14 . 弃对工作区的修改

	$ git checkout -- readme.txt  #把readme.txt文件在工作区的修改全部撤销
	--很重要，没有--，就变成了“切换到另一个分支”的命令
	
	当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改
	git reset HEAD readme.txt
	git checkout --readme.txt 


# 15  . 查看比较工作区与暂存区的差异

	git diff   # 不加参数即默认比较工作区与暂存区


# git 的使用

### 1.拉代码:
	git clone git://vt-sync.sh.intel.com/vmm_tree.git -b cicada-test
	-b  cicada-test 表示下载此项目下的cicada-test分支，一个项目下可以有多个分支，默认为master分支
 
### 2.如何 push 代码
	git config --global user.name "Hu Fenglin"
	git config --global user.email fenglin.hu@intel.com

### 3.查看git status状态
 使用命令git status查看结果，我们可以时刻掌握仓库（History）当前的状态，该结果告诉我们，readme.txt被修改过了，但是还没有提交修改。

### 4.执行
    git commit -a
    
git是分为三部分，一部分是你自己的文件，另外一个是缓存区，最后一个是本地库。当你修改了自己的文件后，

git add xx将修改保存到缓存区，然后再用commit推送修改到本地库中。  
git push 将本地仓库修改推送到服务器上的仓库中  
git commit 命令的
	a. 合并上次提交和本次提交的
	    git commit -a(将 untagged 变成 tagged)
	b.  修改代码
	    git commit --amend -a  
	c.  如果不修改代码，只是修改提交的注释的话
	    git commit --amend
	d. 合并中间有间隔的几次提交
	    git rebase -i HEAD~3
	    修改 pick 的顺序  
	e. 提交并增加签名
	    git commit -a -s
	f. 提交并添加注释 
	    这是 git 基本工作流程的第一步；使用如下命令以实际提交改动：
	    git commit -m "代码提交信息"
	    现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。

git add .命令除了能够判断出当前目录（包括其子目录）所有被修改或者已删除的文档，还能判断用户所添加的新文档，并将其信息追加到索引中。

### 5.执行
	git format-patch -1
生成patch。


### 6.进入修改的目录下面执行
	git am -3 -i -s -u 0001-Check-whether-Replay-END-exists.patch

### 7.执行
	git push origin cicada-test:cicada-test
	

## 注意：使用git log 查看我们提交的历史记录
	git log
	
### 8.查看已经提交的历史patch

	 git show b1e86b507c31ff3064907e7f51f75b4802e2bb06

### 创建分支
	基于远程分支”origin”，创建一个叫”hello”的分支。
	git checkout -b hello origin
 
### 合并patch
	git rebase -i HEAD~2(合并2个patch)
	[pick 保留，squash合并到保留选项]
	git branch -a         ---查看所有分支
	git checkout + 分支名  ---切换分支
	
	git tag
	git remote -v         ----查看远端服务器上的仓库路径

### 返回到某个patch
	git reset --hard commit-number

### Pull patch
	git reset --hard commit 号                 撤销所打的patch
	git show commit 号                        查看所提交的patch内容
	git commit -am "add a dd script(patch的名字)"
	git am -3 -i -s -u <patch>
	git push origin cicada-test:cicada-test

### merge patch 
	git push origin YOURNAME_FEATURE:YOURNAME_FEATURE
	git checkout cicada-test
	git merge YOURNAME_FEATURE

	  
	



