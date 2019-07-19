---
title: 為您的 Azure 資料總管叢集選取正確的 VM SKU
description: 本文說明如何為 Azure 資料總管叢集選取最佳的 SKU 大小。
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 0239111ca56dfe431a00eee83c79eedccc66c927
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226139"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>為您的 Azure 資料總管叢集選取正確的 VM SKU 

Azure 資料總管有多個 VM Sku, 可供您在建立新叢集或針對變更的工作負載優化叢集時從中選擇。 已謹慎選擇 Vm, 以允許任何工作負載的最佳成本。 

資料管理叢集的大小和 VM SKU 完全由 Azure 資料總管服務管理。 其取決於引擎的 VM 大小和內嵌工作負載等因素。 

藉由相應[增加](manage-cluster-vertical-scaling.md)叢集, 可以隨時變更引擎叢集的 VM SKU。 因此, 最好是從符合初始案例的最低 SKU 大小開始。 請記住, 在使用新的 VM SKU 重新建立叢集時, 相應增加叢集會導致最多30分鐘的停機時間。

> [!TIP]
> 計算[RI (保留實例)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances)適用于 Azure 資料總管叢集。  

本文說明不同的 VM SKU 選項, 並提供可協助您做出最佳選擇的技術詳細資料。

## <a name="select-the-cluster-type"></a>選取叢集類型

Azure 資料總管提供兩種類型的叢集:

* **生產**:生產叢集包含兩個適用于引擎和資料管理叢集的節點, 並可在 Azure 資料總管[SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)下運作。

* **開發/測試 (無 SLA)** :開發/測試叢集具有引擎叢集的單一 D11_v2 節點, 以及適用于資料管理叢集的單一 D1 節點。 此叢集類型是最低的成本設定, 因為實例計數較低, 而且沒有任何引擎標記費用。 此叢集設定沒有任何 SLA, 因為它缺乏冗余。

## <a name="sku-types"></a>SKU 類型

建立 Azure 資料總管叢集時, 請為規劃的工作負載選取*最佳*的 VM SKU。 Azure 資料總管有兩個 SKU 系列可供選擇:

* **D_V2**:**D** SKU 的計算優化, 並提供兩種方式。
    * VM 本身
    * 與 premium 儲存體磁片配套的 VM

* **LS**:**L** SKU 是儲存體優化的。 其 SSD 大小遠高於類似定價的**D** SKU。

下表提供可用 SKU 類型之間的主要差異:
 
|**屬性** | **D SKU** | **L SKU**
|---|---|---
|**小型 Sku**|大小最小為 11, 有兩個核心|最小大小為具有四個核心的 ' L4 '
|**可用性**|適用于所有區域 (DS + PS 版本的可用性較有限)|適用于幾個區域
|**每個核心的每 GB 快取成本**|具有 D SKU 的高, DS + PS 版本較低|使用隨用隨*付*選項的最低價
|**RI (保留實例) 定價**|高折扣 (超過 55%, 適用于三年的承諾用量)|折扣較低 (3 年承諾用量為 20%)  

## <a name="select-your-cluster-vm"></a>選取您的叢集 VM 

若要選取叢集 VM, 請[設定垂直調整](manage-cluster-vertical-scaling.md#configure-vertical-scaling)。 

不同的 VM SKU 選項可讓您針對所需的案例, 針對必要的效能和快取記憶體需求, 將成本優化。 如果案例需要高查詢量的最佳效能, 理想的 SKU 應該是計算優化。 另一方面, 如果此案例需要以相對較低的查詢負載來查詢大量資料, 則儲存體優化的 SKU 會降低成本, 同時仍提供絕佳的效能。

針對小型 Sku, 每個叢集的實例數目會受到限制, 因此最好使用具有較大 RAM 的較大 Vm。 針對 RAM 資源 (例如使用`joins`的查詢) 的某些查詢類型, 需要 ram 大小。 因此, 在考慮調整選項時, 建議您新增更多實例, 以相應放大為較大的 SKU。

## <a name="vm-options"></a>VM 選項

下表提供 Azure 資料總管叢集 Vm 的技術規格:

|**名稱**| **分類** | **SSD 大小** | **核心** | **RAM** | **Premium 儲存體磁片 (1 TB)**| **每個叢集的最小實例計數** | **每個叢集的實例計數上限**
|---|---|---|---|---|---|---|---
|D11_v2| 計算優化 | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (除了開發/測試 SKU, 其為 1)
|D12_v2| 計算優化 | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| 計算優化 | 307 GB   | 8 | 56 GB | 0 | 2 | 1,000
|D14_v2| 計算優化 | 614 GB   | 16| 112 GB | 0 | 2 | 1,000
|DS13_v2 + 1TB PS| 儲存體優化 | 1 TB | 8 | 56 GB | 1 | 2 | 1,000
|DS13_v2 + 2TB PS| 儲存體優化 | 2 TB | 8 | 56 GB | 2 | 2 | 1,000
|DS14_v2 + 3TB PS| 儲存體優化 | 3 TB | 16 | 112 GB | 2 | 2 | 1,000
|DS14_v2 + 4TB PS| 儲存體優化 | 4 TB | 16 | 112 GB | 4 | 2 | 1,000
|L4s_v1| 儲存體優化 | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| 儲存體優化 | 1.3 TB | 8 | 64 GB | 0 | 2 | 1,000
|L16s_1| 儲存體優化 | 2.6 TB | 16| 128 GB | 0 | 2 | 1,000

* 使用 Azure 資料總管[LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet), 來查看每個區域的已更新 VM SKU 清單。 
* 深入瞭解不同的[計算 sku](/azure/virtual-machines/windows/sizes-compute)。 

## <a name="next-steps"></a>後續步驟

* 藉由變更 VM SKU 以因應不同的需求, 可以隨時相應[增加或減少](manage-cluster-vertical-scaling.md)引擎叢集。 

* 您可以相應縮小[和](manage-cluster-horizontal-scaling.md)放大引擎叢集的大小, 以改變需求的容量。

