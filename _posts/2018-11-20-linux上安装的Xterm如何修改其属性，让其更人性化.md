XTerm是Linux系统中常用的一个终端模拟软件,但其丑陋的默认字体让人无法忍受,google了一下相关资料,将修改方法记录如下:

### 1.建立~/.Xresources文件,可参考下列内容:

	XTerm*faceName: Bitstream Vera Serif Mono
	xterm*faceSize: 18
	xterm*vt100*geometry: 100x50
	xterm*saveLines: 16384
	xterm*loginShell: true
	xterm*charClass: 33:48,35:48,37:48,43:48,45-47:48,64:48,95:48,126:48
	xterm*termName: xterm-color
	xterm*eightBitInput: false
	XTerm.vt100.foreground: white
	XTerm.vt100.background: black




### 2.在~/.bashrc目录下增加下面一句话来运行xrdb(X server resource database utility):
	
	
	xrdb -merge ~/.Xresources

###  改变可执行权限

	chmod a+x  ~/.bashrc

OK,好看的XTerm准备就绪.


[参考手册]("https://wiki.archlinux.org/index.php/Xterm")
