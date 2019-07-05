---
title: HB 系列和 HC 系列 Vm-Azure 虛擬機器的已知問題 |Microsoft Docs
description: 深入了解 HB 系列 VM 大小，在 Azure 中的已知問題。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a41155b90257f7eaec85c3adbd975a0a37e24d91
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560410"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB-系列和 HC 系列 VM 的已知問題

使用 HB 系列和 HC 系列 Vm 時，這篇文章會提供最常見的問題和解決方案。

## <a name="dram-on-hb-series"></a>DRAM HB 系列上

HB 系列 Vm 只可以在這個階段會公開 228 GB 的 RAM 以客體 Vm。 這是 hypervisor 的因為 Azure 的已知限制防止網頁要指派給本機 DRAM 的 AMD CCX 的 （NUMA 網域） 保留供客體 VM。

## <a name="accelerated-networking"></a>加速網路

在這個階段中，未啟用 azure 的加速網路，但會隨著在預覽期間進行。 支援此功能時，我們會通知客戶。

## <a name="qp0-access-restriction"></a>qp0 存取限制

若要防止低階的硬體存取，可能會導致安全性弱點，佇列對 0 不是客體 Vm 可存取。 這應該只會影響動作通常相關聯的管理工作的 ConnectX 5 NIC，並執行一些 InfiniBand 診斷像 ibdiagnet，但不是本身使用者應用程式。

## <a name="ud-transport"></a>UD 傳輸

在啟動時，HB 和 HC 系列不支援動態連接傳輸 (DCT)。 經過一段時間，將會實作 DCT 的支援。 支援可靠的連線 (RC) 並不可靠資料包 (UD) 傳輸。

## <a name="gss-proxy"></a>GSS Proxy

GSS Proxy 可以呈現為顯著的效能和回應性更高搭配 NFS 使用的 CentOS/RHEL 7.5 中有一個已知的 bug。 這可減輕使用：

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>清除快取

上的 HPC 系統，通常很有用來清除記憶體下, 一位使用者指派相同的節點之前完成作業之後。 在 Linux 中執行應用程式之後可能會發現您的緩衝區記憶體增加，儘管不執行任何應用程式時，減少可用的記憶體。

![命令提示字元的螢幕擷取畫面](./media/known-issues/cache-cleaning-1.png)

使用`numactl -H`會顯示哪些 NUMAnode(s) 記憶體緩衝處理 （可能是所有）。 在 Linux 中，使用者可以清除的快取在三種方式來傳回經過緩衝處理或快取於 「 免費 」' 的記憶體。 您需要根或具有 sudo 權限。

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![命令提示字元的螢幕擷取畫面](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>核心警告

在下的 HB 系列 VM 開機時，可能會看到下列的核心警告訊息。

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

您可以忽略此警告。 這是 hypervisor 的因為 Azure，將在經過一段時間的方式來解決的已知限制。

## <a name="next-steps"></a>後續步驟

深入了解[高效能運算](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 中。
