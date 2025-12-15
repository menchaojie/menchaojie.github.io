---
layout: post
title: win10+ubuntu24.04双系统
categories:
  - system
description: 学一些系统安装
keywords:  
  - windows
  - ubuntu
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

# win10系统环境下, 安装ubuntu24.04, 双系统

硬件环境，主要是磁盘分配方面, 原来占用情况以及预备安装ubuntu的磁盘分配情况

|序号|硬盘类型|容量|占用情况|
|--|--|--|--|
|1|NVMe SSD|256 G|win10 （C盘, 256G）|
|2|NVMe SSD|512 G|win10  (D盘, 300G), 预备Ubuntu（/boot, swap, /)|
|3|SATA SSD|112 G|空闲，预备Ubuntu（/home)|
|4|机械硬盘 |1 T|win10 (E盘， 1T)|

### 安装中的注意事项

#### 检查 BIOS中 secure boot 

在启动栏(boot)的安全启动(secure boot)菜单下将操作系统类型修改为其他操作系统(other system)

#### 分区的时候，选择手动分区

手动分区可以分 "/boot", "/" 等就可以了 ubuntu24.04中的"/boot/efi" 会在选择安装引导设置事自动创建一个分区。 虽然有些帖子中说，只要 "/" 以及 "/boot/efi"就可以了，但是在我实际安装过程中，没有/boot情况下，一直没有安装成功，这里倒有几个需要注意的点：

1. 要有/boot分区，而且GPT分区情况下选择Btrfs格式，可以分配500M左右的空间
2. /boot/efi使用的是FAT32格式，只能自动创建，而且为1.3G左右的空间，如果觉得空间浪费，可以在自动创建之后，更改其空间大小 

参考：[ubuntu相关论坛](https://forum.ubuntu.com.cn/viewtopic.php?t=494687) 以及 [安装过程]( https://blog.csdn.net/liuqihang11/article/details/147700827)