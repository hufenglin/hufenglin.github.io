### 开机挂载mount etc/fstab与/etc/rc.d/rc.local区别差不多，差别就是如果你又程序依赖于NFS的话还是的放到fstab比较好。

### 放到fstab里面会在程序启动前加载上NFS文件系统，放到rc.local里往往造成程序启动加载时找不到路径

## 一、/etc/fstab 参数详解及如何设置开机自动挂载

#### 某些时候当Linux系统下划分了新的分区后，需要将这些分区设置为开机自动挂载，否则，Linux是无法使用新建的分区的。 /etc/fstab 文件负责配置Linux开机时自动挂载的分区。

#### Windows的文件结构是多个并列的树状结构，最顶部的是不同的磁盘（分区），如：C，D，E，F等。Linux的文件结构是单个的树状结构。最顶部的 为根目录，即/。在根目录下，分为多个子目录，包括/bin、/boot、/dev、/etc、/home、/lib、/media、/mnt、 /opt、/proc、/root、/sbin、/tmp、/usr和/var等。

#### 磁盘Linux分区都必须挂载到目录树中的某个具体的目录上才能进行读写操作，而fstab正是负责这一配置。显然，根目录是所有Linux的文件和目录 所在的地方，需要挂载上一个磁盘分区。上面还提到，Linux分区交换也需要独立使用一个分区，因此，安装一个Linux至少需要两个分区。（事实上，只 使用一个分区安装Linux也是可能的，而且，如果电脑的物理内存足够大，交换分区并不是必须的）

 

## 本文将以典型的centos 系统为例。打开 /etc/fstab 文件

[root@ccd-sdv3 ~]# vim /etc/fstab

默认情况下，fstab中已经有了当前的分区配置，内容可能类似：


	proc              /proc              proc            defaults              0            0
	/dev/hda1         /                  ext3            errors=remount-ro     0            1
	/swapfile         swap               swap            defaults              0            0
	/dev/hdc          /media/cdrom       udf,iso9660     user,noauto           0            0
    /home/CentOS-7-x86_64-DVD-1708.iso /home/CentOS-7-x86_64-DVD-1708 iso9660 defaults,ro,loop 0 0

由上面的内容可以看出，系统的 /dev/hda1 分区被挂载在根目录，文件系统是ext3。此外，还有proc、swap等特殊的“分区”，与 /dev/hdc 被作为光驱挂载在了 /media/cdrom0.

因此，如果希望将新分区 /dev/hda5 挂载在 /home/new 目录下，则只需在fstab文件中加入一行
即可：

	/dev/hda5       /home/new               ext3    default   0       1


 

### 第一列可以是实际分区名，也可以是实际分区的卷标（Lable）。

如果磁盘是SATA接口，且有多个磁盘，则每个磁盘被标记为 /dev/hda 、 /dev/hdb、 /dev/hdc 等以此类推；而每个磁盘的分区被标记为 /dev/hda1、 /dev/hda2等。

如果磁盘是SCSI类型，则多个磁盘会被分别标记为 /dev/sda、/dev/sdb等等。分区同理。

如果使用标签来表示，则格式如：

	LABLE=/

除了显示的使用设备名，你可以使用设备的UUID或设备的卷标签，例如，你可以在这个字段写成“LABAL=root”或 “UUID=3e6be9de-8139-11d1-9106-a43f08d823a6”，这将使系统更具伸缩性。例如，如果你的系统添加或移除了一个SCSI硬盘，这有可以改变你的设备名，但它不会修改你的卷标签。

### 如何查看UUID：

	[root@ccd-sdv3 ~]# ls -l /dev/disk/by-uuid/
	total 0
	lrwxrwxrwx 1 root root 11 Aug  8 09:56 2017-09-06-10-51-00-00 -> ../../loop0
	lrwxrwxrwx 1 root root 15 Aug  8 09:56 54e1942b-39ec-4113-bec5-7204461144f8 -> ../../nvme0n1p3
	lrwxrwxrwx 1 root root 15 Aug  8 09:56 b63f1097-2410-4dc5-8def-7fa526a52982 -> ../../nvme0n1p2
	lrwxrwxrwx 1 root root 15 Aug  8 09:56 D504-20AD -> ../../nvme0n1p1


 

### 第二列是挂载点。

挂载点必须为当前已经存在的目录，为了兼容起见，最好在创建需要挂载的目标目录后，将其权限设置为777，以开放所有权限。

 

### 第三列为此分区的文件系统类型。

Linux可以使用ext2、ext3等类型，此字段须与分区格式化时使用的类型相同。也可以使用 auto 这一特殊的语法，使系统自动侦测目标分区的分区类型。auto通常用于可移动设备的挂载。

 

### 第四列是挂载的选项，用于设置挂载的参数。

常见参数如下：

	auto: 系统自动挂载，fstab默认就是这个选项
	defaults: rw, suid, dev, exec, auto, nouser, and async.
	noauto 开机不自动挂载
	nouser 只有超级用户可以挂载
	ro 按只读权限挂载
	rw 按可读可写权限挂载
	user 任何用户都可以挂载
	请注意光驱和软驱只有在装有介质时才可以进行挂载，因此它是noauto

### 第五列是dump备份设置。

当其值设置为1时，将允许dump备份程序备份；设置为0时，忽略备份操作；

### 第六列是fsck磁盘检查设置。

其值是一个顺序。当其值为0时，永远不检查；而 / 根目录分区永远都为1。其它分区从2开始，数字越小越先检查，如果两个分区的数字相同，则同时检查。

当修改完此文件并保存后，重启服务器生效。

