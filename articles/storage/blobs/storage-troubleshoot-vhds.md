---
title: 針對連結至 Azure VM 的磁碟進行疑難排解 | Microsoft Docs
description: 提供針對 Azure 虛擬機器虛擬硬碟 (VHD) 的資源進行疑難排解的連結。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: c00fdf3ad02edc1faf0d3257d3836a1c2f44d682
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150774"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>針對連結至 Azure VM 的磁碟進行疑難排解 

對於作業系統磁碟和任何連結的資料磁碟，Azure 虛擬機器 (VM) 需仰賴虛擬硬碟 (VHD)。 VHD 會以分頁 Blob 的形式儲存在一或多個 Azure 儲存體帳戶中。 本文指向 VHD 可能發生之常見問題的疑難排解內容。 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>針對 VM 的儲存體刪除錯誤進行疑難排解

在某些情況下，如果資源管理員部署中的 VM 包含連結的 VHD，則刪除儲存體資源時可能會發生錯誤。 如需解決此問題的說明，請參閱下列其中一篇文章： 

  * 在 Linux VM 上：[Resource Manager 部署中的儲存體刪除錯誤](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * 在 Windows VM 上：[Resource Manager 部署中的儲存體刪除錯誤](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>針對包含連結 VHD 的 VM 非預期重新開機進行疑難排解

如果您遇到包含大量連結 VHD 的 VM 非預期重新開機，請參閱下列其中一篇文章：

  * 在 Linux VM 上：[包含連結 VHD 的 VM 非預期重新開機](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * 在 Windows VM 上：[包含連結 VHD 的 VM 非預期重新開機](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
