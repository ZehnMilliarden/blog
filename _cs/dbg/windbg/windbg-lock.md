---
layout: default
title: Windbg 死锁调试
nav_order: 2
description: ""
permalink: /cs/dbg/windbg-lock
guid: c6117144-54ed-4f26-9b37-73f1b35c96b8
parent: d51dd9fb-857d-4bef-83f0-ed449e8aeb3d
---

# Windbg 死锁调试

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

## 查看当前的锁
- `!locks` 查看所有的锁
    ```
    0:000> !locks
    CritSec ntdll!LdrpLoaderLock+0 at 00007ff9d241f4f8
    WaiterWoken        No
    LockCount          1        #从-1开始，-1表示没有线程调用entercs，
                                #0表示有一个线程。大于0就是有多线程等待了
    RecursionCount     1        #从0开始，表示拥有这个cs的线程调用了多少次entercs
    OwningThread       7ec      #目前占用改资源的线程
    EntryCount         0        #从-1开始，第一次调用entercs的线程会导致该值+1，
                                #后续不是owner thread 调用entercs，会导致该值继续+1
    ContentionCount    1        #争用次数，其他线程争用阻塞一次就会+1
    *** Locked                  #当前锁状态

    Scanned 26 critical sections
    ```

- `!cs -l` 列出所有的临界区
    ```
	0:006> !cs -l
	DebugInfo          = 0x00007ff9d241f978
    Critical section   = 0x00007ff9d241f4f8 (ntdll!LdrpLoaderLock+0x0)
    LOCKED
    LockCount          = 0x1
    WaiterWoken        = No
    OwningThread       = 0x00000000000007ec
    RecursionCount     = 0x1
    LockSemaphore      = 0xFFFFFFFF
    SpinCount          = 0x0000000004000000

    ```

- `!cs -s 0x00007ff9d241f978` 列出特定临界区的信息
- `dt ntdll!_RTL_CRITICAL_SECTION 0x00007ff9d241f978` 查看指定临界区的信息
- `!handle` 查看所有句柄
- `!handle 0x208 f` 查看句柄详细信息
    ```
    0:000> !handle 0x208 f
    Handle 0000000000000208
    Type         	Semaphore
    Attributes   	0
    GrantedAccess	0x1f0003:
         Delete,ReadControl,WriteDac,WriteOwner,Synch
         QueryState,ModifyState
    HandleCount  	2
    PointerCount 	65535
    Name         	\Sessions\1\BaseNamedObjects\SM0:1720:304:WilStaging_02_p0
    No object specific information available
    ```