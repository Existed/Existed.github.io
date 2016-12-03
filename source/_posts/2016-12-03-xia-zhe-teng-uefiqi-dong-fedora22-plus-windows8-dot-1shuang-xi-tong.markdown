---
layout: post
title: "[瞎折腾] UEFI启动Fedora22+Windows8.1双系统"
date: 2016-12-03 18:35:39 +0800
comments: true
categories: Linux
tags: [Linux, Windows, Windows8, 双系统, Fedora, UEFI]
keywords: Linux, Windows, Windows8, 双系统, Fedora, UEFI
description: 安装Fedora和Windows双系统，并通过UEFI启动。
---

本人比较懒，所以直接贴出参考文章，思路差不多，但是最后使用Fedora来引导，而不是参考文章中所说的rEFInd。怎样使用Fedora引导写在最后。

<!-- more -->

## [参考]

UEFI+GPT安装Windows8和CentOS双系统

[日期：2014-05-11] 来源：Linux社区 作者：smstong

随着虚拟机管理软件的广泛使用，双系统安装场合越来越少了，但还存在这样的地方，尤其是学习一些硬件的场合。本文针对UEFI时代的双系统安装进行了实验和说明。

前一篇文章已经对UEFI,GPT和安装CentOS进行了详细说明，见： http://www.linuxidc.com/Linux/2014-05/101574.htm 。本文专门说明双系统的安装方式。

U盘安装64位CentOS 6.5方法 http://www.linuxidc.com/Linux/2014-04/100035.htm

Win7安装64位CentOS 6.4双系统详细图文教程 http://www.linuxidc.com/Linux/2014-04/100032.htm

CentOS与Windows双系统安装注意事项记录 http://www.linuxidc.com/Linux/2014-05/101571.htm

#### 1 安装步骤

（1）使用diskgen工具把硬盘修改为GPT格式。（可以通过带有这个工具的光盘、U盘等完成）。

（2）光盘安装Window8，在建立分区时，安装程序自动建立额外的两个分区MSR和ESP。

（3）一步步点击完成。

此时，ESP分区有/EFI/Microsoft/Boot/文件夹，里面是Windows8的启动器，bootmgr.efi，bootmgfw.efi等。

（4）放入CentOS-EFI的光盘，重新启动机器

（5）安装程序能够识别ESP分区，我们需要做的只是把这个ESP分区挂载到/boot/efi。（注:ESP分区类型的GUID是固定的，据此识别之）

（6）在安装GRUB时，安装程序自动设置为安装到ESP分区，我们看清即可

（7）正常安装完成。

此时，ESP分区增加了/EFI/RedHat/文件夹，里面是grub.efi和grub.conf。

#### 2 双系统启动的方式

目前为止，只是安装了两个系统，UEFI的nvram启动菜单有了这两个菜单项，但是这款主板启动时，只显示一个微软的选项。这就导致了只能启动Windows8，不能启动CentOS。解决思路有如下几种

* 使用windows8的bootmgr.efi来链式启动grub.efi。在BIOS时代，ntldr是可以加载grub所在扇区的，现在我还没找到可行方式;（不可行）   
* 把grub.efi修改路径及名称为bootmgfw.efi，然后通过grub.efi链式启动bootmgfw.efi。这在BIOS时代是相当常见的，但现在我实验证明Centos所带的grub.efi不能链式加载bootmgfw.efi，提示错误。（不可行）   
* 升级主板固件，使得能按下F12时，同时显示CentOS和Windows两个启动选项。还没找到可行的途径。（不可行）   
* 通过第三方启动管理器来代替bootmgfw.efi的位置，让后由这个管理器提供链式加载bootmgfw.efi和grub.efi。我选用的是rEFInd，实验证明完全可行。（可行！）

目前位置我还没找到可以修该这款主板UEFI的启动菜单的可行方式，所以采用把refind.efi改名为微软的bootmgfw.efi的方式。而微软的bootmgfw.efi则修改到其他位置。具体步骤：

（1）通过CentOS光盘recue模式，把/EFI/Microsft/Boot文件夹改名为 /EFI/weiruan；  
（2）把refind文件夹拷贝到/EFI/Microsoft/，并改名为Boot；  
（3）把refind_x64.efi改名为bootmgfw.efi；修改refind.conf.smaple文件名为refind.conf，并根据需要修改其中的内容，我修改后的内容如下：

>timeout 20    
showtools shell, mok_tool, about, reboot, exit, firmware   
scanfor internal,external,optical,manual   
dont_scan_files shim.efi,MokManager.efi,memtest.efi,bootmgr.efi    
scan_all_linux_kernels   
default_selection weiruan

至此，文件夹布局为：
>EFI/weiruan （里面是Windows8的启动器）   
EFI/Microsoft/Boot（里面是refind的文件）   
EFI/redhat （里面是grub启动器）   

完成后，重新启动机器，喜人的rEFInd界面出现，上图显摆一下：   
>UEFI+GPT安装Windows8和CentOS双系统

#### 3 UEFI时代的双启动思考

微软依旧很霸道，所以多系统安装时，Windows要首先安装，省的它在安装是破坏别的已安装系统；
这个时代的启动加载器尚未完全成熟稳定，grub就是个例子，它的链式加载有问题存在；
UEFI主板功能还不完善，设置修改不方便，不好用；
理论上多启动更加简单；
目前作为启动管理器的rEFInd确实非常好用。
更多CentOS相关信息见CentOS 专题页面 http://www.linuxidc.com/topicnews.aspx?tid=14

本文永久更新链接地址：http://www.linuxidc.com/Linux/2014-05/101575.htm

## [默认由Fedora引导]

根据上面的思路，将EFI分区的Microsoft命名为weiruan，并创建Microsoft文件夹，拷贝fedora到Microsoft目录，并重命名为Boot，修改Microsoft/Boot/grubx64.efi为bootmgfw.efi，修改同目录下的grub.cfg，将Microsoft改为weiruan。关闭UEFI的secure boot模式，OK啦～

有一点不爽的是win8启动后，桌面右下角会显示“SecureBoot未正确配置” :-(

* Fedora安装介质是用[liveusb-creator](https://fedorahosted.org/liveusb-creator/)制作的,支持UEFI启动。

## [问题]

这种方式有几个问：      
1. 当有新版本的内核被安装时，新内核的启动信息会自动配置在中/boot/efi/EFI/fedora/grub.cfg，需要手动更新/boot/efi/EFI/Microsoft/Boot/grub.cfg；        
2. grub配置文件中建议不要手动修改grub.cfg，推荐通过修改/etc/grub.d/中的文件，执行grub-mkconfig来达到配置启动项的目的，显然上面的方法没遵循这条建议；    
3. 针对上面的问题，可能可以用“grub-mkconfig -o /boot/efi/EFI/Microsoft/Boot/grub.cfg”，没有亲自尝试过，如果可行，不过没次内核更新后，还需手动执行一次该命令；       
4. windows8在启动过程中，“Windows”标识可能不能正常显示，不过不影响使用。
