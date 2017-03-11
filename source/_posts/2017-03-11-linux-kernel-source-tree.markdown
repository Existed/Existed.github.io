---
layout: post
title: "Linux kernel source tree"
date: 2017-03-11 11:30:02 +0800
comments: true
categories: Linux
keywords: Linux, kernel
styles: [data-table]
---

  内核源码树由很多目录组成，而大多数目录又包含很多子目录。
  源码树根目录极其子目录如下表:

<!-- more -->
***
目录 | 描述
---------: | :----------:
arch/ | 特定体系结构的源码
block/ | 快设备I/O层
crypto/ | 加密API
Documentation/ | 内核源码文档
drivers/ | 设备驱动程序
firmaware/ | 某些驱动程序需要的设备固件
fs/ | VFS和各种文件系统
include/ | 内核头文件
init/ | 内核引导和初始化
ipc/ | 进程间通信代码
kernel/ | 核心子系统，例如调度程序
lib/ | 通用内核函数
mm/ | 内存管理子系统和VM
net/ | 网络子系统
samples/ | 示例，示范代码
scripts/ | 编译内核所用的脚本
security/ | Linux安全模块
sound/ | 语音子系统
usr/ | 早期用户空间代码(所谓的initramfs)
tools/ | 在Linux开发中有用的工具
virt/ | 虚拟化基础基础结构

***
    在源码树根目录由很多文件值得提及：
    - COPYING文件，是内核许可证(GNU GPL v2)；
    - CREDITS，是开发了很多内核代码的开发者列表；
    - MAINTAINERS，维护者列表，他们负责维护内核子系统和驱动程序；
    - Makefile，基本内核的Makefile。


### [参考]
* 《Linux内核设计与实现》（第三版）
