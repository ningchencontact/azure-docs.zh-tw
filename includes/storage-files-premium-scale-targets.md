---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c9561f868c70920a0ffb80777f1fc57abafb0df9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073729"
---
#### <a name="additional-premium-file-share-limits"></a>其他進階檔案共用限制

|領域  |目標  |
|---------|---------|
|最小的大小增加/減少    |1 GiB      |
|基準 IOPS    |每 GiB，最多 100,000 個 1 IOPS|
|負載平衡的 IOPS    |3 個 x IOPS 每 GiB，最多 100,000 個|
|輸出速率         |60 MiB/秒 + $0.06 * 佈建 GiB        |
|輸入速率| 40 MiB/秒 + 0.04 * 佈建 GiB |
|快照集的最大數目        |200       |

#### <a name="premium-file-limits"></a>進階檔案限制

|領域  |目標  |
|---------|---------|
|大小                  |1 TiB         |
|每個檔案的最大 IOPS     |5,000         |
|並行控制代碼    |2,000         |