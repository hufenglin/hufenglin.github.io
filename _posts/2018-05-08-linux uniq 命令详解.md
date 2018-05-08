## Linux uniq命令

Linux uniq命令用于检查及删除文本文件中重复出现的行列。

uniq可检查文本文件中重复出现的行列。


## 语法

	uniq [-cdu][-f<栏位>][-s<字符位置>][-w<字符位置>][--help][--version][输入文件][输出文件]

## 参数：

- -c或--count 在每列旁边显示该行重复出现的次数。
- -d或--repeated 仅显示重复出现的行列。
- -u或--unique 仅显示出一次的行列。
- -f<栏位>或--skip-fields=<栏位> 忽略比较指定的栏位。
- -s<字符位置>或--skip-chars=<字符位置> 忽略比较指定的字符。
- -w<字符位置>或--check-chars=<字符位置> 指定要比较的字符。
- --help 显示帮助。
- --version 显示版本信息。
- [输入文件] 指定已排序好的文本文件。
- [输出文件] 指定输出的文件。

## 补充

文件经过处理后在它的输出文件中可能会出现重复的行。例如，使用cat命令将两个文件合并后，再使用sort命令进行排序，就可能出现重复行。这时可以使用uniq命令将这些重复行从输出文件中删除，只留下每条记录的唯一样.


## 示例

要删除名为 fruit 文件中的重复行并将其保存到一个名为 newfruit 的文件中，输入：

	uniq fruit newfruit

如果 fruit 文件包含下列行:

	apples
	apples
	peaches
	pears
	bananas
	cherries
	cherries

则在您运行uniq 命令后 newfruit 文件将包含下列行:

	apples
	peaches
	pears
	bananas
	cherries

文件/usr/bin/uniq 包含 uniq 命令。

 uniq -c 的用法，例如：

	harley
	casely
	weedly
	harley
	linda

.

	cut -c 1-8 | sort | uniq -c > result.txt

	1 casely
	2 harley
	1 linda
	1 weekly

1. 显示文件example中不重复的行。

		uniq - u example

2. 显示文件example中不重复的行，从第2个字段的第2个字符开始做比较。

		uniq - u - 1 +1 example

