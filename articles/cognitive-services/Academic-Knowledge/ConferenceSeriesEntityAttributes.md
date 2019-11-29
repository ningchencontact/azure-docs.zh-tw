---
title: 會議系列實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以搭配會議系列實體使用的屬性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: ecd8042212987849d9d302642f03e59493235599
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143919"
---
# <a name="conference-series-entity"></a>會議系列實體

> [!NOTE]
> 下列屬性是會議系列實體特有的屬性。 （Ty = ' 3 '）

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
CC      |會議系列引用總數         |Int32      |None  
CN      |會議系列標準化名稱      |String     |等於
DCN     |會議系列顯示名稱         |String     |None
ECC     |會議系列引用預估總數   |Int32      |None
F.FId   |與會議系列相關聯的研究領域實體識別碼 |Int64  | 等於
F.FN    |與會議系列相關聯的研究領域名稱  | Equals,<br/>StartsWith
識別碼      |實體識別碼                              |Int64      |等於
PC    |會議系列總計發行集計數 |Int32 | None
