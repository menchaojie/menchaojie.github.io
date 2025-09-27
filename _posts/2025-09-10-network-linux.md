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


---
实测部分

### ubuntu 中的注意事项

在Ubuntu Desktop中，默认使用NetworkManager来管理网络，配合nmtui以及桌面系统右上角的网络管理标志进行设置。还有一种是使用systemd-networkd来管理网络，这个和netplan配合的更稳定。当使用netplan进行固定ip设置，并使用NetworkManager来管理网络时，经常出现IP地址丢失的情况，网络环境很不稳定。因此在Ubuntu中可以逐步设置如下：


#### 1. 禁用 NetworkManager

```bash
sudo systemctl stop NetworkManager
sudo systemctl disable NetworkManager
```

#### 2. 启用 systemd-networkd

```bash
sudo systemctl enable systemd-networkd --now
sudo systemctl enable systemd-resolved --now
```

👉 `systemd-resolved` 管 DNS 解析，通常和 `networkd` 一起启用, 如果不启用这一步，有可能无法访问带有域名的网址，如ping baidu.com不通。


#### 3. 配置 netplan

编辑 `/etc/netplan/01-netcfg.yaml`（名字可能不一样，看你的系统里实际的 yaml 文件），改成这样：

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 10.0.0.5/24
      routes:
        - to: default
          via: 10.0.0.2
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
          - 114.114.114.114
```

保存。

---

#### 4. 应用配置

```bash
sudo netplan apply
```

---

#### 5. 验证

```bash
systemctl is-active systemd-networkd
# 应该是 active

ip addr show ens33
# 应该有 inet 10.0.0.5/24

ping -c 3 8.8.8.8
ping -c 3 www.baidu.com
# 测试网关和 DNS
```

---

### ⚠️ 注意事项

1. 如果这是 **Ubuntu Desktop**，禁用 NetworkManager 后，任务栏的网络图标会消失（因为它依赖 NM）。不过有线网络仍然正常。
2. 如果是 **Ubuntu Server**，这样做最合适，稳定、省心。
3. VMware 的虚拟机桥接/NAT 都能正常工作，不受影响。
