---
title: Azure Cosmos DB 多宿主權益
description: 瞭解 Azure Cosmos DB 中多宿主的優點，以及單一和多個寫入位置的延遲和 SLA 需求比較。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872004"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>瞭解 Azure Cosmos DB 中的多宿主優點

Cosmos DB 帳戶操作員應選擇適當的全域散發設定，以確保其應用程式的延遲、可用性和 RTO 需求。 使用多個寫入位置設定的 Azure Cosmos 帳戶，對具有單一寫入位置的帳戶提供顯著的優點，包括99.999% 寫入可用性 SLA、在第99個百分位數 < 10 毫秒寫入延遲 SLA，以及在區域性嚴重損壞中的 RTO = 0。

## <a name="comparison-of-features"></a>功能比較

|應用程式需求|多個寫入位置|單一寫入位置|附註|
|---|---|---|---|
|在 P99 < 10 毫秒的寫入延遲 SLA|**是**|否|具有單一寫入位置的帳戶會針對每個寫入產生額外的跨區域網路延遲。|
|請在 P99 閱讀 < 10 毫秒的延遲 SLA|**是**|是| |
|99.999% 的寫入 SLA|**是**|否|具有單一寫入位置的帳戶保證 SLA 為99.99%|
|RTO = 0|**是**|否|萬一發生區域災難，則會以零停機的時間進行寫入。 具有單一寫入位置的帳戶具有15分鐘的 RTO。|

## <a name="next-steps"></a>後續步驟

如果您仍想要停用 Azure Cosmos 帳戶中的 EnableMultipleWriteLocations，請[開啟支援票證](https://azure.microsoft.com/support/create-ticket/)。
