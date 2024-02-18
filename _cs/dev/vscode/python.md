---
layout: default
title: PY 环境配置
nav_order: 5
description: ""
guid: 4c61d390-adbb-40ec-910a-669b864f3e28
parent: 527ca8f8-978b-43b7-8735-3a3e252293a3
grand_parent: 053993f5-2abe-48ce-9037-0c4946e7b3c1
permalink: /cs/dev/vscode/python
---

# PY 环境配置
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

### .vscode/.env 配置
```
PYTHONPATH="./;../../folder1;../folder2"
```

### .vscode/launch.json
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Current File",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "justMyCode": false,
            "envFile": "${workspaceFolder}/.vscode/.env" # 配置python环境变量文件位置
        }
    ]
}
```

### .vscode/settings.json
```
{
    "python.languageServer": "Pylance",
    "editor.formatOnSave": true,
    "python.autoComplete.addBrackets": true,
    "python.autoComplete.extraPaths": [
        "./src", # 代码自动补全位置限制代码
        "folder1",
        "folder2"
    ],
    "python.analysis.extraPaths": [
        "./src", # 代码分析限制位置
        "folder1",
        "folder2"
    ],
    "python.jediEnable": false,
    "python.envFile": "${workspaceFolder}/.vscode/.env", # 同上，python环境变量文件位置
    "python.analysis.completeFunctionParens": true,
    "terminal.integrated.env.windows": {
        "PYTHONPATH": "${workspaceRoot}" # 配置控制台窗口的环境变量
    }
}
```