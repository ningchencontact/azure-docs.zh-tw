---
title: 節點由 CloudSimple-Azure 的 VMware 解決方案的概觀
description: 深入了解 CloudSimple 節點和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165791"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 節點概觀

節點是：

* 專用的裸機計算安裝 VMware ESXi hypervisor 的主機  
* 計算您可以佈建或保留的一個單位來建立私人雲端  
* 佈建，或保留 CloudSimple 服務可用的區域中，您可以使用

節點是私用雲端的建置組塊。  若要建立私人雲端，您需要三個節點的相同 SKU 的最小值。  若要展開私人雲端，請新增其他節點。  您可以將節點新增至現有的叢集。 或者，您可以建立新的叢集佈建在 Azure 入口網站中的節點，並將它們關聯 CloudSimple 服務。  佈建的所有節點都已顯示 CloudSimple 服務下的。  您可以建立私用雲端從 CloudSimple 入口網站上的佈建節點。

## <a name="provisioned-nodes"></a>佈建的節點

佈建的節點提供隨用隨付的容量。 佈建節點，可協助您快速調整隨選 VMware 叢集。 您可以視需要新增節點，或刪除佈建的節點，以縮小您的 VMware 叢集。 佈建的節點計費以每月為基礎，並會向它們要佈建所在的訂用帳戶收取費用：

* 如果您以信用卡支付 Azure 訂用帳戶，卡片會立即計費。
* 如果您透過發票計費，費用會出現在您下一步 的發票上。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware CloudSimple 節點 SKU 的解決方案

下列節點的型別可供佈建或保留項目。

| SKU | CS28-節點 | CS36-節點 |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz、 核心，28 (56 HT) | 2x2.3 GHz，36 個核心 (72 HT) |
| RAM | 256 GB | 512 GB |
| 快取磁碟 |  1.6-TB NVMe | 3.2-TB NVMe |
| 容量的磁碟 | 5.625 未經處理的 TB | 11.25 未經處理的 TB |
| 儲存體類型 | All Flash | All Flash |

## <a name="limits"></a>限制

下列的節點限制適用於私人雲端。

| 資源 | 限制 |
|----------|-------|
| 若要建立私人雲端的節點數目下限 | 3 |
| 在私人雲端上的叢集中的節點數目上限 | 16 |
| 私人雲端中的節點數目上限 | 64 |
| 在新叢集上的節點數目下限 | 3 |

## <a name="next-steps"></a>後續步驟

* 了解如何[佈建節點](create-nodes.md)
* 深入了解[私用雲端](cloudsimple-private-cloud.md)