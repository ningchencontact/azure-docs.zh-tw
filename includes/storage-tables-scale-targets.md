---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457259"
---
| 資源 | 目標 |
|----------|---------------|
| 單一資料表的大小上限 | 500 TiB |
| 資料表實體的大小上限 | 1 MiB |
| 資料表實體中屬性的最大數目 | 255，其中包含三個系統屬性：PartitionKey、 RowKey 和 Timestamp |
| 中實體的屬性值的總大小上限 | 1 MiB |
| 實體中的個別屬性的總大小上限 | 屬性類型而異。 如需詳細資訊，請參閱 <<c0>  **屬性的型別**中[了解表格服務資料模型](/rest/api/storageservices/understanding-the-table-service-data-model)。 |
| 每個資料表的預存的存取原則的最大數目 | 5 |
| 每一儲存體帳戶的要求率上限 | 20,000 每秒交易數，其中假設 1 KiB 實體大小 |
| 單一資料表分割區 （1 KiB 實體） 的目標輸送量 | 每秒最多 2,000 個實體 |