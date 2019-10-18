---
title: azcopy 作業 |Microsoft Docs
description: 本文提供 azcopy 作業命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2422b16eb89ef6f1a6a1eb703d88f0ff2b76422e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514727"
---
# <a name="azcopy-jobs"></a>azcopy 作業

與管理作業相關的子命令。

## <a name="examples"></a>範例

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>選項

|選項|描述|
|--|--|
|-h, --help|顯示 [作業] 命令的說明內容。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>請參閱

- [azcopy](storage-ref-azcopy.md)
- [azcopy 作業清單](storage-ref-azcopy-jobs-list.md)
- [azcopy 作業繼續](storage-ref-azcopy-jobs-resume.md)
- [azcopy 作業顯示](storage-ref-azcopy-jobs-show.md)
