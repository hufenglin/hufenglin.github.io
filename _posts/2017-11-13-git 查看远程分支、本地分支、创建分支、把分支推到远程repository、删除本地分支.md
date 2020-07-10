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
      $ git checkout test  
          br-2.1.2.2  
          master  
        * test
	  
# 4 . 合并指定分支到当前分支

	$ git merge test

# 5 . 把分支推到远程分支

      $ git push origin test 

# 6 . 切换分支到test

      $ git checkout test  
        Switched to branch 'test'  

      $ git branch  
         br-2.1.2.2  
         master  
       * test
       
### 注意：M 表示从原来分支（上一次修改没有提交br-2.1.2.2）带过来的修改

# 7 . 删除本地分支  git branch -d xxxxx

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

       git push origin --delete [branchname]
       

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


# 拉代码:

	git clone git://vt-sync.sh.intel.com/vmm_tree.git -b cicada-test



# 如何使用git生成patch


1.进入/home/fenglin执行

	git clone git://vt-sync.sh.intel.com/vmm_tree.git -b cicada-test 

2.进行邮箱设置 

	git config --global user.name "Hu Fenglin"
	git config --global user.email fenglin.hu@intel.com

3.查看git status状态

      使用命令git status查看结果，我们可以时刻掌握仓库（History）当前的状态，该结果告诉我们
      ，readme.txt被修改过了，但是还没有提交修改。

(如果新建文件需要执行此步骤)4.执行git add Hu_ReplayResultParser.sh

	     git add . ：他会监控工作区的状态树，使用它会把工作时的所有变化提交到暂存区，包括文件内容修改(modified)以及新文件(new)，但不包括被删除的文件。
	     git add .命令除了能够判断出当前目录（包括其子目录）所有被修改或者已删除的文档，还能判断用户所添加的新文
                档，并将其信息追加到索引中。
	     git add -u ：他仅监控已经被add的文件（即tracked file），他会将被修改的文件提交到暂存区。add -u 不会提交新文件（untracked file）。（git add --update的缩写）
	     git add -A ：是上面两个功能的合集（git add --all的缩写）

5.执行git status查看状态

6.执行git commit -a 

    git是分为三部分，一部分是你自己的文件，另外一个是缓存区，最后一个是本地库。当你修改了自己的文件后，
     你会git add xx将修改保存到缓存区，然后再用commit推送修改到本地库中。

    git push 将本地仓库修改推送到服务器上的仓库中
    	git add .命令除了能够判断出当前目录（包括其子目录）所有被修改或者已删除的文档，还能判断用户所添加的新文
              档，并将其信息追加到索引中。       
		git commit 命令的-a 选项可只将所有被修改或者已删除的且已经被git管理的文档提交倒仓库中。如果只是修改或者
              删除了已被Git 管理的文档，是没必要使用git add 命令的。


7.执行git format-patch -1

       生成patch




### 命令git log查看我们提交的历史记录


# 如何使用Push

1.重新

	git clone git://vt-sync.sh.intel.com/vmm_tree.git -b cicada-test

2.将生成的patch拷贝到目录

3.执行

	git am -3 -i -s -u 0001-check-whether-replay-end-exists.patch  //注意修改patch名字

4.执行

	git push origin cicada-test:cicada-test
	

# 如何撤销远程已经打上去的patch

1.回退到指定的commit

     git reset --hard commit_id1
     git push -f  或者 git push origin branch --force
     
2.如果存在错误：fatal: previous rebase directory .git/rebase-apply still exists but mbox given.执行如下命令：

     git am --abort
     
3.git reset 使用

     git reset --soft xxx
           * soft （修改版本库，保留暂存区，保留工作区）

     git reset --hard xxx
	  * hard （修改版本库，修改暂存区，修改工作区）



	  
	



