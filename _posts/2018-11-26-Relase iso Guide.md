##  Prepare
1.  Download and decompress an Ubuntu ISO image.

2.  Enter the casper file, there should be an initrd.lz, an vmlinuz.efi, and filesystem.squashfs. They are the files we need to modify.

##  Update squashfs

1.  Decompress filesystem.squashfs with `unsquashfs filesystem.squashfs`

2.  `mount --bind /run ./squashfs-root/run`

3.  `mount --bind /dev ./squashfs-root/dev`

4.  `mount --bind /proc ./squashfs-root/proc`

5.  `mount --bind /sys ./squashfs-root/sys`

6.  `chroot ./squashfs-root`

7.  `cp sources.list /etc/apt/`

8.  `apt-get update`

9.  vim /etc/environment  add：


		export GIT_PROXY_COMMAND=/usr/local/bin/git-proxy.sh
		export no_proxy="localhost,127.0.0.1,.intel.com"
		export http_proxy="http://child-prc.intel.com:913/"
		export https_proxy="http://child-prc.intel.com:913/"
		export ftp_proxy="http://child-prc.intel.com:913/"

10.  `scp root@10.239.156.51:/git-proxy.sh /usr/local/bin/`

11.  `source /etc/environment`

12.  `scp root@10.239.156.51:/usr/local/bin/connect.c  /usr/local/bin/connect.c`

13.  `gcc connect.c -o /usr/bin/connect`

14.  Install packages here.

	    apt-get install libepoxy-dev libdrm-dev libgbm-dev spice-client-gtk libegl1-mesa-dev libgtk2.0-dev bin86 bcc iasl uuid-dev libncurses-dev libyajl-dev libc6-dev-i386

15.  Install kernel here. While compiling the kernel, make sure to turn on` CONFIG_OVERLAY_FS` and other modules kernel might require according to the wiki. A good way is to generate the new config with previous config with `make oldconfig`.

16. Install KVM and Xen here. You might want to put igvtg-qemu and igvtg-xen directory right under `/root`, otherwise some path problems would occur later.

17. Delete `gvt-linux`, `igvtg-xen` and `igvtg-qemu` directories.

18. Delete old kernels and bash history, then exit and umount everything.

19. Modify files in `squashfs-root/etc/grub.d` to meet grub needs.

20. Copy necessary scripts into `usr/share/script`, and Launch_Guest.desktop into `usr/share/applications`.

21. Copy out `lib/modules/$YOUR_MODULES`, `usr/lib/modules/efi64/mboot.c32`, `vmlinuz` and `xen.gz` to modify initrd later.

22.  `cp sources.list /etc/apt/`

23.  `apt-get update`

24.  vim /etc/environment   remove：


		export GIT_PROXY_COMMAND=/usr/local/bin/git-proxy.sh      
		export no_proxy="localhost,127.0.0.1,.intel.com"      
		export http_proxy="http://child-prc.intel.com:913/"      
		export https_proxy="http://child-prc.intel.com:913/"      
		export ftp_proxy="http://child-prc.intel.com:913/"      
		

25.  remove  `/usr/local/bin/git-proxy.sh`

26.  remove  `/usr/bin/connect` 、 `/usr/local/bin/connect.c`


27. Re-package filesystem.squashfs with `mksquashfs squashfs-root filesystem.squashfs`. Replace the old squashfs with the new one.

##  Update initrd

1.  Copy initrd.lz to a directory. It is actually a LZMA format file. Use `mv initrd.lz initrd.lzma`, `lzma -d initrd.lzma` and `cpio -idmv < initrd` to decompress it. Remove the original initrd file.

2.  Remove `lib/modules/*`, and then copy `$YOUR_MODULES` there.

3.  In `scripts/casper-bottom/25adduser`, around line 81, add the files you want to copy to the desktop.

4.  Repackage initrd.lz


	      find ./* | cpio -H newc -o > ../initrd
          lzma initrd 
	      mv initrd.lzma initrd.lz


##  Update files in /casper

1.  Replace vmlinuz, filesystem.squashfs, initrd.lz with the new ones.

2.  Add xen.gz and mboot.c32 here.

## Modify ISO GRUB

1.  Under ISO directory, modify ./boot/grub.cfg and ./isolinux/txt.cfg

## Repackage ISO

1.  Find my.bin to support booting from ISO.
 
2.  `xorriso -as mkisofs -isohybrid-mbr ../my.bin -D -r -V "Intel GVT-g" -J -l -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -eltorito-alt-boot -e boot/grub/efi.img -no-emul-boot -isohybrid-gpt-basdat -o ../ISO_NAME.iso .`


