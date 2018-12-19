---
layout: post
categories: 技术
tags: Cicada   
---

## 1 . 测试SSD

### 1 . 1 执行如下命令，查看设备信息的BDF号：

> 假设查看到的是`88:00.0`

	lspci | grep Non

其中不带`(rev 03)`的`Non-Volatile memory controller: Intel Corporation`那一行的最前面的序列号即为BDF号      
>#### spci命令解说：该命令能列出机器中的PCI设备信息，如声卡，显卡，Modem，网卡等信息，主板集成设备的信息也能列出来，lspci读取的是hwdata数据库。

### 1 . 2 在自己的当前目录下生成一个`.qcow`文件

	qemu-img create -b  /share/xvs/img/ia32e_rhel7u3_kvm.img -f qcow2 ia32e_rhel7u3_pcm.qcow

### 1 . 3 在`/root`目录下拷贝一个`vfio-pci.sh`到当前目录下

	scp /root/vfio-pci.sh .

### 1 . 4 当前目录下存在`ia32e_rhel7u3_pcm.qcow`与 `vfio-pci.sh`两个文件，执行如下命令，隐藏`BDF`号，之后在当前端口就看不到这个`BDF`号。      
> 目的：隐藏了BDF号在当前端口就看不到，就是为了让新起的guest里面可以使用

	./vfio-pci.sh -h 88:00.0

### 1 . 5 起guest

	qemu-system-x86_64 --enable-kvm -m 1024 -smp 1 -device vfio-pci,host=88:00.0 -hda ia32e_rhel7u3_pcm.qcow -monitor stdio -cpu Skymeadow -M q35 
	//其实如果是手动就用第一个就可以
	qemu-system-x86_64 --enable-kvm -m 1024 -smp 1 -device vfio-pci,host=88:00.0 -hda ia32e_rhel7u3_pcm.qcow -monitor stdio -cpu Skymeadow -M q35 -net nic,macaddr=f4:8e:38:af:73:1f,model=rtl8139 -net tap,script=/etc/kvm/qemu-ifup
    //如果自动的话就是用第二个，加了后面的其实就是为了获取IP

### 1 . 6 如果guest起来成功，另外打开一个终端输入

	vncviewer :5900

### 1 . 7 等待，在起来的guest中输入 

	lspci | grep Non

> 这时候就会有一个刚刚隐藏的SSD过来。

### 1 . 8 执行如下命令，如果有2000GB的设备显示，就说明SSD成功

	fdisk -l


## 2 . 测试 XL710网卡

### 2 . 1 执行如下命令，查看设备信息的BDF号：

> 假设查看到的是`88:00.0`

	lspci | grep Eth

其中不带`(rev 03)`的`Ethernet controller: Intel Corporation Ethernet Controller  10G XL710`那一行的最前面的序列号即为BDF号      
>#### spci命令解说：该命令能列出机器中的PCI设备信息，如声卡，显卡，Modem，网卡等信息，主板集成设备的信息也能列出来，lspci读取的是hwdata数据库。

### 2 . 2 在自己的当前目录下生成一个`.qcow`文件

	qemu-img create -b  /share/xvs/img/ia32e_rhel7u3_kvm.img -f qcow2 ia32e_rhel7u3_pcm.qcow

### 2 . 3 在`/root`目录下拷贝一个`vfio-pci.sh`到当前目录下

	scp /root/vfio-pci.sh .

### 2 . 4 当前目录下存在`ia32e_rhel7u3_pcm.qcow`与 `vfio-pci.sh`两个文件，执行如下命令，隐藏`BDF`号，之后在当前端口就看不到这个`BDF`号。      
> 目的：隐藏了BDF号在当前端口就看不到，就是为了让新起的guest里面可以使用

	./vfio-pci.sh -h 88:00.0

### 2 . 5 起guest

	qemu-system-x86_64 --enable-kvm -m 1024 -smp 1 -device vfio-pci,host=88:00.0 -hda ia32e_rhel7u3_pcm.qcow -monitor stdio -cpu Skymeadow -M q35 
	//其实如果是手动就用第一个就可以,网卡就‘一定不要’添加后面的上面SSD的第二个后面的内容，因为网卡可以直接分配一个IP，添加就会出错。
	

### 2 . 6 如果guest起来成功，另外打开一个终端输入

	vncviewer :5900

### 2 . 7 等待，在起来的guest中输入 

	lspci | grep Eth

> 这时候就会有一个刚刚隐藏的网卡过来。显示有XL710即可。



## 3 . 测试 X550 网卡

### 3 . 1 执行如下命令，查看设备信息的BDF号：

> 假设查看到的是`88:00.0`与`88:00.1`，X550网卡有两个BDF号，一定都要隐藏



	lspci | grep Eth

其中不带`(rev 03)`的`Ethernet controller: Intel Corporation Ethernet Controller  10G X550`那一行的最前面的序列号即为BDF号      
>#### spci命令解说：该命令能列出机器中的PCI设备信息，如声卡，显卡，Modem，网卡等信息，主板集成设备的信息也能列出来，lspci读取的是hwdata数据库。

### 3 . 2 在自己的当前目录下生成一个`.qcow`文件

	qemu-img create -b  /share/xvs/img/ia32e_rhel7u3_kvm.img -f qcow2 ia32e_rhel7u3_pcm.qcow

### 3 . 3 在`/root`目录下拷贝一个`vfio-pci.sh`到当前目录下

	scp /root/vfio-pci.sh .

### 3 . 4 当前目录下存在`ia32e_rhel7u3_pcm.qcow`与 `vfio-pci.sh`两个文件，执行如下命令，隐藏`BDF`号，之后在当前端口就看不到这个`BDF`号。      
> 目的：隐藏了BDF号在当前端口就看不到，就是为了让新起的guest里面可以使用，《`X550网卡有两个BDF号，一定都要隐藏`》

	./vfio-pci.sh -h 88:00.0
	./vfio-pci.sh -h 88:00.1

### 3 . 5 起guest

	qemu-system-x86_64 --enable-kvm -m 1024 -smp 1 -device vfio-pci,host=88:00.0 -hda ia32e_rhel7u3_pcm.qcow -monitor stdio -cpu Skymeadow -M q35 
	//其实如果是手动就用第一个就可以,网卡就‘一定不要’添加后面的上面SSD的第二个后面的内容，因为网卡可以直接分配一个IP，添加就会出错。
	

### 3 . 6 如果guest起来成功，另外打开一个终端输入

	vncviewer :5900

### 3 . 7 等待，在起来的guest中输入 

	lspci | grep Eth

> 这时候就会有一个刚刚隐藏的网卡过来。显示有X550 即可。
