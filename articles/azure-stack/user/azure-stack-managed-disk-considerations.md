---
title: Azure Stack 中受控磁碟的差異與考量 | Microsoft Docs
description: 使用 Azure Stack 中的受控磁碟時，瞭解有關差異和注意事項。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 4bd36744cc417e85f49e58f9a08d2b9006da9fe4
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284024"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack 受控磁碟：差異與注意事項
本文摘要說明 Azure Stack 受控磁碟和適用於 Azure 的受控磁碟之間的已知差異。 若要深入了解 Azure Stack 與 Azure 之間的大致差異，請參閱[主要考量](azure-stack-considerations.md)文章。

受控磁碟會管理與 VM 磁碟相關的[儲存體帳戶](/azure/azure-stack/azure-stack-manage-storage-accounts)，從而簡化 IaaS VM 的磁碟管理。
  

## <a name="cheat-sheet-managed-disk-differences"></a>速查表：受控磁碟的差異

| 功能 | Azure (全域) | Azure Stack |
| --- | --- | --- |
|待用資料的加密 |Azure 儲存體服務加密 (SSE)、Azure 磁碟加密 (ADE)     |BitLocker 128 位元 AES 加密      |
|映像          | 支援受控自訂映像 |尚不支援|
|備份選項 |支援 Azure 備份服務 |尚不支援 |
|災害復原選項 |支援 Azure Site Recovery |尚不支援|
|磁碟類型     |進階 SSD、標準 SSD (預覽) 和標準 HDD |進階 SSD、標準 HDD |
|進階磁碟  |完全支援 |可佈建，但無效能限制或保證  |
|進階磁碟 IOP  |取決於磁碟大小  |每一磁碟 2300 個 IOP |
|進階磁碟輸送量 |取決於磁碟大小 |每個磁碟 145 MB/秒 |
|磁碟大小  |Azure 進階磁碟：P4 (32 GiB) 至 P80 (32 TiB)<br>Azure 標準 SSD 磁碟：E10 (128 GiB) 至 E80 (32 TiB)<br>Azure 標準 HDD 磁碟：S4 (32 GiB) 至 S80 (32 TiB) |M4：32 GiB<br>M6：64 GiB<br>M10：128 GiB<br>M15：256 GiB<br>M20：512 GiB<br>M30：1024 GiB |
|磁碟效能分析 |彙總計量和每個磁碟支援的計量 |尚不支援 |
|移轉      |提供從現有非受控 Azure Resource Manager VM 進行移轉的工具，而不需要重新建立 VM  |尚不支援 |


## <a name="metrics"></a>度量
儲存體計量也有些差異：
- 使用 Azure Stack，儲存體度量中的交易資料並不區分內部或外部網路頻寬。
- 儲存體度量中的 Azure Stack 交易資料並不包含虛擬機器對所掛接磁碟的存取。


## <a name="api-versions"></a>API 版本
Azure Stack 受控磁碟支援下列 API 版本：
- 2017-03-30


## <a name="next-steps"></a>後續步驟
[了解 Azure Stack 虛擬機器](azure-stack-compute-overview.md)
