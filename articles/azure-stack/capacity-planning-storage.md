---
title: Azure Stack 的儲存體容量規劃 | Microsoft Docs
description: 了解 Azure Stack 部署的儲存體容量規劃。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 02/20/2019
ms.openlocfilehash: 32e6e8ff4c37554a0c3fa50e243b241eed2953cf
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445996"
---
# <a name="azure-stack-storage-capacity-planning"></a>Azure Stack 儲存體容量規劃
下列各節提供 Azure Stack 儲存體容量的規劃資訊，協助規劃解決方案的儲存體需求。

## <a name="uses-and-organization-of-storage-capacity"></a>儲存體容量的使用和安排
Azure Stack 的超融合式組態可共用實體的儲存體裝置。 可用儲存體的三個主要部分位於基礎結構、租用戶虛擬機器的暫存儲存體，以及支援 Azure Consistent Storage (ACS) 服務的 blob、資料表和佇列的儲存體之間。

## <a name="spaces-direct-cache-and-capacity-tiers"></a>空間直接存取快取和容量層
有些儲存體容量用在作業系統、本機記錄、傾印，以及其他暫存基礎結構儲存體的需求上。 此本機儲存體的容量獨立於儲存空間直接存取設定管理下的儲存體裝置 (獨立裝置和容量)。 無論縮放單位中的伺服器數目是多少，儲存體裝置的其餘部分都會放在儲存體容量的單一集區內。 這些裝置有兩種類型：快取和容量。  快取裝置僅供快取使用。 空間直接存取會耗用這些裝置，以供回寫和快取讀取之用。 在使用這些快取裝置的容量時，不會用在格式化虛擬磁碟的格式化「可見」容量上。 但容量裝置可用來達成此目的，並提供儲存空間所管理資料的「當前位置」。

所有儲存體容量會直接由 Azure Stack 基礎結構進行配置和管理。 而操作人員還是需要下達設定和配置方面的決策，或是處理涉及到容量擴充的選擇。 這些設計決策是為了配合方案需求而產生，會在初始安裝/部署期間或容量擴充期間自動化處理。 至於復原和重建保留容量的詳細資料，以及其他詳細資料均視為設計的一部分。 

操作人員子可以選擇全快閃或混合式儲存體設定：

![Azure 儲存體容量規劃](media/azure-stack-capacity-planning/storage.png)

在全快閃設定中，設定可以是雙層式或單層式設定。  如果設定是單層式，所有容量裝置都會是相同的類型 (例如 NVMe 或 SATA SSD 或 SAS SSD)，且不會使用快取裝置。 在雙層式的全快閃設定中，通常會使用 NVMe 作為快取裝置，並使用 SATA 或 SAS SSD 作為容量裝置。

在混合式的雙層式設定中，快取裝置可以是 NVMe、SATA 或 SAS SSD，而容量裝置則會是 HDD。 

儲存空間直接存取和 Azure Stack 儲存體設定的簡短摘要如下：
- 每個縮放單位一個儲存空間集區 (所有儲存體裝置都在單一集區中設定)
- 虛擬磁碟會建立為三個複本鏡像，以達最佳效能和復原功能
- 每個虛擬磁碟會格式化為 ReFS 檔案系統
- 計算和配置虛擬磁碟容量時，會在集區中保有一部容量裝置的資料容量不加以配置。 相當於每部伺服器有一部容量磁碟機。
- 每個 ReFS 檔案系統必須啟用 BitLocker，才能進行待用資料加密。 

會自動建立虛擬磁碟，其容量如下：

|Name|容量計算|說明|
|-----|-----|-----|
|本機/開機裝置|至少 340 GB<sup>1</sup>|作業系統映像和「本機」基礎結構 VM 的個別伺服器儲存體|
|基礎結構|3.5 TB|所有 Azure Stack 基礎結構的使用方式|
|VmTemp|請參閱下方<sup>2</sup>|租用戶虛擬機器已連接暫存磁碟，而且該資料會儲存在這些虛擬磁碟中|
|ACS|請參閱下方<sup>3</sup>|用於 blob、資料表和佇列的 Azure Consistent Storage 容量|

<sup>1</sup>Azure Stack 解決方案合作夥伴所需的最小儲存體容量。

<sup>2</sup>租用戶虛擬機器暫存磁碟使用的虛擬磁碟大小會以伺服器的實體記憶體比率計算。 Azure IaaS VM 的大小如下表所述，暫存磁碟指的是指派給虛擬機器的實體記憶體比率。 為 Azure Stack 中「暫存磁碟」儲存體進行的配置，會以擷取大多數使用案例的方式完成，但是無法滿足所有暫存磁碟儲存體的需求。 所選的比率必須權衡是否要提供暫存儲存體，而不要只為了暫存磁碟容量而耗用到大半解決方案儲存體的容量。 縮放單位中的每個伺服器，會建立一個暫存磁碟。 暫存儲存體的容量，不會增加到超過縮放單位的儲存體集區中，整體可用儲存體容量的 10%。 計算方式類似以下範例：

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> 供 ACS 使用所建立的虛擬磁碟，只是簡單劃分剩餘的容量而成。 如上所述，所有虛擬磁碟都是三向鏡像，而且並未給每部伺服器配置一部容量磁碟機的容量。 以上列舉的各個虛擬磁碟都是首次配置，而且剩餘的容量會用於 ACS 虛擬磁碟。

## <a name="next-steps"></a>後續步驟
[了解 Azure Stack 容量規劃試算表](capacity-planning-spreadsheet.md) (英文)
