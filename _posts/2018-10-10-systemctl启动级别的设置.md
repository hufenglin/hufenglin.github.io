---
layout: post
categories: 技术
tags: Linux   
---

相比于以往使用service命令，或者直接修改/etc/init.d，或者chkconfig这样多种工具搭配来完成一个系统服务的配置、部署，systemd的野心极大，redhat系已经将systemd作为了其默认的系统服务管理工具。

##  1 . 启动级别配置

### 由于工作需要，常常会在虚拟机中安装各类linux系统，而绝大部分时间，我们需要的只是一个文字界面，能够通过host ssh到VM中，图形界面没必要起着，白白浪费cpu和内存。

###   配置默认进入文字界面：

	systemctl set-default multi-user.target

###  配置默认进入图形界面： 

	systemctl set-default graphical.target

### 由text模式进入桌面系统

	systemctl isolate graphical.target

## 2 . 开机启动服务

	systemctl enable ***.service

## 3 . 开机不启动服务

	systemctl disable ***.service

## 4 . 启动/停止/重启服务

	systemctl start/stop/restart ***.service

## 5 . 查询服务状态

	systemctl status ***.service

## 6 . 直接查看某项服务的配置文件,以sshd.serice为例

	systemctl cat sshd.service