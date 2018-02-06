
# KVM简介

　　KVM（名称来自英语：Kernel-basedVirtual Machine的缩写，即基于内核的虚拟机），是一种用于Linux内核中的虚拟化基础设施，可以将Linux内核转化为一个hypervisor。KVM在2007年2月被导入Linux 2.6.20核心中，以可加载核心模块的方式被移植到FreeBSD及illumos上。       
　　KVM在具备Intel VT或AMD-V功能的x86平台上运行。它也被移植到S/390，PowerPC与IA-64平台上。在Linux内核3.9版中，加入ARM架构的支持。	

### 关于KVM：



- 1）.KVM是开源软件，全称是kernel-based virtual machine（基于内核的虚拟机）。      
- 2）.是x86架构且硬件支持虚拟化技术（如 intel VT 或 AMD-V）的Linux全虚拟化解决方案。      
- 3）.它包含一个为处理器提供底层虚拟化 可加载的核心模块kvm.ko（kvm-intel.ko或kvm-AMD.ko）。      
- 4）.KVM还需要一个经过修改的QEMU软件（qemu-kvm），作为虚拟机上层控制和界面。      
- 5）.KVM能在不改变linux或windows镜像的情况下同时运行多个虚拟机，（它的意思是多个虚拟机使用同一镜像）并为每一个虚拟机配置个性化硬件环境（网卡、磁盘、图形适配器……）。      
- 6）.在主流的Linux内核，如2.6.20以上的内核均已包含了KVM核心。


## kvm 是linux内核包含的东西，使用qemu作为上层管理（命令行）。

- 1）.要求cpu 必须支持虚拟化。
- 2）.性能：作为服务器很好，可是图形能力十分的差。即使放电影，图像也是像刷油漆一样，一层一层的。
- 3）.cpu使用率控制很好。
- 4）.控制上比较简洁，功能比较丰富：比如使用“无敌功能”所有更改指向内存，你的镜像永远保持干净。“母镜像”功能让你拥有n个独立快照点。还有很多参数。另外，kvm作为内核级的虚拟机，刚开始发展关注的公司比较多——但是还没有达到商业应用的水平。

　　总体而言：在支持虚拟化的情况下，vbox 和 kvm 的性能差不多，主要是面向对象不同：kvm适用于服务器，vbox适用于桌面应用。
 
　　qemu 全称Quick Emulator。是独立虚拟软件，能独立运行虚拟机（根本不需要kvm）。kqemu是该软件的加速软件。kvm并不需要qemu进行虚拟处理，只是需要它的上层管理界面进行虚拟机控制。虚拟机依旧是由kvm驱动。所以，大家不要把概念弄错了，盲目的安装qemu和kqemu。qemu使用模拟器.

# KVM内存管理

 　　KVM继承了Linux系统管理内存的诸多特性，比如，分配给虚拟使用的内存可以被交换至交换空间、能够使用大内存页以实现更好的性能，以及对NUMA的支持能够让虚拟机高效访问更大的内存空间等。       
 　　KVM基于Intel的EPT（ExtendedPage Table）或AMD的RVI（Rapid Virtualization Indexing）技术可以支持更新的内存虚拟功能，这可以降低CPU的占用率，并提供较好的吞吐量。        
 　　此外，KVM还借助于KSM（Kernel Same-pageMerging）这个内核特性实现了内存页面共享。KSM通过扫描每个虚拟机的内存查找各虚拟机间相同的内存页，并将这些内存页合并为一个被各相关虚拟机共享的单独页面。在某虚拟机试图修改此页面中的数据时，KSM会重新为其提供一个新的页面副本。实践中，运行于同一台物理主机上的具有相同GuestOS的虚拟机之间出现相同内存页面的概率是很的，比如共享库、内核或其它内存对象等都有可能表现为相同的内存页，因此，KSM技术可以降低内存占用进而提高整体性能。

### 相关连接：

