---
layout: post
categories: 技术
tags: Linux   
---

## 方法一：

	root@kubernetes-master:~# fdisk -l | grep gpt
	Disklabel type: gpt  #如果有输出，则表示是UEFI系统，没有就是leagcy系统
	
##### GUID磁盘分区表（GUID Partition Table，缩写：GPT）其含义为“全局唯一标识磁盘分区表”，是一个实体硬盘的分区表的结构布局的标准。它是可扩展固件接口（EFI）标准（被Intel用于替代个人计算机的BIOS）的一部分，被用于替代BIOS系统中的一64bits来存储逻辑块地址和大小信息的主开机纪录（MBR）分区表。

## 方法二：

	root@kubernetes-master:~# ls /sys/firmware/efi/
	config_table  efivars  esrt  fw_platform_size  fw_vendor  runtime  runtime-map  systab  vars
	# 如果有文件表示是UEFI，如果没有表示是leagcy系统
	
