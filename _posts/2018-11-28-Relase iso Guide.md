##  一、Prepare

1.  **Download and decompress an Ubuntu ISO image.**

		//最好新建一个文件夹在其文件夹下面解压
		mkdir fenglin
		cd fenglin
		7z  x    ****.ISO

2.  **Enter the casper file, there should be an initrd.lz, an vmlinuz.efi, and filesystem.squashfs. They are the files we need to modify.**

		cd casper/

##  二、Update squashfs

1. **Decompress filesystem.squashfs**
 
		unsquashfs filesystem.squashfs

2. **进行挂载**

		mount --bind /run ./squashfs-root/run
		mount --bind /dev ./squashfs-root/dev
		mount --bind /proc ./squashfs-root/proc
		mount --bind /sys ./squashfs-root/sys

3. **切换成root**

		chroot ./squashfs-root

4.  **备份一个本机的源，后面还要更改回来，并更新**


		cd   etc/apt/
		mv  sources.list sources.list.bak  //备份一个本机的源   
	
		//重新登录host主机将在Tools目录下将source.list拷贝到casper这个系统
		cp /Tools/18.04-sources.list  fenglin/casper/squashfs-root/etc/apt/sources.list //拷贝一个内部Intel的源
		
		//在casper这个系统执行
		apt-get update


5.  **安装一些必要的包**
 
		apt install vim  gcc  git -y

6.  **vim /etc/environment添加：**

		export GIT_PROXY_COMMAND=/usr/local/bin/git-proxy.sh
		export no_proxy="localhost,127.0.0.1,.intel.com"
		export http_proxy="http://child-prc.intel.com:913/"
		export https_proxy="http://child-prc.intel.com:913/"
		export ftp_proxy="http://child-prc.intel.com:913/"

7. **配置git 代理**

		cp /Tools/git-proxy.sh  fenglin/casper/squashfs-root/usr/local/bin/
		source /etc/environment

8. **编译connect.c文件**

		cp /Tools/connect.c  fenglin/casper/squashfs-root/usr/local/bin/connect.c
		gcc /usr/local/bin/connect.c -o /usr/bin/connect


##  三、Build Kernel

1.  **Install packages here**

	    apt-get install libepoxy-dev libdrm-dev libgbm-dev spice-client-gtk libegl1-mesa-dev libgtk2.0-dev bin86 bcc iasl uuid-dev libncurses-dev libyajl-dev libc6-dev-i386  -y

2. **Build the initrd (initial ramdisk)**

	Use Ubuntu as example, modify /etc/initramfs-tools/modules, like below（加入如下命令）:

		vim /etc/initramfs-tools/modules
		
		kvmgt
		xengt	
		vfio-iommu-type1		
		vfio-mdev

	For Fedora or RHEL you should make the similar changes in "dracut".


3. **Build Kernel Source**
 
		在/root目录下操作最好

		git clone https://github.com/intel/gvt-linux.git		
		cd gvt-linux		
		git checkout gvt-stable-4.17	
		apt install bison flex -y	
		echo ""|make oldconfig   //如果这个步骤有错需要安装依赖包 apt install bison flex -y
		
		有错误也没事，只要出现下面的就可以了
		
		#
		# configuration written to .config
		#




4. **Then make sure to enable these parameters in ".config"**

		CONFIG_OVERLAY_FS=y
		CONFIG_DRM_I915_GVT=y
		CONFIG_DRM_I915_GVT_KVMGT=y
		CONFIG_DRM_I915_GVT_XENGT=y
		CONFIG_XEN_PVHVM=n

		CONFIG_VFIO_MDEV=y
		CONFIG_VFIO_MDEV_DEVICE=y

5. **安装依赖包**

		apt-get install git libfdt-dev libpixman-1-dev libssl-dev vim socat libsdl1.2-dev libspice-server-dev autoconf libtool xtightvncviewer tightvncserver x11vnc libsdl1.2-dev uuid-runtime uuid uml-utilities bridge-utils python-dev liblzma-dev libc6-dev libegl1-mesa-dev libepoxy-dev libdrm-dev libgbm-dev libelf-dev libsdl2-dev -y

6. **生成 Kernel 并安装的两种方法**

	一：

		make -j8 && make modules_install && make install

	二：If you use Ubuntu, you can generate the kernel debian package directly and install it with below commands:

		make -j8 deb-pkg
		cd ..
		dpkg -i linux-image-4.17.0+_4.17.0+-2_amd64.deb  //注意安装的是生成的linux-image-xxxxx

