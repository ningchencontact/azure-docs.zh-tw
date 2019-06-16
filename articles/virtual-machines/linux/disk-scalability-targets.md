---
title: Linux 中虛擬機器磁碟的延展性和效能目標 - Microsoft Azure | Microsoft Docs
description: 針對連結至執行 Linux 之虛擬機器的虛擬機器磁碟，深入了解其延展性和效能目標。
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 11/15/2017
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 39e0a36b50a9e222e6016140967336ccda167514
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771629"
---
# <a name="scalability-and-performance-targets-for-vm-disks-on-linux"></a>Linux 中虛擬機器磁碟的延展性和效能目標

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

如需詳細資訊，請參閱 [Linux 虛擬機器大小](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="managed-virtual-machine-disks"></a>受控虛擬機器磁碟

以星號表示的大小目前為預覽狀態。 請參閱我們的[常見問題集](faq-for-disks.md#new-disk-sizes-managed-and-unmanaged)，以了解在哪些區域中可以取得它們。

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>非受控虛擬機器磁碟
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>請參閱

[Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-subscription-service-limits)