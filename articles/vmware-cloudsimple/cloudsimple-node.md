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
ms.openlocfilehash: b3c8fca1dd93f379860cc3b084fbb14d4a0c6380
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577356"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 節點概觀

節點是：

* 專用的裸機計算安裝 VMware ESXi hypervisor 的主機  
* 計算您可以購買或保留的一個單位來建立私人雲端  
* 若要購買，或保留 CloudSimple 服務可用的區域中可用

節點是私用雲端的建置組塊。  若要建立私人雲端，您需要三個節點的相同 SKU 的最小值。  若要展開私人雲端，請新增其他節點。  您可以將節點新增至現有的叢集。 或者，您可以購買 Azure 的入口網站中的節點，並將它們關聯 CloudSimple 服務建立新的叢集。  購買的所有節點都已顯示 CloudSimple 服務下的。  您可以建立私用雲端從 CloudSimple 入口網站上購買的節點。

## <a name="purchased-nodes"></a>已購買的節點

已購買的節點提供隨用隨付的容量。 購買節點，可協助您快速調整隨選 VMware 叢集。 您可以視需要新增節點，或刪除已購買的節點，以縮小您的 VMware 叢集。 購買的節點計費以每月為基礎，並購買所在的訂用帳戶計費：

* 如果您以信用卡支付 Azure 訂用帳戶，卡片會立即計費。
* 如果您透過發票計費，費用會出現在您下一步 的發票上。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware CloudSimple 節點 SKU 的解決方案

下列節點的型別可供購買或保留項目。

| SKU | CS28-節點 | CS36-節點 |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz、 核心，28 (56 HT) | 2x2.3 GHz，36 個核心 (72 HT) |
| RAM | 256 GB | 512 GB |
| 快取磁碟 |  1.6-TB NVMe | 3.2-TB NVMe |
| 容量的磁碟 | 5.625 未經處理的 TB | 11.25 未經處理的 TB |
| 儲存體類型 | All Flash | All Flash |

## <a name="limits"></a>限制

下列的節點限制適用於私人雲端。

| Resource | 限制 |
|----------|-------|
| 若要建立私人雲端的節點數目下限 | 3 |
| 在私人雲端上的叢集中的節點數目上限 | 16 |
| 私人雲端中的節點數目上限 | 64 |
| 在新叢集上的節點數目下限 | 3 |

## <a name="next-steps"></a>後續步驟

* 了解如何[購買節點](create-nodes.md)
* 深入了解[私用雲端](cloudsimple-private-cloud.md)