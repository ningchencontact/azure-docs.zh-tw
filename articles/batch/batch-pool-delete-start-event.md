---
title: Azure Batch 集區刪除開始事件 | Microsoft Docs
description: Batch 集區刪除開始事件的參考。
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774534"
---
# <a name="pool-delete-start-event"></a>池删除开始事件

 当池删除操作开始时，会发出此事件。 由於集區刪除為非同步事件，因此您可以預期當刪除作業完成時，就會發出集區刪除完成事件。

 下列範例顯示集區刪除開始事件內文。

```
{
    "id": "myPool1"
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|id|字串|集區識別碼。|

<!-- Update_Description: update metedata properties -->