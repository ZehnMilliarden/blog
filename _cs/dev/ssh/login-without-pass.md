---
layout: default
title: 免密登录
nav_order: 1
description: ""
guid: 311126ec-78d6-4f05-9d4f-a1edf412acec
parent: SSH
grand_parent: 开发环境
permalink: /cs/dev/ssh/login-without-pass
---

# github 或 gitlab 环境配置
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

### 准备工作
1. 选择合适的ssh版本程序，注意，电脑上可能会有多份,例如 D:\TortoiseGit\bin\TortoiseGitPlink.exe，D:\Git\usr\bin\ssh.exe，C:\Windows\System32\OpenSSH\ssh.exe 等
2. 使用 ssh-keygen -t rsa -C "备注"，生成 rsa秘钥对 （也可以使用 TortoiseGit自带的工具 PuttyGen生成）
3. 生成 例如 xxx.pub和xxx 的秘钥对文件后 将 xxx.pub 文件或文件内容，拷贝到git或远程 sshd服务设备上

### 配置方案一
1. 客户端本地 启动 ssh-agent的服务
&emsp;1.1 如果使用系统自带的 ssh 需要，检查ssh-agent服务是否启动，windows 下 services.msc，查看服务 OpenSSH Authentication Agent （linux上），需要启动该服务
&emsp;1.2如果不使用系统自带，使用git的ssh，则可使用命令 ssh-agent -s 启动服务，（TortoiseGit 未测试）
2. 通过 ssh-add xxx （私钥全路径），将私钥添加到代理管理
3. ssh 配置 文件配置 
Host github （自定义命名，可以用这个名称替换目标域名或ip）
          HostName github.com （主机名称，域名或ip）
          User ZehnMilliarden （用户名）
          IdentityFile ~/.ssh/xxx （对应用户名使用的 私钥）
    *** 注意 host字段值，需要让git ssh链接地址里的host该成这个，否则ssh会失效，找不到私钥，设置 git remote这里

4. 可通过 ssh -T git@github.com 验证秘钥配置是否正确 
      如果发现该步骤失败，1）需要检查是否使用了 同一份ssh程序，和 2）xxx 私钥路径是否正确，和3）config配置是否正确

5. 修改 git 配置，以前可能是使用的https协议下拉的，现在需要修改成 git协议的地址
        例如：
        [remote "origin"]
        url = git@github.com:ZehnMilliarden/Poc.git
        fetch = +refs/heads/*:refs/remotes/origin/*

6. 如果使用  TortoiseGit 工具下拉代码或上传代码出现异常，例如 github No supported authentication methods available (server sent: 人 publickey)，可以检查下 TortoiseGit 配置使用的ssh文件版本


<img src="{{site.cdn.cdn001}}/{{page.guid}}/1.png">

### 配置方案二
1. 通过puttygen将当前的私钥转为ppk格式(如果无法转换需要升级tortoisegit)
2. 将设置 network 选项里 ssh client 设置为tortoisegitplink.exe
3. 在git->remote 每个仓库地址的，puttykey设置为刚刚生成的ppk文件
4. 运行Pageagent添加ppk文件
5. 拉取新仓库时记得设置load putty key选项

