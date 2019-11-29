---
title: 學術知識 API 中的服務機關實體屬性
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配服務機關實體使用的屬性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143929"
---
# <a name="affiliation-entity"></a>服務機關實體

> [!NOTE]
> 下列屬性是關係實體特有的。 （Ty = ' 5 '）

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
AfN | 服務機關標準化名稱 |String |等於
CC | 服務機關引用總數 |Int32        |None  
DAfN | 服務機關顯示名稱 |String |None
E | 延伸的中繼資料</br></br>**重要**事項：此屬性已被取代，而且只支援繼承應用程式。 個別要求此屬性（亦即，attribute = Id，Ti，E）會導致在*序列化 JSON 字串*中傳回所有擴充中繼資料屬性</br></br>延伸中繼資料中包含的所有屬性現在都可做為最上層屬性，並可依此方式要求（也就是屬性 = Id，Ti，DOI，IA） | [段](#extended) | None
ECC | 服務機關引用預估總數 |Int32 |None
識別碼 | 實體識別碼 |Int64 |等於
PC | 與此實體一起寫入的發行集總數 | Int32 | None

## <a name="extended"></a>段

> [!IMPORTANT]
> 此屬性已被取代，而且只支援繼承應用程式。 個別要求此屬性（亦即，attribute = Id，Ti，E）會導致在*序列化 JSON 字串*中傳回所有擴充中繼資料屬性</br></br>延伸中繼資料中包含的所有屬性現在都可做為最上層屬性，並可依此方式要求（也就是屬性 = Id，Ti，DOI，IA）

> [!IMPORTANT]
> 支援使用 "E" 來要求個別的擴充屬性。 範圍，亦即 "E. DN" 已被取代。 雖然這在技術上仍受到支援，但使用 "E" 來要求個別的擴充屬性。 範圍會導致屬性值在 JSON 回應中的兩個位置傳回，做為 "E" 物件的一部分，並做為最上層屬性。

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
PC | 與此實體一起寫入的發行集總數 | Int32 | None
