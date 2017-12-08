# grep使用

----------
#### linux下通过grep来查找含有指定字符串的所有文件的方法。以查找Parameter这个字符串为例。

## 方法/步骤
### 1. grep用法
	            
	grep [options] PATTERN [FILE...]
	grep [选项] 要查找的字符串 查找范围(查找路径)  
	
###### 常用组合为：grep -rinl "要查找的字符串" 查找范围，rinl表示如下：
		
		-r 是递归查找，查找所有文件包含子目录
		-i 忽略大小写
		-n 是显示行号
		-l 只列出匹配的文件名
		-E 将范本样式为延伸的普通表示法来使用，意味着使用能使用扩展正则表达式。
		-o 只输出文件中匹配到的部分
		-q 不显示任何信息
		

### 2. grep -r递归查找

	1、不带-r查找范围不包括子目录，只是指定目录下的所有文件；   
	2、带-r时查找范围包括子目录。
   
        grep  -i "Parameter" /home/test/shell/*   
        grep  -ri "Parameter" /home/test/shell/*
       `*`: 表示当前目录所有文件，也可以是某个文件名  
![](https://imgsa.baidu.com/exp/pic/item/e49cf91190ef76c6a17baef49a16fdfaae516700.jpg)    
![](https://imgsa.baidu.com/exp/pic/item/c856613e6709c93d484a5a28983df8dcd0005471.jpg)

### 3. grep -i忽略大小写
    1、不带-i表示大小写敏感，表示要与查找字符串的大小写一致；
    2、带-i表示忽略大小写，大写和小写都可以。

    grep  -i "Parameter" /home/test/shell/*
    grep  -ri "Parameter" /home/test/shell/*   
![](https://imgsa.baidu.com/exp/pic/item/1a94b36eddc451dad1a05a34b1fd5266d116325f.jpg)

### 4. grep -n显示行数
    显示查找字符串在文件的行数位置。
    grep  -rn "Parameter" /home/test/shell/*      
![](https://imgsa.baidu.com/exp/pic/item/5af4d7ea15ce36d30503d2ef3df33a87e850b1af.jpg)   

### 5. grep -l只显示匹配的文件名。
	grep  -ri "Parameter" /home/test/shell/*
	grep  -ril "Parameter" /home/test/shell/*   
![](https://imgsa.baidu.com/exp/pic/item/86d5bac27d1ed21b46ce7d3aaa6eddc450da3fae.jpg)

### 6 . grep -E使用正则表达式 

	ls | grep -E "[1-9]+"  #输出当前目录下所有文件名字包含1-9的数字的文件名


### 7 . grep -o只输出文件中匹配到的部分

	echo this is a test line. | grep -o -E "[a-z]+\."  #输出前面是字母后面有.的单词
	line.
	echo this is a test line. | egrep -o "[a-z]+\."
	line.
	ls
		1224_offline_replay.log  1423_offline_replay.log  1642_offline_replay.log  1840_offline_replay.log  2380_offline_replay.log
		1241_offline_replay.log  1434_offline_replay.log  1692_offline_replay.log  2198_offline_replay.log  fvde
		1302_offline_replay.log  1507_offline_replay.log  1837_offline_replay.log  2212_offline_replay.log
	ls | grep -o -E "[0-9]+\_"  #输出当前目录下所有文件名字的数字后有_的部分
		1224_
		1241_
		1302_
		1423_
		1434_
		1507_
		1642_
		1692_
		1837_
		1840_
		2198_
		2212_
		2380_
		
### 8 . grep -q静默输出,不显示任何信息

	grep -q "test" filename #不会输出任何信息，如果命令运行成功返回0，失败则返回非0值。一般用于条件测试。
      
          
	        
		      
#### grep 显示前后几行的信息

### grep 后面带上-A -B -C 参数可以多显示几行内容

	grep -A 5 可以显示匹配内容以及后面的5行内容
	grep -B 5 可以显示匹配内容以及前面的5行内容
	grep -C 5 可以显示匹配内容以及前后面的5行内容
	
### RE（正则表达式）

	  \ 忽略正则表达式中特殊字符的原有含义 
	  ^ 匹配正则表达式的开始行 
	  $ 匹配正则表达式的结束行 
	  \< 从匹配正则表达式的行开始 
	  \> 到匹配正则表达式的行结束 
	  [] 单个字符；如[A] 即A符合要求 
	  [ - ] 范围 ；如[A-Z]即A，B，C一直到Z都符合要求 
	  . 所有的单个字符 
	  * 所有字符，长度可以为0  
### 举例

	 # cat size.txt | grep [a-b] 范围 ；如[A-Z]即A，B，C一直到Z都符合要求 
	   b124230 
	   b034325 
	   a061048 
	   b103303 
	   a013386 

	 # cat size.txt |  grep 'b'
	   b124230 
	   b034325 
	   b103303 

	 # cat size.txt | grep '[bB]' 
	   b124230 
	   b034325 
	   b103303 
	   B081016 
	   B103303 
	   BADc2345 

	 # more size.txt | grep -i 'b1..*3' -i ：忽略大小写  
	   b124230 
	   b103303 
	   B103303 

	 # more size.txt | grep -iv 'b1..*3' -v ：查找不包含匹配项的行 
	   b034325 
	   a081016 
	   m7187998 
	   m7282064 
	   a022021 
	   a061048 
	   m9324822 
	   a013386 
	   b044525 
	   m8987131 



	



