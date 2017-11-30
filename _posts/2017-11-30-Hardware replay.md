## 什么是Hardware replay 

   Hardware replay 意味着使用PCM平台上的另一个虚拟机来replay sampling snapshot。 因为它也是一个基于KVM的虚拟机，所以叫hardware replay。

## 为什么要hardware replay

  当前的PCM replay由BOCHS RCP处理，花费很多时间来确定BOCHS或PCM是否负责，由基于KVM的另一个VM重播可以将问题限制到PCM代码。



## How to use hwreplay:

### Offline replay:

####  1 . Create replay VM by：
      
      qemu-system-x86_64 -nodefaults -no-user-config -enable-kvm -cpu Skymeadow -M q35 -m 1024 -smp 1 -monitor stdio -pcm-replay-mode
      
      -pcm-replay-mode:表明这是一个replay虚拟机

#### 2 . 在文件夹中放置offline snapshot。 与Bochs一样的位置。

      将offline snapshot 放到 /rcp-replay-log目录下

#### 3 . In qemu monitor run command:

      pcm-offline-replay <num>
      <num>: 是offline snapshot log日志的前缀
      例如：pcm-offline-replay 123   它将以123 *名称前缀replay the snapshot 。

### Online replay:

#### 1 . Create OSS VM

#### 2 . Create replay VM by:

      qemu-system-x86_64 -nodefaults -no-user-config -enable-kvm -cpu Skymeadow -M q35 -m 1024 -smp 1 -monitor stdio -pcm-replay-mode

#### 3 . in OSS VM qemu monitor, start auto test timer:

      pcm-auto-test-timer 1234 10 1 100

#### 4 . in replay VM qemu monitor, run command:

      pcm-online-replay 1234 10
      1234: the shm key
      10000: 10000 cycles which is equal to the sample cycles.

#### 5 . 如果在replay过程中发生cpu或内存不匹配，失败的示例将被保存在位于启动replay虚拟机的文件夹 ./rcp-replay-log中。







      




  