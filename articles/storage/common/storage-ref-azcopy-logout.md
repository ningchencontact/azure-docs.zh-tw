---
title: azcopy 登出 |Microsoft Docs
description: 本文提供 azcopy 登出命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 84b65470b12212eb1038e18bd442ff07511a5c1a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513507"
---
# <a name="azcopy-logout"></a>azcopy 登出

將使用者登出，並終止 Azure 儲存體資源的存取權。

## <a name="synopsis"></a>摘要

此命令將會移除目前使用者的所有快取登入資訊。

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>選項

|選項|描述|
|--|--|
|-h, --help|顯示 [登出] 命令的說明內容。|

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>請參閱

- [azcopy](storage-ref-azcopy.md)
