---
title: 作者實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配作者實體使用的屬性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146502"
---
# <a name="author-entity"></a>作者實體

> [!NOTE]
> 下列是 author 實體特有的屬性。 （Ty = ' 1 '）

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
識別碼      | 實體識別碼                             |Int64      |等於
AuN     | 作者標準化名稱                    |String     |等於
CC      | 作者引用總數           |Int32      |None  
DAuN    | 作者顯示名稱                   |String     |None
E | 延伸的中繼資料</br></br>**重要**事項：此屬性已被取代，而且只支援繼承應用程式。 個別要求此屬性（亦即，attribute = Id，Ti，E）會導致在*序列化 JSON 字串*中傳回所有擴充中繼資料屬性</br></br>延伸中繼資料中包含的所有屬性現在都可做為最上層屬性，並可依此方式要求（也就是屬性 = Id，Ti，DOI，IA） | [段](#extended) | None
ECC     | 作者引用預估總數 |Int32      |None
LKA.AfId | 為作者找到的最後一個已知關係的實體識別碼 | Int64 | None
LKA.AfN | 為作者找到的最後一個已知關係的正規化名稱 | String | None
PC | 撰寫總發行集計數 | Int32 | None

## <a name="extended"></a>段

> [!IMPORTANT]
> 此屬性已被取代，而且只支援繼承應用程式。 個別要求此屬性（亦即，attribute = Id，Ti，E）會導致在*序列化 JSON 字串*中傳回所有擴充中繼資料屬性</br></br>延伸中繼資料中包含的所有屬性現在都可做為最上層屬性，並可依此方式要求（也就是屬性 = Id，Ti，DOI，IA）

> [!IMPORTANT]
> 支援使用 "E" 來要求個別的擴充屬性。 範圍，亦即 "E. DN" 已被取代。 雖然這在技術上仍受到支援，但使用 "E" 來要求個別的擴充屬性。 範圍會導致屬性值在 JSON 回應中的兩個位置傳回，做為 "E" 物件的一部分，並做為最上層屬性。

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
LKA.AfId | 為作者找到的最後一個已知關係的實體識別碼 | Int64 | None
LKA.AfN | 為作者找到的最後一個已知關係的正規化名稱 | String | None
PC | 撰寫總發行集計數 | Int32 | None
