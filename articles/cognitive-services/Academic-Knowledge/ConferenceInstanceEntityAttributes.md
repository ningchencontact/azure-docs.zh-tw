---
title: 會議執行個體實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配會議執行個體實體使用的屬性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146557"
---
# <a name="conference-instance-entity"></a>會議執行個體實體

> [!NOTE]
> 下列屬性是會議實例實體所特有。 （Ty = ' 4 '）

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
CC      |會議執行個體引用總數           |Int32      |None  
CD.D    |會議執行個體事件的日期    |日期       |Equals、IsBetween
CD.T    |會議執行個體事件的標題   |日期       |Equals、IsBetween
CIARD   |擷取會議執行個體的註冊到期日  |日期       |Equals、IsBetween
CIED    |會議執行個體的結束日期    |日期       |Equals、IsBetween
CIFVD   |會議執行個體的最終版本到期日  |日期       |Equals、IsBetween
CIL     |會議執行個體的位置    |String     |Equals、StartsWith
CIN     |會議實例正規化名稱 |String        |等於
CINDD   |會議執行個體的通知日期   |日期       |Equals、IsBetween
CISD    |會議執行個體的開始日期  |日期       |Equals、IsBetween
CISDD   |會議執行個體的提交到期日     |日期       |Equals、IsBetween
DCN     |會議實例顯示名稱  |String      |None
E | 延伸的中繼資料</br></br>**重要**事項：此屬性已被取代，而且只支援繼承應用程式。 個別要求此屬性（亦即，attribute = Id，Ti，E）會導致在*序列化 JSON 字串*中傳回所有擴充中繼資料屬性</br></br>延伸中繼資料中包含的所有屬性現在都可做為最上層屬性，並可依此方式要求（也就是屬性 = Id，Ti，DOI，IA） | [段](#extended) | None
ECC     |會議執行個體預估引用總數 |Int32      |None
FN | 會議執行個體全名 | String | None
識別碼      |實體識別碼                              |Int64      |等於
PC | 會議實例總計發行集計數 | Int32 | None
PCS.CN  |實例的父會議系列名稱 |String  |等於
PCS.CId |實例的父會議系列識別碼 |Int64     |等於

## <a name="extended"></a>段

> [!IMPORTANT]
> 此屬性已被取代，而且只支援繼承應用程式。 個別要求此屬性（亦即，attribute = Id，Ti，E）會導致在*序列化 JSON 字串*中傳回所有擴充中繼資料屬性</br></br>延伸中繼資料中包含的所有屬性現在都可做為最上層屬性，並可依此方式要求（也就是屬性 = Id，Ti，DOI，IA）

> [!IMPORTANT]
> 支援使用 "E" 來要求個別的擴充屬性。 範圍，亦即 "E. DN" 已被取代。 雖然這在技術上仍受到支援，但使用 "E" 來要求個別的擴充屬性。 範圍會導致屬性值在 JSON 回應中的兩個位置傳回，做為 "E" 物件的一部分，並做為最上層屬性。

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
FN | 會議執行個體全名 | String | None
PC | 會議實例總計發行集計數 | Int32 | None
