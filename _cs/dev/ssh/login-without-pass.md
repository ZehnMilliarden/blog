---
layout: default
title: 免密登录
nav_order: 1
description: ""
guid: 311126ec-78d6-4f05-9d4f-a1edf412acec
parent: 418642fe-3a9f-4493-ac04-5ef51732fa1b
grand_parent: 053993f5-2abe-48ce-9037-0c4946e7b3c1
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

### 生成SSH秘钥对
1. 选择合适的SSH版本程序，注意电脑上可能会有多份，例如： 
   - D:\TortoiseGit\bin\TortoiseGitPlink.exe
   - D:\Git\usr\bin\ssh.exe
   - C:\Windows\System32\OpenSSH\ssh.exe 等

2. 使用命令 `ssh-keygen -t rsa -C "备注"` 生成RSA秘钥对，也可以使用TortoiseGit自带的工具PuttyGen生成

3. 生成例如 `xxx.pub` 和 `xxx` 的秘钥对文件后，将`xxx.pub`文件或文件内容拷贝到Git或远程SSHD服务设备上。

### 使用 SSH 认证连接 GitHub

为了更加安全地连接 GitHub，可以使用 SSH 认证方式。下面是该过程的详细步骤：

1. 在客户端本地启动 ssh-agent 服务。如果使用系统自带的 SSH，需要检查 ssh-agent 服务是否启动。在 Windows 上，可以使用 services.msc 查看服务 OpenSSH Authentication Agent（在 Linux 上也是类似）。如果不使用系统自带的 SSH，可以使用 Git 的 SSH，并使用命令 `ssh-agent -s` 启动服务（TortoiseGit 未测试）。在Windows 7系统上可以在命令行终端执行ssh-agent bash然后再执行ssh-add命令将私钥匙添加进代理中。再添加完秘钥后记得配置下.ssh/config 文件，将秘钥和站点进行关联。

2. 使用 `ssh-add xxx` 命令（`xxx` 是私钥的全路径）将私钥添加到代理管理。

3. 配置 SSH 配置文件。在该文件中，可以指定一个自定义的命名（例如 `github`），并设置 `HostName`（即目标域名或 IP）、`User`（即用户名）和 `IdentityFile`（即对应用户使用的私钥）。需要注意的是，`Host` 字段的值需要让 Git SSH 链接地址里的 `host` 替换成这个值，否则 SSH 会失效，找不到私钥。可以在 Git Remote 配置中设置这个值。

4. 使用 `ssh -T git@github.com` 命令验证秘钥配置是否正确。如果验证失败，需要检查是否使用了同一份 SSH 程序，私钥路径是否正确以及配置文件是否正确。如果在系统命令终端中无效，可以在ssh-agent base 中再尝试下。

5. 修改 Git 配置。如果以前使用的是 HTTPS 协议拉取代码，现在需要修改成 Git 协议的地址。例如：

   ```
   [remote "origin"]
   url = git@github.com:ZehnMilliarden/Poc.git
   fetch = +refs/heads/*:refs/remotes/origin/*
   ```

6. 如果使用 TortoiseGit 工具拉取或上传代码出现异常，例如 GitHub 返回错误信息“no supported authentication methods available (server sent: publickey)”，可以检查 TortoiseGit 配置使用的 SSH 文件版本。

<img src="{{site.cdn.cdn001}}/{{page.guid}}/1.png">

以上就是使用 SSH 认证连接 GitHub 的详细步骤。

### 使用 TortoiseGit 连接 SSH

下面是使用 TortoiseGit 连接 SSH 的步骤：

1. 使用 puttygen 将当前的私钥转为 ppk 格式（如果无法转换需要升级 TortoiseGit）。

2. 在 TortoiseGit 设置中，选择 Network 选项，将 SSH client 设置为 tortoisegitplink.exe。

3. 在 Git 的 Remote 选项中，为每个仓库地址设置 puttykey，将其设置为刚刚生成的 ppk 文件。

4. 运行 Pageant，添加 ppk 文件。

5. 拉取新仓库时，记得设置 load putty key 选项。

以上就是使用 TortoiseGit 连接 SSH 的全部步骤。

