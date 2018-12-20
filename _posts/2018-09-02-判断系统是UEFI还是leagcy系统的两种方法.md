---
layout: post
categories: 技术
tags: Linux   
---

方法一：

	root@kubernetes-master:~# fdisk -l | grep gpt
	Disklabel type: gpt  #如果有输出，则表示是UEFI系统，没有就是leagcy系统

方法二：

	root@kubernetes-master:~# ls /sys/firmware/efi/
	config_table  efivars  esrt  fw_platform_size  fw_vendor  runtime  runtime-map  systab  vars
	# 如果有文件表示是UEFI，如果没有表示是leagcy系统
	
