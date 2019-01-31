---
title: Azure Batch 集區刪除開始事件 | Microsoft Docs
description: Batch 集區刪除開始事件的參考。
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474287"
---
# <a name="pool-delete-start-event"></a>集區刪除開始事件

 集區刪除作業開始時，就會發出此事件。 由於集區刪除為非同步事件，因此您可以預期當刪除作業完成時，就會發出集區刪除完成事件。

 下列範例顯示集區刪除開始事件內文。

```
{
    "id": "myPool1"
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|id|字串|集區識別碼。|