### add a grub option “softlockup_panic=1”, then redo the test.


 Note: 
The principle of Kdump is that when kernel has problems, such as panic, it starts second kernel to save kernel core dump, because the first kernel is already unreliable.The second kernel is kdump kernel. PCM's kernel will have some special actions during the startup, such as reserve memory,I estimate that these operations lead to a failure to use PCM's kernel as a kdump kernel.

Step:
 

 	 1.Install kexec-tools rpm

           # yum install kexec-tools

     2.  configure /etc/sysconfig/kdump

           let the KDUMP_KERNELVER assigned to RHEL/CenOS7 native kernel
           Such as : if my SDV kernel is vmlinuz-3.10.0-327.el7.x86_64, then set KDUMP_KERNELVER="3.10.0- 327.el7.x86_64"

           # Kernel Version string for the -kdump kernel, such as 2.6.13-1544.FC5kdump

           # If no version is specified, then the init script will try to find a

           #  kdump kernel with the same version number as the running kernel.
              KDUMP_KERNELVER=""

     3.   Enable Kdump service

           #  systemctl enable kdump

           #  systemctl restart kdump

     4.  on some system, like my hsw server, there will be the following error at kdump:

            Kernel panic – not syncing: timer doesn't work through interrupt-remapped IO-APIC
            The solution is to add "intremap=off" to the KDUMP_COMMANDLINE_APPEND in /etc/sysconfig/kdump,

            as follows:
            KDUMP_COMMANDLINE_APPEND="intremap=off irqpoll nr_cpus=1 reset_devices cgroup_disable=memory mce=off numa=off udev.children-max=2 panic=10 rootflags=nofail acpi_no_memhotplug transparent_hugepage=never"

     5. reboot


### 查看是否开启

	 systemctl status kdump 

### 存放core_dump文件

	/var/crash