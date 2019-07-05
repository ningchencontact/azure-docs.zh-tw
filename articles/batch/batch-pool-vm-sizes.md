---
title: 為集區選取 VM 大小 - Azure Batch | Microsoft Docs
description: 如何為 Azure Batch 集區中的計算節點選取可用的 VM 大小
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 1848891a0a37235c9769b3cee18262239e19df5a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502659"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>為 Azure Batch 集區中的運算節點選擇 VM 大小

當您為 Azure Batch 集區選取節點大小時，可以從 Azure 中幾乎所有可用的 VM 大小進行選擇。 針對不同的工作負載，Azure 提供許多適用於 Linux 和 Windows VM 的大小。

選擇 VM 大小時有幾個例外狀況和限制：

* 批次中不支援某些 VM 系列或 VM 大小。
* 有些 VM 大小會有限制，而且必須特別啟用後才能配置。

## <a name="supported-vm-series-and-sizes"></a>支援的 VM 系列和大小

### <a name="pools-in-virtual-machine-configuration"></a>虛擬機器組態中的集區

虛擬機器組態中的 batch 集區支援幾乎所有的 VM 大小 ([Linux](../virtual-machines/linux/sizes.md)， [Windows](../virtual-machines/windows/sizes.md))。 請參閱下表，以深入了解支援的大小和限制。

任何促銷或未列出的預覽 VM 大小不保證支援。

| VM 系列  | 支援的大小 | Batch 帳戶集區配置模式<sup>1</sup> |
|------------|---------|-----------------|
| 基本 A 系列 | 所有的大小*除了*Basic_A0 (A0) | 任意 |
| A 系列 | 所有的大小*除了*Standard_A0 | 任意 |
| Av2 系列 | 所有大小 | 任意 |
| B 系列 | None | 尚未提供 |
| DC 系列 | None | 尚未提供 |
| Dsv2 系列、 Dv2 | 所有大小 | 任意 |
| Dv3、 Dsv3 系列 | 所有大小 | 任意 |
| [記憶體最佳化大小](../virtual-machines/linux/sizes-memory.md) | None | 尚未提供 |
| Fsv2 系列 | 所有大小 | 任意 |
| H 系列 | 所有大小 | 任意 |
| Hb 系列<sup>2</sup> | 所有大小 | 任意 |
| Hc 系列<sup>2</sup> | 所有大小 | 任意 |
| Ls 系列 | 所有大小 | 任意 |
| Lsv2 系列 | None | 尚未提供 |
| M 系列 | Standard_M64ms （低優先順序只），Standard_M128s （只有低優先順序） | 任意 |  
| NCv2-series<sup>2</sup> | 所有大小 | 任意 |
| NCv3-series<sup>2</sup> | 所有大小 | 任意 |
| ND 系列<sup>2</sup> | 所有大小 | 任意 |
| NDv2 系列 | 所有大小 | 使用者訂用帳戶模式 |
| NV 系列 | 所有大小 | 任意 |
| NVv3 系列 | None | 尚未提供 |
| SAP HANA | None | 尚未提供 |

<sup>1</sup>部分最初支援一些較新的 VM 系列。 這些 VM 系列可由 Batch 帳戶分配**集區配置模式**設為**使用者訂用帳戶**。 請參閱[管理 Batch 帳戶](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)如需有關批次帳戶組態。 請參閱[配額和限制](batch-quota-limit.md)若要了解如何要求配額這些部分支援的 VM 系列**使用者訂用帳戶**Batch 帳戶。  

<sup>2</sup>這些 VM 大小可配置在 Batch 集區中虛擬機器組態，但您必須要求特定[增加配額](batch-quota-limit.md#increase-a-quota)。

### <a name="pools-in-cloud-service-configuration"></a>雲端服務組態中的集區

雲端服務組態中的 Batch 集區支援所有[適用於雲端服務的 VM 大小](../cloud-services/cloud-services-sizes-specs.md)，「除了」  下列幾個：

| VM 系列  | 不支援的大小 |
|------------|-------------------|
| A 系列   | 超小型       |
| Av2 系列 | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>大小考量

* **應用程式需求** - 請考量將於節點上執行之應用程式的特性和需求。 應用程式是否為多執行緒以及需要使用多少記憶體之類的層面，有助於決定最適合且具成本效益的節點大小。 針對多重執行個體的 [MPI 工作負載](batch-mpi.md)或 CUDA 應用程式，請考慮分別使用特殊 [HPC](../virtual-machines/linux/sizes-hpc.md) 或[已啟用 GPU](../virtual-machines/linux/sizes-gpu.md) 的 VM 大小。 (請參閱[在 Batch 集區中使用具備 RDMA 功能或已啟用 GPU 功能的執行個體](batch-pool-compute-intensive-sizes.md)。)

* **每個節點的工作** - 在選取節點大小時，通常會假設每次在節點上執行一項工作。 不過，在作業執行期間有多項工作 (因而有多個應用程式執行個體) 在計算節點上[以平行方式執行](batch-parallel-node-tasks.md)，也可能有好處。 在此情況下，通常會選擇多核心節點大小，以因應增加的平行工作執行需求。

* **不同工作的負載層級** - 集區中的所有節點都是相同大小。 如果您打算執行具有不同系統需求和/或負載層級的應用程式，建議使用不同的集區。

* **區域可用性**-VM 系列或大小可能無法使用您用來建立 Batch 帳戶的區域中。 若要確認是否有提供某個大小，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。

* **配額** - Batch 帳戶中的[核心配額](batch-quota-limit.md#resource-quotas)可能會限制您可以新增至 Batch 集區的指定大小節點數目。 若要要求增加配額，請參閱[本文](batch-quota-limit.md#increase-a-quota)。 

* **集區組態** - 一般來說，如果您在虛擬機器組態中建立集區，您可選擇的 VM 大小會比雲端服務組態多。

## <a name="next-steps"></a>後續步驟

* 如需 Batch 的深入概觀，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)。
* 如需使用計算密集型 VM 大小的相關資訊，請參閱[在 Batch 集區中使用具備 RDMA 功能或已啟用 GPU 功能的執行個體](batch-pool-compute-intensive-sizes.md)。
