---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5b7aa618df63bf813e0ab0d77025cf57cfe55c3a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964529"
---
| 資源 | 目標 |
|----------|---------------|
| 單一資料表的大小上限 | 500 TiB |
| 資料表實體的大小上限 | 1 MiB |
| 資料表實體中屬性的數目上限 | 255 (包括 3 個系統屬性：PartitionKey、RowKey 和 Timestamp) |
| 每個資料表的預存存取原則的最大數目 | 5 |
| 每一儲存體帳戶的要求率上限 | 每秒 20,000 則交易 (假設 1 KiB 實體大小) |
| 單一資料表分割的目標輸送量 (1 KiB 實體) | 每秒最多 2000 個實體 |
