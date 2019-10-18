---
title: azcopy 檔 |Microsoft Docs
description: 本文提供 azcopy doc 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513863"
---
# <a name="azcopy-doc"></a>azcopy 文件

以 Markdown 格式產生工具的檔。

## <a name="synopsis"></a>摘要

以 Markdown 格式產生工具的檔，並將其儲存在指定的位置。

根據預設，檔案會儲存在目前目錄中名為 ' doc ' 的資料夾中。

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>選項

|選項|描述|
|--|--|
|-h, --help|顯示 doc 命令的說明內容。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>請參閱

- [azcopy](storage-ref-azcopy.md)
