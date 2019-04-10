---
title: Azure Stack 的計算容量規劃 | Microsoft Docs
description: 了解 Azure Stack 部署的計算容量規劃。
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
ms.date: 04/03/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 04/03/2019
ms.custom: ''
ms.openlocfilehash: 437e55b1a2907418fe47f418245431fa1c882b80
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915675"
---
# <a name="azure-stack-compute-capacity-planning"></a>Azure Stack 計算容量規劃
[Azure Stack 中支援的虛擬機器大小](./user/azure-stack-vm-sizes.md)屬於 Azure 支援的虛擬機器 (VM) 的一小部分。 Azure 會在許多方面施加資源限制，來避免資源 (伺服器本機和服務層級) 過度耗用。 若未加以限制租用戶的耗用量，當其他租用戶過度耗用資源時，租用戶的體驗就會受到影響。 對於 VM 的網路輸出，在 Azure Stack 上已有符合 Azure 限制的頻寬上限。 至於儲存體資源，已在 Azure Stack 上實施儲存體的 IOP 限制，以免租用戶為了存取儲存體而造成基本的資源過度耗用。  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>VM 放置與虛擬至實體核心的過度佈建
在 Azure Stack 中，租用戶無法指定放置 VM 所用的特定伺服器。 放置 VM 的唯一考量是，主機是否有足夠的記憶體可供該 VM 類型使用。 Azure Stack 不會過量使用記憶體；但允許過量使用核心數目。 由於放置演算法不會將現有的虛擬至實體核心過度佈建比率視為因素之一，因此每部主機的比率可能各不相同。 

為了達到 Azure 中多 VM 生產環境的高可用性，會將 VM 放在可用性設定組中，以便將 VM 分散在多個容錯網域中。 在 Azure Stack 中，可用性設定組中的容錯網域會定義為縮放單位中的單一節點。

雖然 Azure Stack 的基礎結構具備失敗復原能力，但在發生硬體故障時，基礎技術 (容錯移轉叢集) 仍然會造成受影響實體伺服器上的 VM 產生一些停機時間。 目前，Azure Stack 支援的可用性設定組最多可以有三個容錯網域 (與 Azure 一致)。 系統會將放在可用性設定組中的 VM 儘可能平均分散到多個容錯網域 (Azure Stack 節點)，讓這些 VM 在實體上彼此隔離。 當發生硬體故障時，失敗容錯網域中的 VM 會在其他節點中重新啟動，但可能的話，會放在與相同可用性設定組中其他 VM 不同的容錯網域中。 當硬體回到線上時，系統會將 VM 重新平衡以保持高可用性。

Azure 為了提供高可用性所運用的另一個概念是，在可用性設定組中採取更新網域的形式。 更新網域是可以同時進行維護或重新啟動的基礎硬體邏輯群組。 在 Azure Stack 中，會先將 VM 即時移轉至叢集內的各個其他線上主機，然後才更新 VM 的基礎主機。 由於在主機更新期間並不會導致租用戶停機，因此 Azure Stack 上更新網域功能的存在只是為了與 Azure 的範本相容。

## <a name="azure-stack-resiliency-resources"></a>Azure Stack 復原資源
為了得以修補和更新 Azure Stack 整合系統，並能從實體硬體故障中復原，會保留一部分的伺服器記憶體總量，而且無法供租用戶虛擬機器 (VM) 放置使用。

如果伺服器故障，託管在故障伺服器上的 VM 會在剩餘可用的伺服器上重新啟動，以提供 VM 的可用性。 同樣地，在修補和更新程序期間，伺服器上執行的所有 VM 會即時移轉到其他可用的伺服器上。 如果保留容量能夠重新啟動或執行移轉，才能管理或移動 VM。

以下算式可計算出能用來放置租用戶 VM 的可用記憶體總量。 此記憶體容量是供整個 Azure Stack 縮放單位使用。

  放置 VM 的可用記憶體 = 伺服器記憶體總量 – 復原保留 – 執行中 VM 所使用的記憶體量 – Azure Stack 基礎結構的額外負荷<sup>1</sup>

  復原保留 = H + R * ((N-1) * H) + V * (N-2)

> 其中：
> - H = 單一伺服器記憶體的大小
> - N = 縮放單位大小 (伺服器數目)
> - R = OS 作業系統做為額外負荷的保留量<sup>2</sup>
> - V = 縮放單位中最大的 VM

  <sup>1</sup> Azure Stack 基礎結構額外負荷 = 230 GB

  <sup>2</sup> 用於額外負荷的作業系統保留量 = 15% 的節點記憶體。 作業系統保留值為估計值，會因為伺服器的實體記憶體容量和一般作業系統的額外負荷而異。

V 值 (縮放單位中最大的 VM) 會根據最大的租用戶 VM 記憶體大小而動態改變。 例如，最大 VM 值可能是 7 GB 或 112 GB，或是 Azure Stack 解決方案中任何其他支援的 VM 記憶體大小。

上述的計算只是估計值，會因 Azure Stack 的目前版本而變動。 部署租用戶 VM 和服務的能力視所部署的解決方案的詳細規格而定。 此範例算式僅供參考，並非 VM 部署能力的絕對答案。



## <a name="next-steps"></a>後續步驟
[儲存體容量規劃](capacity-planning-storage.md)