KVM的官方地址： [http://www.linux-kvm.org/page/Main_Page](http://www.linux-kvm.org/page/Main_Page)

KVM的Howto文档：  [http://www.linux-kvm.org/page/HOWTO](http://www.linux-kvm.org/page/HOWTO)

Kqemu源码地址： [http://sourceforge.net/projects/kqemu/](http://sourceforge.net/projects/kqemu/)

Qemu下载地址： [http://wiki.qemu.org/Main_Page](http://wiki.qemu.org/Main_Page)

#使用qemu-kvm管理KVM虚拟机

### 1 . Qemu-kvm介绍 

 　　Qemu是一个广泛使用的开源计算机仿真器和虚拟机。当作为仿真器时，可以在一种架构(如PC机)下运行另一种架构(如ARM)下的操作系统和程序。而通过动态转化，其可以获得很高的运行效率。当作为一个虚拟机时，qemu可以通过直接使用真机的系统资源，让虚拟系统能够获得接近于物理机的性能表现。qemu支持xen或者kvm模式下的虚拟化。当用kvm时，qemu可以虚拟x86、服务器和嵌入式powerpc，以及s390的系统。

 　　QEMU 当运行与主机架构相同的目标架构时可以使用KVM。例如，当在一个x86兼容处理器上运行 qemu-system-x86 时，可以利用 KVM 加速——为宿主机和客户机提供更好的性能。

### 2 . Qemu有如下几个部分组成：

- 处理器模拟器(x86、PowerPC和Sparc)；      
- 仿真设备(显卡、网卡、硬盘、鼠标等)；      
- 用于将仿真设备连接至主机设备(真实设备)的通用设备；      
- 模拟机的描述信息；      
- 调试器；      
- 与模拟器交互的用户接口；      

基于libvirt的工具如virt-manager和virt-install提供了非常便捷的虚拟机管理接口，但它们事实上上经二次开发后又封装了qemu-kvm的工具。因此，直接使用qemu-kvm命令也能够完成此前的任务。

qemu-kvm命令使用格式为:      

	qemu-kvm [options] [disk_image]  
   
大致可分为如下几类。

- 标准选项；
- USB选项；
- 显示选项；
- i386平台专用选项；
- 网络选项；
- 字符设备选项；
- 蓝牙相关选项；
- Linux系统引导专用选项；
- 调试/专家模式选项；
- PowerPC专用选项；
- Sparc32专用选项；

### qemu-kvm的标准选项

qemu-kvm的标准选项主要涉及指定主机类型、CPU模式、NUMA、软驱设备、光驱设备及硬件设备等。

- -name name：设定虚拟机名称；      
- -M machine：指定要模拟的主机类型，如Standard PC、ISA-only PC或Intel-Mac等，可以使用“qemu-kvm -M ?”获取所支持的所有类型；      
- -m megs：设定虚拟机的RAM大小；      
- -cpu model：设定CPU模型，如coreduo、qemu64等，可以使用“qemu-kvm -cpu ?”获取所支持的所有模型；      
- -smp n[,cores=cores][,threads=threads][,sockets=sockets][,maxcpus=maxcpus]：设定模拟的SMP架构中CPU的个数等、每个CPU的核心数及CPU的socket数目等；PC机上最多可以模拟255颗CPU；maxcpus用于指定热插入的CPU个数上限；      
- -numa opts：指定模拟多节点的numa设备；      
- -fda file      
- -fdb file：使用指定文件(file)作为软盘镜像，file为/dev/fd0表示使用物理软驱；      
- -hda file      
- -hdb file      
- -hdc file      
- -hdd file：使用指定file作为硬盘镜像；      
- -cdrom file：使用指定file作为CD-ROM镜像，需要注意的是-cdrom和-hdc不能同时使用；将file指定为/dev/cdrom可以直接使用物理光驱；      
- -drive option[,option[,option[,...]]]：定义一个硬盘设备；可用子选项有很多。    
  
		file=/path/to/somefile：硬件映像文件路径；      
		if=interface：指定硬盘设备所连接的接口类型，即控制器类型，如ide、scsi、sd、mtd、floppy、pflash及virtio等；      
		index=index：设定同一种控制器类型中不同设备的索引号，即标识号；      
		media=media：定义介质类型为硬盘(disk)还是光盘(cdrom)；      
		snapshot=snapshot：指定当前硬盘设备是否支持快照功能：on或off；  
	    cache=cache：定义如何使用物理机缓存来访问块数据，其可用值有none、writeback、unsafe和writethrough四个；
	    format=format：指定映像文件的格式，具体格式可参见qemu-img命令；  
     
- -boot [order=drives][,once=drives][,menu=on|off]：定义启动设备的引导次序，每种设备使用一个字符表示；不同的架构所支持的设备及其表示字符不尽相同，在x86 PC架构上，a、b表示软驱、c表示第一块硬盘，d表示第一个光驱设备，n-p表示网络适配器；默认为硬盘设备；
- -boot order=dc,once=d      

###  qemu-kvm的显示选项

显示选项用于定义虚拟机启动后的显示接口相关类型及属性等。

- -nographic：默认情况下，qemu使用SDL来显示VGA输出；而此选项用于禁止图形接口，此时,qemu类似一个简单的命令行程序，其仿真串口设备将被重定向到控制台；
- -curses：禁止图形接口，并使用curses/ncurses作为交互接口；
- -alt-grab：使用Ctrl+Alt+Shift组合键释放鼠标；
- -ctrl-grab：使用右Ctrl键释放鼠标；
- -sdl：启用SDL；
- -spice option[,option[,...]]：启用spice远程桌面协议；其有许多子选项，具体请参照qemu-kvm的手册；
- -vga type：指定要仿真的VGA接口类型，常见类型有：
	
		  cirrus：Cirrus Logic GD5446显示卡；
		  std：带有Bochs VBI扩展的标准VGA显示卡；
		  vmware：VMWare SVGA-II兼容的显示适配器；
		  qxl：QXL半虚拟化显示卡；与VGA兼容；在Guest中安装qxl驱动后能以很好的方式工作，在使用spice协议时推荐使用此类型；
		  none：禁用VGA卡；

- -vnc display[,option[,option[,...]]]：默认情况下，qemu使用SDL显示VGA输出；使用-vnc选项，可以让qemu监听在VNC上，并将VGA输出重定向至VNC会话；使用此选项时，必须使用-k选项指定键盘布局类型；其有许多子选项，具体请参照qemu-kvm的手册；

### 网络属性相关选项

 　　网络属性相关选项用于定义网络设备接口类型及其相关的各属性等信息。这里只介绍nic、tap和user三种类型网络接口的属性，其它类型请参照qemu-kvm手册。

- -net nic[,vlan=n][,macaddr=mac][,model=type][,name=name][,addr=addr][,vectors=v]：创建一个新的网卡设备并连接至vlan n中；PC架构上默认的NIC为e1000，macaddr用于为其指定MAC地址，name用于指定一个在监控时显示的网上设备名称；emu可以模拟多个类型的网卡设备，如virtio、i82551、i82557b、i82559er、ne2k_isa、pcnet、rtl8139、e1000、smc91c111、lance及mcf_fec等；不过，不同平台架构上，其支持的类型可能只包含前述列表的一部分，可以使用“qemu-kvm -net nic,model=?”来获取当前平台支持的类型；
- -net tap[,vlan=n][,name=name][,fd=h][,ifname=name][,script=file][,downscript=dfile]：通过物理机的TAP网络接口连接至vlan n中，使用script=file指定的脚本(默认为/etc/qemu-ifup)来配置当前网络接口，并使用downscript=file指定的脚本(默认为/etc/qemu-ifdown)来撤消接口配置；使用script=no和downscript=no可分别用来禁止执行脚本；
- -net user[,option][,option][,...]：在用户模式配置网络栈，其不依赖于管理权限；有效选项有：

		  vlan=n：连接至vlan n，默认n=0；
		  name=name：指定接口的显示名称，常用于监控模式中；
		  net=addr[/mask]：设定GuestOS可见的IP网络，掩码可选，默认为10.0.2.0/8；
		  host=addr：指定GuestOS中看到的物理机的IP地址，默认为指定网络中的第二个，即x.x.x.2；
		  dhcpstart=addr：指定DHCP服务地址池中16个地址的起始IP，默认为第16个至第31个，即x.x.x.16-x.x.x.31；
		  dns=addr：指定GuestOS可见的dns服务器地址；默认为GuestOS网络中的第三个地址，即x.x.x.3；
		  tftp=dir：激活内置的tftp服务器，并使用指定的dir作为tftp服务器的默认根目录；
		  bootfile=file：BOOTP文件名称，用于实现网络引导GuestOS；
		  如：qemu -hda linux.img -boot n -net user,tftp=/tftpserver/pub,bootfile=/pxelinux.0



