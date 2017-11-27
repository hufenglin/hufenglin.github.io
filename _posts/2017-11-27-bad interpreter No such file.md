### 今天在WingIDE下写了个脚本，传到服务器执行后提示：

     -bash: /usr/bin/autocrorder: /usr/bin/python^M: bad interpreter: No such file or directory

#### 分析：
  这是不同系统编码格式引起的：在windows系统中编辑的.sh .py文件可能有不可见字符，所以在Linux系统下执行会报以上异常信息。一般是因为windows行结尾和linux行结尾标识不同造成的。

### 解决：

#### 1）在windows下转换： 

利用一些编辑器如UltraEdit或EditPlus等工具先将脚本编码转换，再放到Linux中执行。转换方式如下（UltraEdit）：File-->Conversions-->DOS->UNIX即可。 

#### 2）linux下直接替换：

sed -i 's/^M//g'  filename （注意^M 在linux 下写法 按^M 是回车换行符,输入方法是按住CTRL+v,松开v,按m)

#### 3）也可在Linux中转换： 

 3.1 .首先要确保文件有可执行权限 

      #sh>chmod a+x filename 

 3.2 .然后修改文件格式 

      #sh>vi filename 

利用如下命令查看文件格式 

      :set ff 或 :set fileformat 

可以看到如下信息 

      fileformat=dos 或 fileformat=unix 

利用如下命令修改文件格式 

      :set ff=unix 或 :set fileformat=unix 
      :wq (存盘退出) 

最后再执行文件 

      #sh>./filename 