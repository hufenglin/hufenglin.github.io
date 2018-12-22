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
	
# 12 .  查看命令历史，以便确定要回到未来的哪个版本

	$ git reflog
	  e475afc HEAD@{1}: reset: moving to HEAD^
	  1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
	  e475afc HEAD@{3}: commit: add distributed
	  eaadf4e HEAD@{4}: commit (initial): wrote a readme file
	  
# 13 . 弃对工作区的修改

	$ git checkout -- readme.txt  #把readme.txt文件在工作区的修改全部撤销
	--很重要，没有--，就变成了“切换到另一个分支”的命令
	
	当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改
	git reset HEAD readme.txt
	git checkout --readme.txt 

	  
	



