---
layout: post
categories: 技术
tags: Linux   
---

## ssh-config和sshd_config配置文件区别

        远程管理linux系统基本上都要使用到ssh，原因很简单：telnet、FTP等传输方式是?以明文传送
    用户认证信息，本质上是不安全的，存在被网络窃听的危险。SSH（Secure Shell）目前较可靠，
    是专为远程登录会话和其他网络服务提供安全性的协议。利用SSH协议可以有效防止远程管理过程中
    的信息泄露问题，透过SSH可以对所有传输的数据进行加密，也能够防止DNS欺骗和IP欺骗。    
 
        ssh_config和sshd_config都是ssh服务器的配置文件，二者区别在于，前者是针对客户端的配置
    文件，后者则是针对服务端的配置文件。两个配置文件都允许你通过设置不同的选项来改变客户端程序的
    运行方式。下面列出来的是两个配置文件中最重要的一些关键词，每一行为“关键词&值”的形式，其中“关键
    词”是忽略大小写的。

# [详细参考链接](https://www.cnblogs.com/xiaochina/p/5802008.html "参考链接")
