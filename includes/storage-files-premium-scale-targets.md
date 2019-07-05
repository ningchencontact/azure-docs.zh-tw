---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542642"
---
#### <a name="additional-premium-file-share-level-limits"></a>其他進階檔案共用層級的限制

|領域  |目標  |
|---------|---------|
|最小的大小增加/減少    |1 GiB      |
|基準 IOPS    |每 GiB，最多 100,000 個 1 IOPS|
|負載平衡的 IOPS    |3 個 x IOPS 每 GiB，最多 100,000 個|
|輸出速率         |60 MiB/秒 + $0.06 * 佈建 GiB        |
|輸入速率| 40 MiB/秒 + 0.04 * 佈建 GiB |

#### <a name="file-level-limits"></a>檔案層級的限制

|領域  |進階檔案  |標準檔案 |
|---------|---------|---------|
|大小                  |1 TiB         |1 TiB|
|每個檔案的最大 IOPS     |5,000         |1,000|
|並行控制代碼    |2,000         |2,000|
|輸入  |300 MiB/sec|      請參閱標準檔案處理量值|
|輸出   |200 Mib/sec| 請參閱標準檔案處理量值|
|Throughput| 請參閱進階檔案輸入/輸出值| 最高 60 MiB/秒|