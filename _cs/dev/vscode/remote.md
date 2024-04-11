---
layout: default
title: 远程开发环境配置
nav_order: 6
description: ""
guid: 5fe9b911-a205-4434-ad1c-9395a4affb39
parent: 527ca8f8-978b-43b7-8735-3a3e252293a3
grand_parent: 053993f5-2abe-48ce-9037-0c4946e7b3c1
permalink: /cs/dev/vscode/remote
---

# REMOTE 环境配置
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

## VSCode Server 安装指南

本指南将带您通过安装 VSCode Server 的过程。

### 获取 Commit ID
首先，我们需要找到 VSCode 客户端的 Commit ID：
1. 在 `Help` -> `About` 页面 查看当前 VSCode 客户端的 Commit ID。
2. 您将看到如下所示的信息：

    ```
    Version: 1.88.0 (user setup)
    Commit: 5c3e652f63e798a5ac2f31ffd0d863669328dc4c
    Date: 2024-04-03T13:26:18.741Z
    Electron: 28.2.8
    ElectronBuildId: 27744544
    Chromium: 120.0.6099.291
    Node.js: 18.18.2
    V8: 12.0.267.19-electron.0
    OS: Windows_NT x64 10.0.19045
    ```

### 下载 VSCode Server
接下来，下载与您的客户端 Commit ID 对应的 VSCode Server 版本：
1. 使用以下链接模板来下载服务端版本，记得替换 `commit_id` 为您之前获取的 Commit ID。
   ```
   https://update.code.visualstudio.com/commit: xxx/server-linux-x64/stable
   ```
   下载对应commit客户端对应的服务端版本 
   注意 链接中 commit: xxx 这里xxx替换为对应的 commit id即可
   例如：[https://update.code.visualstudio.com/commit:5c3e652f63e798a5ac2f31ffd0d863669328dc4c/server-linux-x64/stable](https://update.code.visualstudio.com/commit:5c3e652f63e798a5ac2f31ffd0d863669328dc4c/server-linux-x64/stable)

2. 解压安装包
    完成安装包下载之后，得到包 ```vscode-server-linux-x64.tar.gz``` 通过 ```scp vscode-server-linux-x64.tar.gz user_name@server_ip:~/.vscode-server/bin``` 复制到远程服务器上解压

    ```
    cd ~/.vscode-server/bin
    tar -zxvf vscode-server-linux-x64.tar.gz
    ```
    解压后将在 ~/.vscode-server/bin 目录下生成 vscode-server-linux-x64 目录，将其改名为上文中得到的 vscode 的 commit id，并删除 vscode-server-linux-x64.tar.gz：
    ```
    mv vscode-server-linux-x64 5c3e652f63e798a5ac2f31ffd0d863669328dc4c
    rm vscode-server-linux-x64.tar.gz
    ```
    在这个以 commit id 命名的目录下创建命名为0的文件，此举的目的是为了vscode 客户端链接服务端时触发 vscode服务的安装
    ```
    cd ~/.vscode-server/bin/5c3e652f63e798a5ac2f31ffd0d863669328dc4c
    touch 0
    ```

3. 在vscode的客户端链接此服务器即可