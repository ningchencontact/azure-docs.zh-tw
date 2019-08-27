---
title: Azure 進階儲存體：專為 Windows VM 的效能而設計 | Microsoft Docs
description: 使用 Azure 進階儲存體設計高效能應用程式。 「進階儲存體」可針對在「Azure 虛擬機器」上執行且需要大量 I/O 的工作負載，提供高效能、低延遲的磁碟支援。
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7fbeb12f5b221685aad5cac5352318db10bd69a7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036257"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 有時候，看似磁碟效能問題的情況，其實是網路瓶頸。 在這些情況下，您應該將您的[網路效能](../../virtual-network/virtual-network-optimize-network-bandwidth.md)最佳化。
>
> 如果您想要對磁片進行基準測試, 請參閱關於[磁片的](disks-benchmarks.md)效能評定文章。
>
> 如果您的 VM 支援加速網路，您應確實加以啟用。 如果未啟用，您可以對 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 和 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 上已部署的 VM 加以啟用。

開始之前, 如果您不熟悉進階儲存體, 請先閱讀[為 IaaS Vm 選取 Azure 磁片類型](disks-types.md), 並[Azure 儲存體儲存體帳戶的擴充性和效能目標](../../storage/common/storage-scalability-targets.md)。

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

如果您想要對磁片進行基準測試, 請參閱關於[磁片的](disks-benchmarks.md)效能評定文章。

深入了解可用的磁碟類型：[選取磁碟類型](disks-types.md)  

若為 SQL Server 使用者，請參閱「SQL Server 的效能最佳作法」文章：

* [Azure 虛擬機器中的 SQL Server 效能最佳作法](sql/virtual-machines-windows-sql-performance.md)
* [Azure 進階儲存體為 Azure VM 中的 SQL Server 提供最高效能](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)