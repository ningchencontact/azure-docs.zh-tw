---
title: azcopy 作業繼續 |Microsoft Docs
description: 本文提供 azcopy 作業 resume 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9e8dbbd40259c7a71f252d0d6e93dd6f135973de
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513535"
---
# <a name="azcopy-jobs-resume"></a>azcopy 作業繼續

使用指定的工作識別碼繼續現有的作業。

## <a name="synopsis"></a>摘要

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>選項

|選項|描述|
|--|--|
|--目的地-sas 字串|給定 JobId 之目的地的目的地 SAS。|
|--排除字串|篩選：繼續執行工作時，請排除這些失敗的傳輸。 檔案應該以 '; ' 分隔。|
|-h, --help|顯示 resume 命令的說明內容。|
|--include 字串|篩選：當繼續作業時，只包含這些失敗的傳輸。 檔案應該以 '; ' 分隔。|
|--來源-sas 字串 |給定 JobId 之來源的來源 SAS。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>請參閱

- [azcopy 作業](storage-ref-azcopy-jobs.md)
