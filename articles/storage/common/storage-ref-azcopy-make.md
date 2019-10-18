---
title: azcopy make |Microsoft Docs
description: 本文提供 azcopy make 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebed5c8dbe5001e9beab17bdbff41610277143b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514701"
---
# <a name="azcopy-make"></a>azcopy 製作

建立容器或檔案共用。

## <a name="synopsis"></a>摘要

建立以指定的資源 URL 表示的容器或檔案共用。

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>範例

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>選項

|選項|描述|
|--|--|
|-h, --help|顯示 make 命令的說明內容。 |
|--quota-gb uint32|指定共用的大小上限（以 gb （GiB）為單位），0表示您接受檔案服務的預設配額。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>請參閱

- [azcopy](storage-ref-azcopy.md)
