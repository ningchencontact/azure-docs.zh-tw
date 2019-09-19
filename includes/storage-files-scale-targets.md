---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9f259c3e403e933c847ac56000b1db2cd594caf5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "67449951"
---
| Resource | 標準檔案共用 | Premium 檔案共用 |
|----------|---------------|------------------------------------------|
| 檔案共用大小上限 | 沒有最小值;隨用隨付 | 100 GiB;預 |
| 檔案共用的大小上限 | 5 TiB （GA）、100 TiB （預覽） | 100 TiB |
| 檔案共用中檔案的大小上限 | 1 TiB | 1 TiB |
| 檔案共用中的檔案數目上限 | 無限制 | 無限制 |
| 每個共用的最大 IOPS | 1000 IOPS （GA），10000 IOPS （預覽） | 100,000 IOPS |
| 每個檔案共用的儲存存取原則數目上限 | 5 | 5 |
| 單一檔案共用的目標輸送量 | 最高 60 MiB/秒（GA），最高 300 MiB/秒（預覽） | 請參閱 premium 檔案共用輸入和輸出值|
| 單一檔案共用的輸出上限 | 請參閱標準檔案共用目標輸送量 | 最高 6204 MiB/秒 |
| 單一檔案共用的輸入上限 | 請參閱標準檔案共用目標輸送量 | 最高 4136 MiB/秒 |
| 每個檔案的開啟控制代碼數目上限 | 2,000 個開啟控制代碼 | 2,000 個開啟控制代碼 |
| 共用快照集的數目上限 | 200 個共用快照集 | 200 個共用快照集 |
| 物件 (目錄和檔案) 名稱長度上限 | 2048個字元 | 2048個字元 |
| 最大路徑名稱元件 (在路徑 \A\B\C\D 中，每個字母都是元件) | 255 個字元 | 255 個字元 |