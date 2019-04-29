---
title: 針對連結至 Azure VM 的磁碟進行疑難排解 | Microsoft Docs
description: 提供針對 Azure 虛擬機器虛擬硬碟 (VHD) 的資源進行疑難排解的連結。
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 10/31/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.openlocfilehash: bf1aa75399f28b8dd1732bc9ec09c15e46132939
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098094"
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
