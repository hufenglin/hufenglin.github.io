---
layout: post
categories: 技术
tags: Shell   
---

#### 1、变量声明为数值型

	declare -i num=12  

也可以查看变量类型

	declare -p num  

得到的结果如下


	declare -i num="12"  

#### 2、声明数组变量

（1）定义数组


	arr[0]=x  
	arr[1]=y  
	declare -a arr[2]=z  

（2）查看数组

	echo ${arr} ---结果x  
	echo ${arr[2]} ---结果z  
	echo ${arr[8]} ---结果x y z  

和普通变量区别是使用大括号，同时使用下标，若没有下标，则默认使用0。

#### 3、声明环境变量

	declare -x t=123  

export命令调用的也是上面declare -x 的命令

#### 4、声明变量只读属性

	declare -r a=123  

重新赋值会报错，不能修改，不能删除

#### 5、查看变量属性

	declare -p     查看所有变量属性  
	declare -p  变量名  查看指定变量的属性  

综上所述，declare声明变量总结如下：

	declare [+/-][选项] 变量名  
	选项：  
	   -：给变量舍得类型属性  
	   +：取消变量的类型属性  
	  -a：将变量声明为数组型  
	  -i：将变量声明为整型  
	  -x：将变量声明为环境变量  
	  -r：将变量声明为只读变量  
	  -p：查看变量的被声明的类型  
