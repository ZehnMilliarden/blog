---
layout: default
title: Rust 环境配置
nav_order: 3
description: ""
guid: d5cd4675-80ca-481f-94f5-587c72ff44df
parent: 527ca8f8-978b-43b7-8735-3a3e252293a3
grand_parent: 053993f5-2abe-48ce-9037-0c4946e7b3c1
permalink: /cs/dev/vscode/rust
---

# RUST 环境配置
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

## Rust 安装 (Windows)
1. 运行 rustup-init.exe 前先执行以下命令，设置环境变量，加速下载
```
  set CARGO_HOME=D:\Rust\.cargo
  set RUSTUP_HOME=D:\Rust\.rustup
  set RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
  set RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup
```
分别设置了安装目录，和下载镜像地址，设置好后，回车安装即可

2. 安装完成后
在 `%CARGO_HOME%` 目录创建文件 config, 该文件没有后缀, 内容填写如下
```
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'ustc'
[source.ustc]
registry = "https://mirrors.ustc.edu.cn/crates.io-index/"
```

3. 环境变量配置
在系统属性环境变量中添加如下信息。<br />
CARGO_HOME=D:\Rust\\.cargo <br />
RUSTUP_HOME=D:\Rust\\.rustup <br />
RUSTUP_DIST_SERVER=http://mirrors.ustc.edu.cn/rust-static <br />
RUSTUP_UPDATE_ROOT=http://mirrors.ustc.edu.cn/rust-static/rustup <br />
PATH 中追加 %CARGO_HOME%\bin <br />

4. 创建工程
```
cargo new greeting
```

## VSCode 配置
1. 安装插件 rust-analyzer 和 Native Debug
2. tasks.json 配置
```
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "cargo",
            "args": [
                "build"
            ]
        }
    ]
}
```

3. launch.json 配置
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(Windows)启动",
            "preLaunchTask": "build",
            "type": "cppvsdbg",
            "request": "launch",
            "program": "${workspaceFolder}/target/debug/${workspaceFolderBasename}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false
        },
        {
            "name": "(gdb)启动",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/target/debug/${workspaceFolderBasename}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "这里填GDB所在的目录",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```