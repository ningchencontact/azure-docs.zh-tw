---
title: 將 Azure VM 移轉至受控磁碟 | Microsoft Docs
description: 將使用儲存體帳戶中非受控磁碟建立的 Azure 虛擬機器移轉成使用受控磁碟。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: f1f786ea4fbf9cea5afbbd2ff038b2b3f8bc3eaf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2019
ms.locfileid: "56803578"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>將 Azure VM 移轉至 Azure 中的受控磁碟

Azure 托管磁盘无需单独管理存储帐户，从而简化了存储管理。  您也可以將現有的 Azure VM 移轉至受控磁碟，以便受惠於可用性設定組中更佳的 VM 可靠性。 它可確保可用性設定組中不同 VM 的磁碟完全彼此隔離，以避免單一失敗點。 它會自動以不同的儲存體縮放單位 (戳記) 將不同 VM 的磁碟放在一個可用性設定組中，以限制硬體和軟體失敗所引起之單一儲存體縮放單位失敗的影響。
根據您的需求，您可從四種類型的儲存體選項中選擇。 若要了解可用的磁碟類型，請參閱[選取磁碟類型](disks-types.md)一文

## <a name="migrate-scenarios"></a>移轉案例

您可以在下列案例中移轉到受控磁碟︰

| **移轉...**                                            | **文件連結**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 將獨立 VM 和可用性設定組中的 VM 轉換成受控磁碟   | [將 VM 轉換成使用受控磁碟](convert-unmanaged-to-managed-disks.md) |
| 將單一 VM 由受控磁碟上的傳統部署移轉到 Resource Manager 部署     | [從傳統 VHD 建立 VM](create-vm-specialized-portal.md)  | 
| 將 vNet 中的所有 VM 由受控磁碟上的傳統部署移轉到 Resource Manager 部署     | [將 IaaS 資源從傳統部署移轉至 Resource Manager 部署](migration-classic-resource-manager-ps.md)，然後[將 VM 從非受控磁碟轉換為受控磁碟](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>後續步驟

- 深入了解[受控磁碟](managed-disks-overview.md)
- 請檢閱[受控磁碟的價格](https://azure.microsoft.com/pricing/details/managed-disks/)。
