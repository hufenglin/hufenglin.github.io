
Linux ssh下执行ssh命令远程登录其他机器，总是需要输入密码，如果人工去登录，输入密码那还可以，但是让程序自动化登录远程ssh服务器，并执行命令着就比较麻烦了。

Linux下有个程序是`expect`，它可以模拟键盘，输入文本。

### 1 . 安装expect
机器上一般是没有这个命令的，需要安装expect

	sudo apt-get install expect
	yum install expect

经常看到一些脚本有会expect、spawn、send关键字，这几个关键字都是在expect程序里面使用的。

### 2 . 使用expect脚本运行
	
	#!/usr/bin/expect
	 
	set timeout 5
	 
	password=ppp
	 
	spawn ssh user@localhost -p 22
	expect {
	    "(yes/no)" { send "yes\r"; exp_continue }
	    "password:" { send "$password\r" }
	}
	expect user@*   {send \" ls -l \r\" }  ;
	expect user@*  { send exit\r } ;
	expect 100% ;
	expect eof ;

- 注意第一行使用的是#!/usr/bin/expect而不是普通的bash脚本那样
- expect都是使用{}，且{、}使用时，前后需要留空格
- 例子使用花括号，表示使用一组并列表达式，只要其中一项符合，就会执行该项，类似switch

### 3 . 使用命令行参数运行expect

有时候写个expect脚本比较麻烦，直接只用命令行参数去执行命令，或者嵌套在shell脚本、python脚本中，这样可以减少expect脚本的数量

一下是一个登录并执行一个ls命令的demo：

	expect -c "
	           set timeout 1;
	           spawn ssh user@localhost -p 22  ;
	           expect {
	               yes/no { send \"yes\r\"; exp_continue }
	               *assword* { send \"password\r\" }
	           } ;
	           expect user@*   {send \" ls -l \r\" }  ;
	           expect user@*  { send exit\r } ;
	           expect eof ;
	       "

expect的参数-c后面是字符串，里面就相当于脚本里面的内容了。

- 用;分号隔开，可以在同一行里。      
- 里面的"引号，使用\"来代替。      
- 第9行的exit最好要有。退出ssh，这样程序不会阻塞。我如果没有使用，在这个shell命令执行完，无法立刻输入字符。      
- expect脚本必须以interact或expect eof结束，执行自动化任务通常expect eof就够了      

### expect与scp使用

expect一般用于登录ssh服务器，除了ssh命令输入密码外，还有scp也是需要密码输入的，因此expect与scp结合起来使用的场景也是很多的。

	expect -c "
	           set timeout 1;
	           spawn scp -P 22 user@remoteHost:/tmp/file.txt ~/  ;
	           expect {
	               yes/no { send \"yes\r\"; exp_continue }
	               *assword* { send \"password\r\" }
	           } ;
	           expect user@*   {send \" ls -l \r\" }  ;
	           expect user@*  { send exit\r } ;
	           expect 100% ;
	           expect eof ;
	       "

#### 注意：scp的制定端口使用大写的-P。

python中使用expect命令

	SCP_CMD_BASE = r"""
	            expect -c "
	            set timeout 1;
	            spawn scp -P {port} {username}@{host}:/tmp/{{filename}} ./proxydownload/  ;
	            expect *assword* {{{{ send {password}\r }}}}  ;
	            expect *\r
	            expect \r
	            expect eof
	            "
	    """.format(username=username,password=password,host=host,port=port)
	 
	cmd = SCP_CMD_BASE.format(filename = filename)
	print "cmd=", cmd
	p  = subprocess.Popen( cmd , stdout=subprocess.PIPE, stderr=subprocess.PIPE, shell=True)
	(output, err) = p.communicate()

### 其他的相关知识
Python对expect也有类库支持，也就是说你可以直接使用python编写expect，而不用安装expect程序了。可以执行搜索一下python pexpect。      

用expect速度会比较慢，因为需要等待返回的数据，然后输入命令执行。没有ssh密钥登录的快速。





