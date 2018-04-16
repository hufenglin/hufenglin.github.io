本文链接：http://codingstandards.iteye.com/blog/804830   （转载请注明出处）

## 用途说明

hwclock命令，与clock命令是同一个命令，主要用来查询和设置硬件时钟（query and set the hardware clock (RTC)）。RTC=Real Time Clock，也就是硬件时钟。在Linux中有硬件时钟与系统时钟等两种时钟。

#### 硬件时钟是指主机板上的时钟设备，也就是通常可在BIOS画面设定的时钟。

#### 系统时钟则是指kernel中 的时钟。所有Linux相关指令与函数都是读取系统时钟的设定。

因为存在两种不同的时钟，那么它们之间就会存在差异。根据不同参数设置，hwclock命令既可以将硬件时钟同步到系统时钟，也可以将系统时钟同步到硬件时钟。

### 关于系统时钟与硬件时钟的关系的说法，网上分成了两种：

- 第一种：当Linux启动时，硬件时钟会去读取系统时钟的设置，然后系统时钟就会独立于硬件运作。

- 第二种：当Linux启动时，系统时钟会去读取硬件时钟的设定，之后系统时钟即独立运作。

到底那一种才是正确的呢？执行一下man hwclock可以看到里面有一段：

## man hwclock 写道

The System Time is the time that matters. The Hardware Clock’s basic purpose in a Linux system is to keep time
when Linux is not running. You initialize the System Time to the time from the Hardware Clock when Linux
starts up, and then never use the Hardware Clock again. Note that in DOS, for which ISA was designed, the
Hardware Clock is the only real time clock.

这段文字说明，第二种说法是正确的，第一种说法是错误的。但网上大部分文章都是第一种说法，所以说，网络虽好，要注意甄别。

man hwclock 写道
It is important that the System Time not have any discontinuities such as would happen if you used the date(1L)
program to set it while the system is running. You can, however, do whatever you want to the Hardware Clock
while the system is running, and the next time Linux starts up, it will do so with the adjusted time from the
Hardware Clock.

如果使用date命令修改了系统时间，并不会自动去修改硬件时钟，因此，当系统下次重启时，系统时钟还会从硬件时钟去取，date设置的时间就无效了。这大概就是为什么需要hwclock命令的原因吧。

## 常用参数

	-r, --show        读取并打印硬件时钟（read hardware clock and print result 
	-s, --hctosys     将硬件时钟同步到系统时钟（set the system time from the hardware clock ）
	-w, --systohc     将系统时钟同步到硬件时钟（set the hardware clock to the current system time ）

## 使用示例

### 示例一 hwclock命令与clock命令是一个东西

	[root@new55 ~]# type -a hwclock 
	hwclock is /sbin/hwclock
	hwclock is /usr/sbin/hwclock
	[root@new55 ~]# ls -l /sbin/hwclock /usr/sbin/hwclock 
	-rwxr-xr-x 1 root root 34096 2010-01-07 /sbin/hwclock
	lrwxrwxrwx 1 root root    18 08-13 00:14 /usr/sbin/hwclock -> ../../sbin/hwclock

	[root@new55 ~]# type -a clock 
	clock is /sbin/clock
	[root@new55 ~]# ls -l /sbin/clock 
	lrwxrwxrwx 1 root root 7 08-13 00:14 /sbin/clock -> hwclock
	[root@new55 ~]#

### 示例二 显示硬件时钟

	[root@new55 ~]# hwclock 
	2010年11月06日 星期六 21时09分28秒  -0.134840 seconds
	[root@new55 ~]# hwclock -r 
	2010年11月06日 星期六 21时09分33秒  -0.469123 seconds
	[root@new55 ~]# hwclock --show 
	2010年11月06日 星期六 21时09分45秒  -0.127548 seconds
	[root@new55 ~]#

 

### 示例三 修改日期并同步到硬件时钟

	[root@new55 ~]# date 
	2010年 11月 06日 星期六 21:11:57 CST
	[root@new55 ~]# date 11062112     <== 格式 mmddHHMM，即 月日时分 
	2010年 11月 06日 星期六 21:12:00 CST
	[root@new55 ~]# hwclock -w 
	[root@new55 ~]# hwclock 
	2010年11月06日 星期六 21时12分13秒  -0.648818 seconds
	[root@new55 ~]#

 

注：要使系统时间准确，最好还是使用ntp方式。

### 示例四 使用ntpdate命令从网络同步时间，再同步到硬件时钟

ntpdate命令是使用NTP协议来从网络同步时间的命令。NTP=Network Time Protocol 网络时间协议。

	[root@new55 ~]# ntpdate 0.rhel.pool.ntp.org 
	 6 Nov 21:17:55 ntpdate[4829]: step time server 123.146.124.28 offset -1.025258 sec
	[root@new55 ~]# date 
	2010年 11月 06日 星期六 21:17:59 CST
	[root@new55 ~]# hwclock -w 
	[root@new55 ~]#

问题思考
1. 为什么Linux要将时钟分为系统时钟和硬件时钟，这样做有什么好处？