---
title: 實體的角色
titleSuffix: Azure Cognitive Services
description: 角色是實體的具名內容子類型，只能使用於模式中。 例如，在 `buy a ticket from New York to London` 語句中，紐約和倫敦都是城市，但是在句子中各有不同的意義。 紐約是出發城市，而輪多是目的地城市。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: f5768012a03629190a65dd86d004dc842d99912e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215941"
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


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>實體的範例角色

角色只是實體在語句從內容中學習的放置方式。 當語句有一個以上的該實體類型時最有效率。 任何實體類型的最簡單範例就是要區別來源與目的地位置。 位置可以許多不同的實體類型表示。 

範例使用案例為將員工從某個部門調到另一個部門。而每個部門是清單中的一個項目。 例如︰ 

`Move [PersonName] from [Department:from] to [Department:to]` 。 

在傳回的預測中，兩個部門實體都會在 JSON 回應中傳回，且每個實體都包含角色名稱。 

## <a name="roles-with-prebuilt-entities"></a>具有預先建置實體的角色

請使用具有預先建置實體的角色，為語句內預先建置實體的不同執行個體提供意義。 

### <a name="roles-with-datetimev2"></a>具有 datetimeV2 的角色

datetimeV2 預先建置實體在理解語句中日期與時間的各種不同變化方面，有很好的表現。 您可以使用與預先建置實體之預設理解方式不同的方式，指定日期與時間範圍。 

## <a name="next-steps"></a>後續步驟

* 了解如何新增[角色](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
