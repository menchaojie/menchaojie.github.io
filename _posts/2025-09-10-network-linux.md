---
layout: post
title: linux系统中网络配置比较
categories:
  - linux
description: 学一些linux知识
keywords:  
  - linux
  - network
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---


| RHEL / CentOS (基于 network-scripts)      | Ubuntu (基于 Netplan 或 ifupdown)                 |
|------------------------------------------|-------------------------------------------------|
| **配置文件目录**                          | **配置文件目录**                                 |
| /etc/sysconfig/network-scripts/ifcfg-eth0 |/etc/netplan/*.yaml|
| **静态 IP 配置示例**                       | **静态 IP 配置示例**                             |
|<pre><code>DEVICE=eth0<br>BOOTPROTO=static<br>ONBOOT=yes<br>IPADDR=192.168.1.100<br>NETMASK=255.255.255.0<br>GATEWAY=192.168.1.1<br>DNS1=8.8.8.8<br>DNS2=8.8.4.4</code></pre>|<pre><code>network: <br>version: 2<br> ethernets:<br> enp0s3:<br> dhcp4: no<br> addresses:<br>    - 192.168.1.100/24<br> gateway4: 192.168.1.1<br> nameservers:<br>   addresses:<br>    - 8.8.8.8<br>    - 8.8.4.4</code></pre> |
| **生效方式**             | **生效方式**                       |
| systemctl restart network (或 nmcli / nmtui) |sudo netplan apply (或 sudo systemctl restart networking 旧版方式) |


注： 实际情况应该根据系统