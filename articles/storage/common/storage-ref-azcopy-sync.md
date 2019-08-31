---
title: azcopy 同步 |Microsoft Docs
description: 本文提供 azcopy 同步命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195759"
---
# <a name="azcopy-sync"></a>azcopy 同步

將來源位置複製到目的地位置。

## <a name="synopsis"></a>摘要

上次修改時間是用來進行比較。 如果目的地中上次修改時間較新, 就會略過該檔案。

支援的配對如下:

- 本機 <-> Azure Blob (可以使用 SAS 或 OAuth 驗證)

Sync 命令與 copy 命令的差異有好幾種:

  1. [遞迴] 旗標預設為開啟。
  2. 來源和目的地不應包含模式 (例如 * 或？)。
  3. [包含] 和 [排除] 旗標可以是與檔案名相符的模式清單。 如需說明, 請參閱範例一節。
  4. 如果來源上沒有檔案或 blob, 則會提示使用者將其刪除。

     您可以使用對應的旗標來靜音此提示, 以自動回答刪除問題。

### <a name="advanced"></a>進階

從本機磁片上傳時, AzCopy 會根據副檔名或內容 (如果未指定副檔名), 自動偵測檔案的內容類型。

內建查閱資料表很小, 但是在 Unix 上, 它會透過本機系統的 mime. 類型檔案來增強 (如果有一或多個這些名稱可用的話):

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上, MIME 類型會從登錄中解壓縮。

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>範例

同步處理單一檔案:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

與上述相同, 但這次也會計算檔案內容的 MD5 雜湊, 並將它儲存為 blob 的 Content-MD5 屬性:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

同步整個目錄 (包括其子目錄) (請注意, 遞迴預設為開啟):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

或

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

只同步目錄中的前幾個檔案, 而不是它的子目錄:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

同步目錄中的檔案子集 (例如: 只有 jpg 和 pdf 檔案, 或檔案名為 "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

同步整個目錄, 但排除範圍中的某些檔案 (例如: 以 foo 或結尾為 bar 的每個檔案):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

> [!NOTE]
> 如果同時使用包含/排除旗標, 則只會查看符合 include 模式的檔案, 但是符合排除模式的檔一律會被忽略。

## <a name="options"></a>選項。

|選項|描述|
|--|--|
|--區塊-大小-mb float|當上傳至 Azure 儲存體或從 Azure 儲存體下載時, 請使用此區塊大小 (以 MiB 指定)。 預設值會根據檔案大小自動計算。 允許小數分數 (例如:0.25)。|
|--check-md5 字串|指定下載時應如何驗證嚴格的 MD5 雜湊。 只有在下載時, 才可以使用此選項。 可用的值包括:NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing。 (預設值為 "FailIfDifferent")。|
|--delete-目的地字串|定義是否要從目的地刪除不存在於來源的額外檔案。 可以設定為 true、false 或 prompt。 如果設定為 [提示], 則會在排程檔案和 blob 以供刪除之前, 詢問使用者一個問題。 (預設值為 "false")。|
|--排除字串|排除名稱符合模式清單的檔案。 例如: *.jpg;* 。pdf; exactName。|
|-h, --help|顯示同步命令的說明內容。|
|--include 字串|只包含名稱符合模式清單的檔案。 例如: *.jpg;* 。pdf; exactName。|
|--記錄層級字串|定義記錄檔的記錄詳細資訊, 可用的層級:INFO (所有要求/回應)、警告 (回應緩慢)、錯誤 (僅限失敗的要求) 和無 (沒有輸出記錄)。 (預設值為 "INFO")。|
|--put-md5|建立每個檔案的 MD5 雜湊, 並將雜湊儲存為目的地 blob 或檔案的 Content-MD5 屬性。 (根據預設, 不會建立雜湊)。只有在上傳時才可使用。|
|--遞迴|True 根據預設, 在目錄之間同步時, 會以遞迴方式查看子目錄。 (預設值為 true)。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限 (以每秒 mb 為單位) 傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略, 則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括: text、json。 預設值為 "text"。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
