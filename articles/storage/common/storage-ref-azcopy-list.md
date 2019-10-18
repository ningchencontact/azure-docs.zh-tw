---
title: azcopy 清單 |Microsoft Docs
description: 本文提供 azcopy list 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513514"
---
# <a name="azcopy-list"></a>azcopy 清單

列出指定資源中的實體。

## <a name="synopsis"></a>摘要

目前的版本僅支援 Blob 容器。

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>範例

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>選項

|選項|描述|
|--|--|
|-h, --help|顯示 list 命令的說明內容。|
|--電腦可讀取|列出檔案大小（以位元組為單位）。|
|--百萬位元-單位|以1000的順序顯示單位，而不是1024。|
|--正在執行-計數|計算檔案總數及其大小。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>請參閱

- [azcopy](storage-ref-azcopy.md)
