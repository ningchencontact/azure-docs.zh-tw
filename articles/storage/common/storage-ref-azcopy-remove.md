---
title: azcopy 移除 |Microsoft Docs
description: 本文提供 azcopy remove 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195902"
---
# <a name="azcopy-remove"></a>azcopy 移除

從 Azure 儲存體 Blob、檔案和 Azure Data Lake Storage Gen2 中刪除實體。

## <a name="synopsis"></a>摘要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>範例

移除具有 SAS 的單一 blob:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

移除具有 SAS 的整個虛擬目錄:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

只移除虛擬目錄中的最上層 blob, 而不是其子目錄:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

移除虛擬目錄中的 blob 子集 (例如: 只有 jpg 和 pdf 檔案, 或如果 blob 名稱是 "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

移除整個虛擬目錄, 但從範圍中排除特定的 blob (例如: 以 foo 或結尾為 bar 的每個 blob):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

移除 Data Lake Storage Gen2 中的單一檔案 (include 和 exclude 不受支援):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

從 Data Lake Storage Gen2 移除單一目錄 (包含和排除不受支援):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>選項。

|選項|描述|
|--|--|
|--排除字串|排除名稱符合模式清單的檔案。 例如: *.jpg;* 。pdf; exactName|
|-h, --help|顯示 [移除] 命令的說明內容。|
|--include 字串|只包含名稱符合模式清單的檔案。 例如: *.jpg;* 。pdf; exactName|
|--記錄層級字串|定義記錄檔的記錄詳細資訊。 可用的層級包括:INFO (所有要求/回應)、警告 (回應緩慢)、錯誤 (僅限失敗的要求) 和無 (沒有輸出記錄)。 (預設值為 "INFO")|
|--遞迴|在目錄之間同步時, 以遞迴方式查看子目錄。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限 (以每秒 mb 為單位) 傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略, 則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括: text、json。 預設值為 "text"。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
