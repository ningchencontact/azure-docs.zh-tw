---
title: Azure Cosmos DB 多重主機權益
description: 了解 Azure Cosmos DB 中多個主要的優點。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789289"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>了解 Azure Cosmos DB 中的多重主機優點

Cosmos DB 帳戶操作員應該選擇適當的全域散發的設定，以確保延遲、 可用性和其應用程式的 RTO 需求。 設有多個寫入位置的 azure Cosmos 帳戶透過單一寫入位置包括帳戶提供顯著的優點，可享有達 99.999%寫入可用性 SLA、 < 10 毫秒的寫入在第 99 個百分位數和 RTO 的延遲 SLA = 區域性災害中的為 0。

## <a name="comparison-of-features"></a>功能比較

|應用程式需求|寫入的多個位置|單一寫入位置|附註|
|---|---|---|---|
|寫入的延遲 SLA < 10 毫秒在 P99|**是**|否|使用單一的寫入位置的帳戶會在每次寫入造成額外的跨區域網路延遲。|
|讀取的延遲 SLA < 10 毫秒在 P99|**是**|是| |
|撰寫可享有達 99.999%的 SLA|**是**|否|帳戶使用單一的寫入位置保證 99.99%的 SLA|
|RTO = 0|**是**|否|零停機時間寫入發生區域性災害提供防護。 使用單一的寫入位置的帳戶有 15 分鐘的 RTO。|

## <a name="next-steps"></a>後續步驟

如果您仍想要在您的 Azure Cosmos 帳戶中停用 EnableMultipleWriteLocations，請[開啟支援票證](https://azure.microsoft.com/support/create-ticket/)。
