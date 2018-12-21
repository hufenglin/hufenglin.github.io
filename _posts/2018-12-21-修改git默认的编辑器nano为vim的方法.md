### git提交后出现nano界面,解决方法

### git默认的编辑器是nano，使用起来感觉很别扭，通过下面命令可以将git默认的编辑器变为vim.


方法一：


	打开.git/config文件，在core中添加 editor=vim即可。

方法二：

	git config --global core.editor vim

