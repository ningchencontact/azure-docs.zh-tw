---
title: azcopy 作業清單 |Microsoft Docs
description: 本文提供 azcopy 作業清單命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ff10a1e4544f80d618d6989ab251096e6cb77baf
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195980"
---
# <a name="azcopy-jobs-list"></a>azcopy 作業清單

顯示所有作業的資訊。

## <a name="synopsis"></a>摘要

```azcopy
azcopy jobs list [flags]
```

## <a name="options"></a>選項。

|選項|描述|
|--|--|
|-h, --help|顯示 list 命令的說明內容。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限 (以每秒 mb 為單位) 傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略, 則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括: text、json。 預設值為 "text"。|

## <a name="see-also"></a>另請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
