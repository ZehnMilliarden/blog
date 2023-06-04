---
layout: default
title: Windbg 基础
nav_order: 1
description: ""
permalink: /cs/dbg/windbg
guid: e0017af6-6c28-43f9-a15a-1f9795a4fac7
parent: d51dd9fb-857d-4bef-83f0-ed449e8aeb3d
---

# Windbg 基础
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

## 符号表设置
1. 快捷指令 `ctrl+s`
2. 符号表路径设置：

   ```
   E:\DumpAnalysis\special_symbols;
   srv*E:\DumpAnalysis\some_symbols* \\1.1.1.1\SomeSymbols;
   srv*E:\DumpAnalysis\ms_symbols* \\2.2.2.2\MicrosoftSymbols; 
   srv*\\2.2.2.2\MicrosoftSymbols* https://msdl.microsoft.com/download/symbols
   ```
3. 设置注释，第一行 注释表示从 本地目录中取符号表信息，第二行和第三行 表示从远程地址 `\\1.1.1.1\SomeSymbols` 取符号表信息，并存储在本地目录`E:\DumpAnalysis\some_symbols`中，第四则表示从远程地址`https://msdl.microsoft.com/download/symbols`中取符号表信息，并存储在远程地址`\\2.2.2.2\MicrosoftSymbols`中使用，不同的配置之间使用;号进行分隔


## 符号表加载
1.  这个命令将启用符号表的详细输出。
   ```
   !sym noisy
   ```

2. 这个命令将关闭符号表的详细输出。

   ```
   !sym quiet
   ```

3. 这个命令将重新加载所有的符号表
   ```
   .reload /f
   ```
   
4. 这个命令将重新加载特定模块 module 的符号表
   ```
   .reload /f module
   ```

## 常用的一些指令
1. `!analyze -v` 自动加载符号表分析dmp文件
2. `k`,`kb`,`kvn` 显示当前线程的堆栈
- `k`命令：用于显示当前线程和堆栈跟踪中的函数调用。它可以显示每个函数调用的参数和局部变量。`k`命令是最基本的调试命令。
- `kb`命令：与k命令类似，但它还会显示函数调用的调用者信息。
- `kvn`命令：与k命令类似，但它可以显示函数调用的详细信息，包括完整的符号信息和源代码行号。它还可以显示函数调用的参数和局部变量的值。
3. 如果使用32位的windbg，windbg加载完dump文件后，窗口会显示wow64cpu，表示是64位进程，需要切换到64位环境：
   ```
   .load wow64exts
   !sw
   ```
4. 切换调用帧当前线程的第X帧
   ```
   kvn
   .frame x
   ```
5. `!peb` 格式化输出PEB信息
6. `lm` 查看所有模块, `lmvm xxx` 查看xxx模块的详细信息，xxx模块不要带后缀名
7. `?` 打印所有标准命令，`.help` 打印所有元命令，`.extmatch *` 列出所有扩展命令

## 其他资料
1. 中文在线帮助：<a href="http://www.dbgtech.net/windbghelp/index.html">http://www.dbgtech.net/windbghelp/index.html</a>
2. 下载和安装教程：<a href="https://learn.microsoft.com/zh-CN/windows-hardware/drivers/debugger/">https://learn.microsoft.com/zh-CN/windows-hardware/drivers/debugger/</a>