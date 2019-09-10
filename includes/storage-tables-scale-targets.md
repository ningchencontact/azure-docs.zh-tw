---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "67457259"
---
| Resource | 目標 |
|----------|---------------|
| 單一資料表的大小上限 | 500 TiB |
| 資料表實體的大小上限 | 1 MiB |
| 資料表實體中的屬性數目上限 | 255，其中包含三個系統屬性：PartitionKey、RowKey 和 Timestamp |
| 實體中屬性值的總大小上限 | 1 MiB |
| 實體中個別屬性的總大小上限 | 依屬性類型而有所不同。 如需詳細資訊，請參閱[瞭解表格服務資料模型](/rest/api/storageservices/understanding-the-table-service-data-model)中的**屬性類型**。 |
| 每個資料表的預存存取原則數目上限 | 5 |
| 每一儲存體帳戶的要求率上限 | 每秒20000筆交易，這假設有一個 KiB 的實體大小 |
| 單一資料表分割的目標輸送量（1個 KiB-個實體） | 最多每秒2000個實體 |