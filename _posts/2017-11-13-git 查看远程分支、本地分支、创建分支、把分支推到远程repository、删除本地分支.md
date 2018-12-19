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
    
      $ git branch test    
      $ git branch  
      * br-2.1.2.2  
        master  
        test

# 4 . 把分支推到远程分支

      $ git push origin test 

# 5 . 切换分支到test

      $ git branch  
      * br-2.1.2.2  
        master  
        test

      $ git checkout test  
      M       jingwei-server/src/main/java/com/taobao/jingwei/server/service/cmd/GetCustomerTarCmd.java  
      M       jingwei-server/src/main/java/com/taobao/jingwei/server/util/ServerUtil.java  
      Switched to branch 'test'  

      $ git branch  
        br-2.1.2.2  
        master  
      * test
### 注意：M 表示从原来分支（上一次修改没有提交br-2.1.2.2）带过来的修改

# 6 . 删除本地分支  git branch -d xxxxx

      $ git checkout br-2.1.2.2  
      M       jingwei-server/src/main/java/com/taobao/jingwei/server/service/cmd/GetCustomerTarCmd.java  
      M       jingwei-server/src/main/java/com/taobao/jingwei/server/util/ServerUtil.java  
      Switched to branch 'br-2.1.2.2'

      $ git branch    
      * br-2.1.2.2  
        master  
        test  

      $ git br -d test  
      Deleted branch test (was 17d28d9).

      $ git branch    
      * br-2.1.2.2  
        master   

# 7 . 删除远程版本

      git push origin :br-1.0.0
      git branch -r -d origin/branch-name  
      git push origin :branch-name  
