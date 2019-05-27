---
title: 包含檔案
description: 包含檔案
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160498"
---
您可以建立多個訂用帳戶內的服務。 每一個可以佈建於特定層。 您受限於只允許每一層的服務數目。 例如，您最多可在基本層建立 12 個服務，並在同一個訂用帳戶內的 S1 層另外建立 12 個服務。 如需各層的詳細資訊，請參閱[選擇 Azure 搜尋服務的 SKU 或階層](../articles/search/search-sku-tier.md)。

最大服務限制可以視要求引發。 如果您需要更多服務相同的訂用帳戶，請連絡 Azure 支援。

| Resource            | 免費<sup>1</sup> | 基本 | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| 服務數目上限    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| 以搜尋單位 (SU) 的最大調整規模<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> 免費服務是根據共用而非專用的資源。 共用資源上不支援相應增加。

<sup>2</sup>搜尋單位可計費單位，形式配置*複本*或是*分割*。 您需要這兩種資源才能進行儲存、編製索引及查詢作業。 若要深入了解 SU 計算，請參閱[針對查詢和索引工作負載調整資源層級](../articles/search/search-capacity-planning.md)。 