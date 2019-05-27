---
title: 包含檔案
description: 包含檔案
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 712b70960e09a9c2b0e7a998bc0bddbc28c1e112
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146230"
---
| Resource | 預設限制 | 上限 |
| --- | --- | --- |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md) VM |25,000<sup>1</sup>每個區域。 |每個區域的 25000。 |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 總計核心 |20<sup>1</sup>每個區域。 | 請連絡支援人員。 |
| 每個核心的 VM，每個數列，例如 Dv2 和 F，[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup>每個區域。 | 請連絡支援人員。 |
| [共同管理員](../articles/billing-add-change-azure-subscription-administrator.md)每訂用帳戶 |無限制。 |無限制。 |
| 每一訂用帳戶每一區域的[儲存體帳戶](../articles/storage/common/storage-quickstart-create-account.md) |250 |250 |
| [資源群組](../articles/azure-resource-manager/resource-group-overview.md)每訂用帳戶 |980 |980 |
| [可用性設定組](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)每訂用帳戶 |每個區域 2,000。 |每個區域 2,000。 |
| Azure Resource Manager API 要求的大小 |4,194,304 位元組。 |4,194,304 位元組。 |
| 每個訂用帳戶的標籤<sup>2</sup> |無限制。 |無限制。 |
| 每個訂用帳戶的唯一標記計算<sup>2</sup> | 10,000 | 10,000 |
| 每一訂用帳戶[雲端服務](../articles/cloud-services/cloud-services-choose-me.md) |N/A<sup>3</sup> |N/A<sup>3</sup> |
| 每一訂用帳戶[同質群組](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [訂用帳戶層級部署](../articles/azure-resource-manager/deploy-to-subscription.md)每個位置 | 800<sup>4</sup> | 800 |

<sup>1</sup>預設限制而有所不同，供應項目分類類型，例如免費試用版 」 和 「 隨用隨付，以及系列、 Dv2、 F 等 g。

<sup>2</sup>您可以套用無限的數量的每個訂用帳戶的標籤。 每個資源或資源群組的標記數目受限於 15。 資源管理員會傳回[唯一的標記名稱和值的清單](/rest/api/resources/tags)訂用帳戶，只有當標記數目是 10,000 或更少。 您仍然可以找到資源的標記數目超出 10,000 時。  

<sup>3</sup>已無法再使用 Azure 資源群組和 Resource Manager 需要這些功能。

<sup>4</sup>達到 800 部署的限制，如果從不再需要的歷程記錄中刪除部署。 若要刪除訂用帳戶層級的部署，請使用[移除 AzDeployment](/powershell/module/az.resources/Remove-AzDeployment)或是[az 部署刪除](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)。

> [!NOTE]
> 虛擬機器核心具有區域總數限制。 它們也有限制的區域的每個大小系列，例如 Dv2 和 f。這些限制會分別會強制執行。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶可以部署 30 A1 Vm，或 30 D1 Vm 或兩者的組合，不能超過 30 個核心的總計。 10 個 A1 Vm 和 20 個 D1 Vm 組合的範例。  
> <!-- -->
> 
> 

