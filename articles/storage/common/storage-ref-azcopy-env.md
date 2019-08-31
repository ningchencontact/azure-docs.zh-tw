---
title: azcopy env |Microsoft Docs
description: 本文提供 azcopy env 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 15e72493190e1bc56e779c22695bc51bd05da940
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195772"
---
# <a name="azcopy-env"></a>azcopy env

顯示可以設定 AzCopy 行為的環境變數。

## <a name="synopsis"></a>摘要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 如果您使用命令列設定環境變數, 該變數將可在您的命令列歷程記錄中讀取。 請考慮從您的命令列歷程記錄中清除包含認證的變數。 若要讓變數不會出現在歷程記錄中, 您可以使用腳本來提示使用者輸入其認證, 並設定環境變數。

## <a name="options"></a>選項。

|選項|描述|
|--|--|
|-h, --help|顯示 env 命令的說明內容。 |
|--區分顯示|顯示敏感性/密碼環境變數。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限 (以每秒 mb 為單位) 傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略, 則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括: text、json。 預設值為 "text"。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
