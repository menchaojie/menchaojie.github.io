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



### **RHEL/CentOS与Ubuntu网络配置对比指南**

#### 配置体系架构对比

| 特性                | RHEL/CentOS (network-scripts)       | Ubuntu (Netplan)                  |
|---------------------|-------------------------------------|-----------------------------------|
| **配置目录**        | /etc/sysconfig/network-scripts/     | /etc/netplan/                     |
| **配置文件命名**    | ifcfg-ethX                          | *.yaml (如00-installer-config.yaml)|
| **配置语法**        | INI键值对格式                       | YAML层级结构                      |
| **默认工具链**      | NetworkManager + network.service    | systemd-networkd + netplan        |

#### 静态IP配置示例

**RHEL/CentOS配置**

```
# /etc/sysconfig/network-scripts/ifcfg-eth0 主要内容：
DEVICE=eth0
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.1.100
PREFIX=24
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4
```

**Ubuntu配置**

```
# /etc/netplan/00-installer-config.yaml 主要内容：
network:
  version: 2
  ethernets:
    enp0s3:
      addresses: [192.168.1.100/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

#### 配置生效方式

| 操作类型          | RHEL/CentOS                     | Ubuntu                          |
|-------------------|---------------------------------|---------------------------------|
| 测试配置          | nmcli connection reload         | sudo netplan try                |
| 应用配置          | systemctl restart network       | sudo netplan apply              |
| 查看当前配置      | nmcli connection show eth0      | networkctl status enp0s3        |
| 故障排查          | journalctl -xe                  | journalctl -u systemd-networkd  |

#### 关键差异说明

1. **CIDR表示法**：Ubuntu强制使用CIDR格式(如/24)，而RHEL支持传统子网掩码
2. **接口命名**：Ubuntu默认采用可预测网络接口名(enpXsY)
3. **DNS配置**：Ubuntu需在netplan中声明，RHEL可直接写入ifcfg文件
4. **多网卡管理**：Ubuntu通过单个YAML文件管理，RHEL需维护多个ifcfg文件


注： 实际情况应该根据系统