##  四、Build Qemu


1.  **Build Qemu for KVMGT**

		//put igvtg-qemu and igvtg-xen directory right under `/root`, 
		otherwise some path problems would occur later 

		cd /root     
 
		git clone https://github.com/intel/igvtg-qemu

		cd igvtg-qemu
		
		git checkout stable-2.12.0
		
		git submodule update --init roms/seabios
		
		./configure --prefix=/usr       \
        --enable-kvm            \
        --disable-xen           \
        --enable-debug-info     \
        --enable-debug          \
        --enable-sdl            \
        --enable-vhost-net      \
        --enable-spice          \
        --disable-debug-tcg     \
        --enable-opengl         \
        --target-list=x86_64-softmmu
		
		make -j8
		
		cd roms/seabios
		
		make -j8
		
		cd -
		
		make install
		
		cp roms/seabios/out/bios.bin /usr/bin/bios.bin

		cd /root

		rm -rf igvtg-qemu


2.  **Build Qemu & Xen for XenGT**


		cd /root

		git clone https://github.com/intel/igvtg-xen

		cd igvtg-xen
		
		git checkout xengt-stable-4.10
		
		git clone https://github.com/intel/igvtg-qemu
		
		cd igvtg-qemu
		
		git checkout stable-2.12.0
		
		cd ..
		
		sed -i 's/\(QEMU_UPSTREAM_REVISION ?= \).*/\1origin\/stable-2.12.0/g' Config.mk
		
		sed -i 's/\(QEMU_UPSTREAM_URL ?= \).*/\1file:\/\/localhost\/root\/igvtg-xen\/igvtg-qemu/g' Config.mk
		
		./autogen.sh
		
		./configure --prefix=/usr
		
		make -j8 xen tools
		
		make install-tools
		
		cp xen/xen.gz /boot/xen-gvt.gz


3. Delete `gvt-linux`, and  `igvtg-xen`  directories.

4. Delete old kernels and bash history, then exit and umount everything.
	
 
		查询系统中装了多少内核
		dpkg --get-selections|grep linux
        
		多余的内核可以通过命令删除 需要删除header和image		
		sudo apt-get remove linux-image-*.*.*-**（*号用你想删除的实际情况改写）
		sudo apt-get remove linux-headers-*.*.*-**（*号用你想删除的实际情况改写）

		执行完上面命令后再次执行
		dpkg --get-selections | grep linux

		查看内核是否都删除干净了。没干净继续删除。有的内核后面会显示是deinstall 那需要通过
		sudo dpkg --get-selections | grep deinstall | sed 's/deinstall/\lpurge/' | sudo dpkg --set-selections; sudo dpkg -Pa

		通过上面方法就可以。


5.  在`/etc/apt/`目录下换成默认的`sources.list`，将之前 备份的改回来。

6.  `apt-get update`

7.  `vim /etc/environment`删掉添加的：


		export GIT_PROXY_COMMAND=/usr/local/bin/git-proxy.sh
		export no_proxy="localhost,127.0.0.1,.intel.com"
		export http_proxy="http://child-prc.intel.com:913/"
		export https_proxy="http://child-prc.intel.com:913/"
		export ftp_proxy="http://child-prc.intel.com:913/"      


8.  删掉  `/usr/local/bin/git-proxy.sh`

9.  删掉  `/usr/bin/connect` 、 `/usr/local/bin/connect.c`



10. 清除历史，并退出,然后取消挂载。
        
	    history -c
		exit

		umount  squashfs-root/run
		umount squashfs-root/dev
		umount squashfs-root/proc
		umount squashfs-root/sys    //不能取消挂载直接重启即可


11. 拷贝/Tools/下面的grub.d  到 `fenglin/casper/squashfs-root/etc/grub.d` 来替换.

		cd /Tools
		cp grub.d /home/fei/fenglin/casper/squashfs-root/etc/  -r


12. 拷贝/Tools/下面的scripts 到 `fenglin/casper/squashfs-root/usr/share/script`, 并且将/Tools/Launch_Guest.desktop 拷贝到 `fenglin/squashfs-root/usr/share/applications/`.


		cp script /home/fei/fenglin/casper/squashfs-root/usr/share/ -r
		cp Launch_Guest.desktop /home/fei/fenglin/casper/squashfs-root/usr/share/applications/
		

