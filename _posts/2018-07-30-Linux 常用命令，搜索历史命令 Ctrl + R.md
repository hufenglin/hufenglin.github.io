
### 在 Linux 终端下查找历史命令，除了用 累赘的history 之外，有另外一个更加高效实用的命令：Ctrl + R。


#### 按下 Ctrl + R：会出现以下命令


	(reverse-i-search)`': 


#### 然后输入关键字 `qemu`会看到最近的一条有关键字`qemu`字符的命令：


	(reverse-i-search)`qemu': less qemucommand.log


#### 如果不是这条命令的话，可以再按下 Ctrl + R，Bash 会向前搜索有 `qemu` 字符的命令：


	[root@ccd-sdv4 ~]#
	(reverse-i-search)`qemu': ps -ef | grep qemu


#### 执行命令直接按下回车键，编辑命令按下左右方向键。