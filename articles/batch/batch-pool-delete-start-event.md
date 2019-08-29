---
title: Azure Batch 集區刪除開始事件 | Microsoft Docs
description: Batch 集區刪除開始事件的參考。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 6f8b1bdd680d43cb14707338d2e37e41114126b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094751"
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
|ID|String|集區識別碼。|