13. Copy out

		fenglin/casper/squashfs-root/lib/modules/$YOUR_MODULES   //cp fenglin/casper/squashfs-root/lib/modules/4.17.0+  /root -r
		fenglin/casper/squashfs-root/usr/lib/syslinux/modules/efi64/mboot.c32   //find ./ -name mboot.c32  注意是efi64
		fenglin/casper/squashfs-root/boot/vmlinuz-4.17.0+ 
		fenglin/casper/squashfs-root/boot/xen-gvt.gz
		


14.  删除以前的filesystem.squashfs

		进入 fenglin/casper下面删掉filesystem.squashfs

15.  压缩现在修改好的squashfs-root
   
		mksquashfs squashfs-root filesystem.squashfs

16.  删掉`squashfs-root`

		rm -rf squashfs-root/

##  Update initrd

1. 将fenglin/casper/下面的initrd.lz拷贝出来然后将拷贝出来的重命名

		cp initrd.lz  /root/
		cd /root 
		mv initrd.lz initrd.lzma  //在目录下新建一个目录，下一步会解压很多文件出来
		lzma -d initrd.lzma
		cpio -idmv < initrd
		rm initrd


2.  Remove `lib/modules/*`, and then copy `$YOUR_MODULES` there.

		rm -rf lib/modules/*
		cp ../4.17.0+  ./lib/modules/  -r

3.  In `scripts/casper-bottom/25adduser`, around line 81, add the files you want to copy to the desktop.

		vim  scripts/casper-bottom/25adduser

		for file in /usr/share/applications/ubiquity.desktop /usr/share/applications/kde4/ubiquity-kdeui.desktop \
		    /usr/share/script/GVT-g_Setup_Guide.md /usr/share/applications/Launch_Guest.desktop /usr/share/script/README /usr/share/script/Combined_boot.sh; do
		    if [ -f "/root/$file" ]; then
		        sed -i "s/RELEASE/$RELEASE/" "/root$file"
		        chroot /root install -d -o $USERNAME -g $USERNAME /home/$USERNAME/Desktop
		        chroot /root install -D -o $USERNAME -g $USERNAME $file /home/$USERNAME/Desktop/$(basename "$file")
		        break
		    fi
		done


4.  Repackage initrd.lz


	      find ./* | cpio -H newc -o > ../initrd
          lzma initrd 
	      mv initrd.lzma initrd.lz


##  Update files in /casper

1.  用新的替换fenglin/casper下面的文件
	
		mv vmlinuz-4.17.0+   vmlinuz.efi  //更改之后就不需要修改后面的配置文件了，然后再替换到fenglin/casper下面
		filesystem.squashfs  //前面已经替换过
		initrd.lz
		
		注意删除fenglin/casper下面以前的vmlinuz


2. 在目录fenglin/casper下面添加刚刚拷贝出去的两个文件

		 xen-gvt.gz 
		 mboot.c32



## Modify ISO GRUB

1. 将/Tools下面的`grub.cfg` 、 `txt.cfg`替换到如下目录，并注意修改其中的内容：
		
		切换到 fenglin目录下
		cp /Tools/grub.cfg  ./boot/grub.cfg      //注意一定是/boot下面的grub
		cp /Tools/txt.cfg  ./isolinux/txt.cfg

		按照需求修改grub.cfg     //修改4处
		           txt.cfg      //修改3处
	


## Repackage ISO

1. 将`my.bin`拷贝到fenglin目录的上一层
 
		Find my.bin to support booting from ISO.
 
2. 在`fenglin`目录下，利用上一层的`my.bin`，并将ISO文件生成到上一层目录，执行如下命令：

		  xorriso -as mkisofs -isohybrid-mbr ../my.bin -D -r -V "Intel GVT-g" -J -l -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -eltorito-alt-boot -e boot/grub/efi.img -no-emul-boot -isohybrid-gpt-basdat -o ../ISO_NAME.iso .
		  //注意修改ISO_NAME.iso是你行要生成的ISO文件名字。

##  制作U盘启动，在Linux上利用Startup Disk 工具：

		 1. CD-Driver/image 选择制作好的ISO
		 2. Device 选择U盘
		 3. Make  Startup Disk
		
## 装系统

		 选择 Install Intel GVT-g



## 东方闪电

