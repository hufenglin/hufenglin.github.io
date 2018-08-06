###  准备一个分区格式必须是FAT32的U盘，拷一个eeupdate工具
[eeupdate工具下载](eeupdate工具 "https://github.com/hufenglin/hufenglin.github.io/blob/master/_posts/change%20linux%20mac%20address.zip")

### 1. 按F2进入BIOS选项

### 2. Driver Option ----> BOOT Manager Menu ---> UEFI Internal Shell

### 3. 然后会出现命令行 `Shell>`，输入

	Shell>fs0:
	FS0:\>ls
	FS0:\>cd "change linux mac address"
	FS0:\>ls
	FS0:\>cd EFI2_x64
	FS0:\>ls
	FS0:\>cd OEM_Mfg
	FS0:\>ls
	FS0:\>eeupdate64e.efi /all  /mac_dump   //此时会出现1：LAN MAC Address is 888888

### 选择需要刷的LAN的MAC地址

	FS0:\>eeupdate64e.efi /nic=1  /mac=90e2ba021903
			90e2ba 是固定的
			0219 是上面显示 Intel(R) Ethernet Connection (3) I219-LM
			03 是表示SDV3
	FS0:\>eeupdate64e.efi /all  /mac_dump   
            看是否MAC地址已经刷回来


	



	