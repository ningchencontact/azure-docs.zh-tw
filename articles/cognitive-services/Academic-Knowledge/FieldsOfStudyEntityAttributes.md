---
title: 研究領域實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配研究領域實體使用的屬性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146483"
---
# <a name="field-of-study-entity"></a>研究領域實體

> [!NOTE]
> 下列屬性專屬於研究領域實體的特定專案。 （Ty = ' 6 '）

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
CC      |研究領域引用總數    |Int32      |None  
DFN     |研究領域顯示名稱            |String     |None
ECC     |領域引用預估總數|Int32      |None
FL      |研究領域階層中的層級     |Int32      |Equals、IsBetween
FN      |研究領域標準化名稱         |String     |等於
FC.FId  |子系研究領域識別碼                |Int64      |等於
FC.FN   |子系研究領域名稱              |String     |等於
FP.FId  |父代研究領域識別碼               |Int64      |等於
FP.FN   |父代研究領域名稱             |String     |等於
識別碼      |實體識別碼                              |Int64      |等於
PC    | 研究的總發行集計數 | Int32 | None
