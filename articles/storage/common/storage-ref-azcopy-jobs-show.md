---
title: azcopy 作業顯示 |Microsoft Docs
description: 本文提供 azcopy 作業 show 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195954"
---
# <a name="azcopy-jobs-show"></a>azcopy 作業顯示

顯示給定作業識別碼的詳細資訊。

## <a name="synopsis"></a>摘要

如果只有在沒有旗標的情況下提供作業識別碼, 則會傳回作業的進度摘要。

如果設定`with-status`了旗標, 則會顯示作業中具有指定值的傳輸清單。

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>選項。

|選項|描述|
|--|--|
|-h, --help|顯示 show 命令的說明內容。|
|--with-status string|僅列出具有此狀態的作業傳輸, 可用的值如下:已啟動、成功、失敗|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限 (以每秒 mb 為單位) 傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略, 則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括: text、json。 預設值為 "text"。|

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
