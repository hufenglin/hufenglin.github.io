##   如何查看跑的是boch 还是hw_replay

1 . 进入跑完的log目录下：

      cd /share/xvs/results/s2017-11-20-164031

2 . 执行以下命令进行查看,如果有如下命令就表示跑的boch

      vim  screen.log
      进行 /remu 有以下命令
      cmd=remu 212345 200 1 1 1 200
      echo remu 212345 200 1 1 1 200

3 . 或者执行如下命令，看 "is_hwreplay=0" 值如果为0，表示boch；如果为1，表示hw_replay

      cat /share/xvs/var/samp_conf



# XVS跑case，设置跑几个case

1 . 进入目录
 
      cd /usr/tet/XVS/cadence/nightly/

2 . 进行编辑，增加或者减少要跑的case,注意case名字不是这样bat_nightly_64_g32e，后面的 “_64_g32e ” 是XVS跑的时候自己加的，在编辑这个文件时不要加后面的东西。

                # gtest
		gtest bat_nightly
		gtest kb_nightly_vnic
		
		# performance
		performance up_linpack_vnic
		performance up_ppv_Dgemm_vnic
		performance up_ppv_Gups_vnic
		performance up_ppv_Linpack_vnic
		performance up_ppv_Sgemm_vnic
		performance up_ppv_Stream_vnic
		performance up_ppv_Pstates_vnic
		performance up_stream_vnic
		
		# performance
		performance Win_Concurrent_Linpack_vnic
		performance Win_mPrime_vnic
		
		
		#control_panel
		control_panel KVM_256M_guest_vnic
		control_panel KVM_Ramdisk_crashme
		control_panel KVM_Ramdisk_du
		control_panel KVM_Ramdisk_idle
		control_panel KVM_Ramdisk_linpack

3 . 修改/share/xvs/var/samp_conf文件，看要跑boch还是hwreplay
   
      is_hwreplay=0 //表示跑boch
      is_hwreplay=1 //表示跑hwreplay

4 . 在当前目录下执行如下命令，就会生成一个新的log，比如 s2017-11-03-183434
  
      xvs run tmp



