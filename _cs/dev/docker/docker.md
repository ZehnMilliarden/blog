---
layout: default
title: Docker
nav_order: 1
description: ""
permalink: /cs/dev/docker
has_children: false
parent: 053993f5-2abe-48ce-9037-0c4946e7b3c1
guid: 38be303a-58d4-46a3-bf7f-ebaf06ed838c
---

# DOCKER
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

## Docker 安装
基于debian操作系统

### 使用官方脚本自动安装
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

### 手动安装
1. 卸载旧版
```bash
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```
2. 删除安装包
```bash
$ sudo apt-get purge docker-ce
```
3. 删除镜像、容器、配置文件
```bash
$ sudo rm -rf /var/lib/docker 
```

#### Docker 仓库设置
1. 更新库 `$ sudo apt-get update`
2. 安装 https 相关依赖包，通过https 获取仓库
```bash
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common
```
3. curl添加官方签名，https访问需要
```bash
$ curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | sudo apt-key add -
```
4. 验证签名 （非必须）
```bash
$ sudo apt-key fingerprint 0EBFCD88
```
执行该指令后看是否能找到对应的签名
5. 设置稳定版仓库
```bash
$ sudo add-apt-repository \
   "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian \
  $(lsb_release -cs) \
  stable"
```

#### 安装引擎
1. 安装最新版
```bash
$ sudo add-apt-repository \
   "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian \
  $(lsb_release -cs) \
  stable"
```
2. 安装指定版本
查询有哪些版本
```bash
$ apt-cache madison docker-ce
```
<img src="{{site.cdn.cdn001}}/{{page.guid}}/1.png">

如果你要安装版本 `5:20.10.17~3-0~debian-bullseye`
```bash
$ sudo apt-get install docker-ce=5:20.10.17~3-0~debian-bullseye docker-ce-cli=5:20.10.17~3-0~debian-bullseye containerd.io
```

3. 测试是否安装成功 `$ sudo docker run hello-world`