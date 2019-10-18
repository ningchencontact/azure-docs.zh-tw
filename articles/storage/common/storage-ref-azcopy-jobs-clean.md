---
title: azcopy 作業已清除 |Microsoft Docs
description: 本文提供 azcopy 作業 clean 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518508"
---
# <a name="azcopy-jobs-clean"></a>清除 azcopy 作業

移除所有作業的所有記錄檔和計畫檔案

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>範例

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>選項

**-h、--help**               清除的說明。

**--with-status**字串只會移除具有此狀態的工作、可用的值：已取消、已完成、失敗、InProgress、全部（預設值為 "all"）

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps uint32**     上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。

**--輸出-** 命令輸出的類型字串格式。 選項包括： text、json。 預設值為「文字」。 （預設值為 "text"）

## <a name="see-also"></a>請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
