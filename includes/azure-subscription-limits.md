---
title: 包含檔案
description: 包含檔案
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 3daf0e214c2c10d682e908ca430f4852df105926
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2018
ms.locfileid: "35414649"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md)的 vCPU<sup>1</sup> |20 |10,000 |
| [共同管理員](../articles/billing-add-change-azure-subscription-administrator.md)  |200 |200 |
| 每一訂用帳戶每一區域的[儲存體帳戶](../articles/storage/common/storage-create-storage-account.md)<sup>2</sup> |200 |250 |
| [雲端服務](../articles/cloud-services/cloud-services-choose-me.md)  |20 |200 |
| [區域網路](http://msdn.microsoft.com/library/jj157100.aspx)  |10 |500 |
| 每一訂用帳戶的 SQL Database 伺服器 |6 |200 |
| 每一訂閱的 DNS 伺服器 |9 |100 |
| 每一訂用帳戶的保留 IP |20 |100 |
| 每一訂用帳戶託管服務的憑證 |199 |199 |
| 每一訂用帳戶[同質群組](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |256 |256 |


<sup>1</sup>雖然只使用部分 CPU 核心，在計算 vCPU 限制時，仍會將超小型執行個體視為一個 vCPU。

<sup>2</sup>儲存體帳戶限制包括標準和進階儲存體帳戶。 如果您在指定區域需要超過 200 個儲存體帳戶，請透過 [Azure 支援](https://azure.microsoft.com/support/faq/)提出要求。 Azure 儲存體小組將會檢閱您的商務案例，而且可以針對指定區域核准多達 250 個儲存體帳戶。 

