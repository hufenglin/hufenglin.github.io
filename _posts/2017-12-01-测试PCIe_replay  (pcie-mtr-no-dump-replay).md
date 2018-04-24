# 测试PCIe_replay  (pcie-mtr-no-dump-replay)

## 一：打开串口log机器192.168.199.193  !!!注意一定是串口机器

>首先搞清楚，机器log(即 host log)<通过九插针的读取>；；      
>扩展卡log（即 mcu log）<通过USB线读取>


### 1 . 1 查看ccdcomx对应的ttyUSBx,ccdcom对应固定的机器，而ttyUSBx与之ccdcomx对应关系会变化。

>sdv5->ccdcom0;;sdv3->ccdcom1;;sdv8->ccdcom2

	ll /dev/
	[root@cicada-serial-port ~]# ll /dev/
	total 0
	crw-------  1 root root     10, 235 Mar 26 01:50 autofs
	drwxr-xr-x  2 root root         160 Mar 26 01:50 block
	drwxr-xr-x  2 root root          60 Mar 26 01:50 bsg
	crw-------  1 root root     10, 234 Mar 26 01:50 btrfs-control
	drwxr-xr-x  3 root root          60 Mar 26 01:50 bus
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom0 -> ttyUSB1
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom1 -> ttyUSB0
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom10 -> ttyUSB7
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom11 -> ttyUSB5
	lrwxrwxrwx  1 root root           8 Mar 26 01:50 ccdcom12 -> ttyUSB11
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom13 -> ttyUSB9
	lrwxrwxrwx  1 root root           8 Mar 26 01:50 ccdcom14 -> ttyUSB14
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom2 -> ttyUSB4
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom3 -> ttyUSB2
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom4 -> ttyUSB8
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom5 -> ttyUSB6
	lrwxrwxrwx  1 root root           8 Mar 26 01:50 ccdcom6 -> ttyUSB12
	lrwxrwxrwx  1 root root           8 Mar 26 01:50 ccdcom7 -> ttyUSB10
	lrwxrwxrwx  1 root root           7 Mar 26 01:50 ccdcom8 -> ttyUSB3
	lrwxrwxrwx  1 root root           8 Mar 26 01:50 ccdcom9 -> ttyUSB13


### 1 . 2 知道机器对应的ttyUSBx，所抓的log是机器log（host log）

>假设通过`ll /dev/` 查询出来知道ccdcom0对应的是ttyUSB3      

	minicom -D /dev/ttyUSB3 -b 115200 -C 2017-12-19-pcie-host.log

### 1 . 3 通过拔插扩展卡上的USB，可以知道对应的扩展卡log（mcu log）对应的ttyUSBx

>ls /dev/ttyUSB*     看看缺少了哪个，就可以确定了比如说是USB5

	minicom -D /dev/ttyUSB5 -b 9600 -C 2017-12-19-pcie-mcu.log

	

## 二：修改Kernel Cmdline

### 2 . 1 开机，进入i_lab,执行以下命令：

	su root

### 2 . 2 修改grub

	vim  /boot/efi/EFI/redhat/grub.cfg

### 2 . 3 进行修改

     在 linuxefi /vmlinuz 的后面修改配置。

## 三：安装rpm包

### 3 . 1 查看已安装的rpm包

	rpm -qa | grep kvm 

### 3 . 2 如果上一步有已有的rpm包，就删除已有的

	rpm -e kvm-3.10.0-f68c1e4c_3c4269a6_20171109.x86_64 

### 3 . 3 获取了最新的rpm包

	wget http://cicada-master.sh.intel.com/kvm-3.10.0-ff907d18_3c4269a6_20171114.x86_64.rpm 《注意更换新地址》

### 3 . 4 安装最新的rpm包

	rpm -ivh kvm-3.10.0-ff907d18_3c4269a6_20171114-weekly-release.x86_64.rpm 《换地址》

### 3 . 5 .如果报错的话请安装

	yum install ***

## 四：隐藏网卡

### 4 . 1 执行如下命令，查看设备信息的BDF号：

	lspci | grep Eth

### 4 . 2 在自己的当前目录下生成一个.qcow文件

	qemu-img create -b  /share/xvs/img/ia32e_rhel7u3_kvm.img -f qcow2 ia32e_rhel7u3_pcm.qcow

### 4 . 3 在/root目录下拷贝一个vfio-pci.sh到当前目录下

	scp /root/vfio-pci.sh .

### 4 . 4 当前目录下存在ia32e_rhel7u3_pcm.qcow与 vfio-pci.sh两个文件，执行如下命令，隐藏BDF号，之后在当前端口就看不到这个BDF号。

> ！！！注意如果网卡是710、550就必须隐藏两个。！！！

	./vfio-pci.sh -h 88:00.0
	./vfio-pci.sh -h 88:00.1

## 五：起Guest

### 5 . 1 起guest，输入以下命令

	qemu-system-x86_64 -enable-kvm -m 4096 -smp 1 -device vfio-pci,host=88:00.0  -cpu Broadwell,-avx,-avx2 -M q35 -hda  ia32e_rhel7u3_workload.qcow  -monitor stdio   

### 5 . 2  如果guest起来成功，在i_lab中输入

	vncviewer :5900

### 5 . 3 在起来的guest（vncviewer :5900）中获取IP

	dhclient

###  5 . 4  运行netserver

> 如果没有`netserver`，可以从43机器上拷贝`/usr/local/bin/netserver`      
> ！！！ 必须把43机器上的`/usr/local/bin/netperf`也拷贝过来！！！

	./netserver

## 六 ： 在Xmoban开启一个终端，连接到host

###	6 . 1 直接ls,查看里面是否有`auto_netperf`，如果有打开查看，如果没有，进行新建，里面代码如下：

		set -x
		IP=$1
		while [ 1 ]
		do
		#echo jl
		for b in 32 64 128 256 512 1024 1460 2048 4096 8192  9000 16384 32768  65495 65507
		
		do ./netperf -t TCP_STREAM -f m -H $IP -P 0 -l 10 -- -m $b
		
		done
		
		for b in 32 64 128 256 512 1024 1460 2048 4096 8192  9000 16384 32768  65495 65507
		
		do ./netperf -t UDP_STREAM -f m -H $IP -P 0 -l 10 -- -m $b
		
		done
		
		sleep 10
		done

### 6 . 2 然后执行脚本，开始测试

	./auto_netperf 192.168.199.7

>192.168.199.7的地址是起来的guest里面的IP地址




## 七 ： 如何查看cmdline

	 cat /proc/cmdline


