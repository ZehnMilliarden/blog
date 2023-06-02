---
layout: default
title: 网卡配置
nav_order: 1
description: ""
guid: 5348a8d3-229c-4e18-8f20-542705176787
parent: 852dac9b-15b3-4ce2-b368-415554849069
grand_parent: c27b1703-af14-46c4-8839-d16a5c0be153
permalink: /cs/sys/centos/net-interface-cfg
---

# 网卡配置
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 查看当前网络适配器配置

### ip addr 命令

执行该命令可以查看当前系统中的网络适配器配置情况，例如：

<img src="{{site.cdn.cdn001}}/{{page.guid}}/1.png">

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
   inet 127.0.0.1/8 scope host lo
      valid_lft forever preferred_lft forever
   inet6 ::1/128 scope host 
      valid_lft forever preferred_lft forever
2: ens32: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
   inet 192.168.31.128/24 brd 192.168.31.255 scope global ens32
      valid_lft forever preferred_lft forever
   inet6 fe80::20c:29ff:fe90:a347/64 scope link 
      valid_lft forever preferred_lft forever
```

其中，ens32 是网卡名，inet 后面的 IP 地址是该网卡当前配置的 IP 地址。

### 网卡说明

- eno1：代表由主板 BIOS 内置的网卡
- ens1：代表有主板 BIOS 内置的 PCI-E 网卡
- enp2s0：PCI-E 独立网卡
- eth0：如果以上都不使用，则回到默认的网卡名

其中，en 表示 Ethernet，o 表示主板板载网卡，集成是的设备索引号，p 表示独立网卡，PCI 网卡，s 表示热插拔网卡，USB 之类的扩展槽索引号，nnn（数字）表示 MAC 地址+主板信息计算得出唯一序列。

lo 网卡是网络回环地址，不用管。ens32 网卡属于第二种网卡，主板 BIOS 内置的 PCI-E 网卡。

## 基础知识

- /etc/host.conf：配置域名服务客户端的控制文件。
- /etc/hosts：完成主机名映射为 IP 地址的功能。
- /etc/resolv.conf：域名服务客户端的配置文件，用于指定域名服务器的位置。
- /etc/sysconfig/network：包含了主机最基本的网络信息，用于系统启动。
- /etc/sysconfig/network-scripts/：系统启动时初始化网络的一些信息以及网卡的配置文件。
- /etc/xinetd.conf：定义了由超级进程 xinetd 启动的网络服务。
- /etc/networks：完成域名与网络地址的映射。
- /etc/protocols：设定了主机使用的协议以及各个协议的协议号。
- /etc/services：设定主机的不同端口的网络服务。

## 网卡配置解释

```conf
DEVICE="ens32" 
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
UUID="c36c226a-ddbc-4000-aa10-adc80b801016"
HWADDR="00:0c:99:88:aa:44"
BOOTPROTO="static"
DEFROUTE="yes"
PEERDNS="yes"
PEERROUTES="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_PEERDNS="yes"
IPV6_PEERROUTES="yes"
IPV6_FAILURE_FATAL="no"
NAME="ens32"
ONBOOT="yes"
NETMASK=255.255.255.0
USERCTL=yes/no
```

- DEVICE="ens32"：指定网卡名为 ens32。
- TYPE="Ethernet"：指定网卡类型为 Ethernet。
- PROXY_METHOD="none"：不使用代理。
- BROWSER_ONLY="no"：不仅在浏览器中使用该网卡。
- UUID="c36c226a-ddbc-4000-aa10-adc80b801016"：唯一 ID。
- HWADDR="00:0c:99:88:aa:44"：MAC 地址。
- BOOTPROTO="static"：配置为静态 IP，也可以使用 DHCP 动态获取 IP 地址。
- DEFROUTE="yes"：是否从 DHCP 服务器获取用于定义接口的默认网关的信息的路由表条目。
- PEERDNS="yes"：是否允许 DHCP 获得的 DNS 覆盖本地的 DNS。
- PEERROUTES="yes"：是否从 DHCP 服务器获取用于定义接口的默认网关的信息的路由表条目。
- IPV4_FAILURE_FATAL="no"：如果 IPV4 配置失败是否禁用设备。
- IPV6INIT="yes"：是否禁止 IPV6。
- IPV6_AUTOCONF="yes"：是否允许 IPV6 自动配置。
- IPV6_DEFROUTE="yes"：是否从 DHCP 服务器获取用于定义接口的默认网关的信息的路由表条目。
- IPV6_PEERDNS="yes"：同 IPV4 配置。
- IPV6_PEERROUTES="yes"：同 IPV4 配置。
- IPV6_FAILURE_FATAL="no"：同 IPV4 配置。
- NAME="ens32"：网卡名。
- ONBOOT="yes"：是否开机加载。
- NETMASK=255.255.255.0：子网掩码。
- USERCTL=yes/no：是否允许非 root 用户控制该设备。

以下配置 BOOTPROTO="static"，当 IP 配置为静态 IP 时生效。

```conf
IPADDR=192.168.31.128
GATEWAY=192.168.31.2
DNS1=192.168.31.2
```

- IPADDR=192.168.31.128：IP 地址。
- GATEWAY=192.168.31.2：网关。
- DNS1=192.168.31.2：DNS 服务。

## 参考配置

```conf
DEVICE="ens32"
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
UUID="c36c226a-ddbc-4000-aa10-adc80b801016"
HWADDR="00:0c:99:88:aa:44"
BOOTPROTO="static"
DEFROUTE="yes"
PEERDNS="yes"
PEERROUTES="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_PEERDNS="yes"
IPV6_PEERROUTES="yes"
IPV6_FAILURE_FATAL="no"
NAME="ens32"
ONBOOT="yes"
IPADDR=192.168.31.128
NETMASK=255.255.255.0
GATEWAY=192.168.31.2
DNS1=192.168.31.2
```