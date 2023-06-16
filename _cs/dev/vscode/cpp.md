---
layout: default
title: CPP 环境配置
nav_order: 1
description: ""
guid: 640fefba-e594-46c2-8caa-3070919106d3
parent: 527ca8f8-978b-43b7-8735-3a3e252293a3
grand_parent: 053993f5-2abe-48ce-9037-0c4946e7b3c1
permalink: /cs/dev/vscode/cpp
---

# CPP 环境配置
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

## gcc 安装
    sudo apt install aptitude -y
    sudo aptitude build-essential 
    # 先拒绝接受第一种解决方案 选择 n ，再接受第二种解决方案 选择y ，再同意下载安装这些依赖 选择y

## cmake 和git 安装 
    sudo apt install cmake git -y
    ubuntu 18.04以上是默认安装了python3的

## 配置vscode基础开发环境
进入目录 执行 `code .` vscode会以当前目录为项目目录打开vs,并且创建了.vscode文件夹，在该文件夹中有重要的三个文件分别是：
- launch.json 会告诉你怎么加载运行，配置调试环境 F5
- tasks.json 告诉你怎么样配置构建任务 ctrl+shift+b
- c_cpp_properties.json


## vscode + cmake + Shell 配合开发


### VSCode
<table>
  <thead>
    <tr>
      <th style="text-align: left;">方法 / 字段</th>
      <th style="text-align: left;">含义</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>${workspaceFolder}</td>
      <td>当前 Workspace 文件夹路径</td>
    </tr>
    <tr>
      <td>${workspaceRootFolderName}</td>
      <td>Workspace 文件夹命令</td>
    </tr>
    <tr>
      <td>${file}</td>
      <td>自身文件绝对路径</td>
    </tr>
    <tr>
      <td>${relativeFile}</td>
      <td>文件在 Workspace 中的路径</td>
    </tr>
    <tr>
      <td>${fileBasenameNoExtension}</td>
      <td>当前文件的文件名，无后缀</td>
    </tr>
    <tr>
      <td>${fileExtname}</td>
      <td>当前文件的后缀</td>
    </tr>
    <tr>
      <td>${fileBasename}</td>
      <td>当前文件的文件名 `${fileBasenameNoExtension}.${fileExtname}`</td>
    </tr>
    <tr>
      <td>${fileDirname}</td>
      <td>当前文件所在文件夹路径</td>
    </tr>
    <tr>
      <td>${lineNumber}</td>
      <td>当前文件光标所在行号</td>
    </tr>
  </tbody>
</table>

### VSCode 环境配置

1. launch.json
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Build&Debug",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/bin/main",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}/bin",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "debug",
            "miDebuggerPath": "/usr/bin/gdb"
        },
        {
            "name": "RBuild&Debug",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/bin/main",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}/bin",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "rdebug",
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```

2. tasks.json
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "debug",
            "type": "shell",
            "args": [
                "debug",
                "false"
            ],
            "command": "scripts/build.sh",
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "problemMatcher": [],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "label": "release",
            "type": "shell",
            "args": [
                "release",
                "false"
            ],
            "command": "scripts/build.sh",
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "problemMatcher": [],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "label": "rdebug",
            "type": "shell",
            "args": [
                "debug",
                "true"
            ],
            "command": "scripts/build.sh",
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "problemMatcher": [],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "label": "rrelease",
            "type": "shell",
            "args": [
                "release",
                "true"
            ],
            "command": "scripts/build.sh",
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "problemMatcher": [],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```

3. setting.json
```json
{
    "C_Cpp.default.configurationProvider": "ms-vscode.cmake-tools"
}
```

### 构建脚本 build.sh
```sh
#!/bin/sh
BUILD_type=$1
ORIGIN_PATH=${pwd}
PROJECT_ROOT=$(cd `dirname $0`; cd ..; pwd)
BUILD_DIR=${PROJECT_ROOT}/build
INSTALL_DIR=${PROJECT_ROOT}/bin

function build_failed() {
    echo $1 failed
    cd ${ORIGIN_PATH} || failed
    exit 1
}

function build_root_cmake() {
    if [[ ! -d ${BUILD_DIR} ]]
    then
        mkdir ${BUILD_DIR}
    fi
    cd ${BUILD_DIR} || echo "cd  error"
    cmake -DCMAKE_BUILD_TYPE=${BUILD_DIR} -DCMAKE_INSTALL_PREFIX=${INSTALL_DIR} .. || build_failed "generate cmake file"
    make || build_failed "make"
    make install || build_failed "make install"
}

build_root_cmake
```

### CMakeLists 编写

```
# Demo 1
cmake_minimum_required(VERSION 3.0)

project(poj)

set(CMAKE_C_COMPILER "gcc")

add_subdirectory(src)

# Demo 2

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -W -Wall -Wextra")

file(GLOB TEST_SRC
    *.h *.c
    core/*.h core/*.c)

add_executable(main ${TEST_SRC})

install(TARGETS main DESTINATION ${CMAKE_INSTALL_PREFIX})
```