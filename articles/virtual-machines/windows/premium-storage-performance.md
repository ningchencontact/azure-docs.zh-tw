---
title: Azure 進階儲存體：專為 Windows VM 的效能而設計 | Microsoft Docs
description: 使用 Azure 進階儲存體設計高效能應用程式。 「進階儲存體」可針對在「Azure 虛擬機器」上執行且需要大量 I/O 的工作負載，提供高效能、低延遲的磁碟支援。
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 61f84dc871b4d7261dcb57ab430ce6feee9a0c64
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658162"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 有時候，看似磁碟效能問題的情況，其實是網路瓶頸。 在這些情況下，您應該將您的[網路效能](../../virtual-network/virtual-network-optimize-network-bandwidth.md)最佳化。
>
> 如果您想要進行效能評定您的磁碟，請參閱我們的文件上[基準測試磁碟](disks-benchmarks.md)。
>
> 如果您的 VM 支援加速網路，您應確實加以啟用。 如果未啟用，您可以對 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 和 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 上已部署的 VM 加以啟用。

在開始，如果您不熟悉進階儲存體之前，先閱讀[選取為 IaaS Vm 的 Azure 磁碟類型](disks-types.md)並[儲存體帳戶的 Azure 儲存體延展性和效能目標](../../storage/common/storage-scalability-targets.md)。

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
