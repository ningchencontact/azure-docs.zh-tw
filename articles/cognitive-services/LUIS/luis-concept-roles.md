---
title: 了解如何在以模式為基礎的實體中使用角色
titleSuffix: Azure Cognitive Services
description: 角色是實體的具名內容子類型，只能使用於模式中。 例如，在 buy a ticket from New York to London 語句中，New York 和 London 都是城市，但是在句子中各有不同的意義。 紐約是出發城市，而輪多是目的地城市。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8a92852a2721bd391ddf7c3cf3489b820c4a1400
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277610"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>模式中的實體角色為內容子類型
角色是實體的具名內容子類型，只能使用於[模式](luis-concept-patterns.md)中。

例如，在 `buy a ticket from New York to London` 語句中，紐約和倫敦都是城市，但是在句子中各有不同的意義。 紐約是出發城市，而輪多是目的地城市。 

角色會賦予這些差異一個名稱：

|實體|角色|目的|
|--|--|--|
|位置|來源|飛機起飛的位置|
|位置|目的地|飛機降落的位置|
|預先建置的 datetimeV2|to|結束日期|
|預先建置的 datetimeV2|from|開始日期|

## <a name="how-are-roles-used-in-patterns"></a>如何在模式中使用角色？
在模式的範本語句中，角色會使用於語句內： 

|模式與實體角色|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>模式中的角色語法
實體和角色都會以大括號 `{}` 括住。 實體與角色是以冒號隔開。 


[!INCLUDE[H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="roles-with-prebuilt-entities"></a>具有預先建置實體的角色

請使用具有預先建置實體的角色，為語句內預先建置實體的不同執行個體提供意義。 

### <a name="roles-with-datetimev2"></a>具有 datetimeV2 的角色

datetimeV2 預先建置實體在理解語句中日期與時間的各種不同變化方面，有很好的表現。 您可以使用與預先建置實體之預設理解方式不同的方式，指定日期與時間範圍。 

## <a name="next-steps"></a>後續步驟

* 了解如何新增[角色](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
