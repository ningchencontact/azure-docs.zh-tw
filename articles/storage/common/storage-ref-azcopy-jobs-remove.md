---
title: azcopy 作業移除 |Microsoft Docs
description: 本文提供 azcopy 作業 [移除] 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518287"
---
# <a name="azcopy-jobs-remove"></a>azcopy 作業移除

移除與指定之工作識別碼相關聯的所有檔案。

> [!NOTE] 
> 您可以自訂記錄檔和計畫檔案的儲存位置。 若要深入瞭解，請參閱[azcopy env](storage-ref-azcopy-env.md)命令。

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>範例

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>選項

**-h、--help**               移除的說明。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps uint32**     上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。

**--輸出-** 命令輸出的類型字串格式。 選項包括： text、json。 預設值為「文字」。 （預設值為 "text"）

## <a name="see-also"></a>請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
