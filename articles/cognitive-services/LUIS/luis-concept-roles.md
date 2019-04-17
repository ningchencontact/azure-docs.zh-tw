---
title: 實體的角色
titleSuffix: Azure Cognitive Services
description: 角色是實體的具名內容子類型，只能使用於模式中。 例如，在 `buy a ticket from New York to London` 語句中，紐約和倫敦都是城市，但是在句子中各有不同的意義。 紐約是出發城市，而輪多是目的地城市。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 5fa922cb91d34483256faf4dcf70569aa2f17b97
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522481"
---
# <a name="entity-roles-for-contextual-subtypes"></a>實體內容的子類型的角色

角色可讓實體必須有具名的子類型。 角色可以搭配任何預先建置或自訂實體型別，並用於範例的發音和模式。 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>角色的機器學習的實體範例

在 [utterance] 」 購買票證**紐約**要**倫敦**、 紐約和倫敦是城市，但每個句子中有不同的意義。 紐約是出發城市，而輪多是目的地城市。 

```
buy a ticket from New York to London
```

角色會賦予這些差異一個名稱：

|實體類型|實體名稱|角色|目的|
|--|--|--|--|
|簡單|位置|來源|飛機起飛的位置|
|簡單|位置|目的地|飛機降落的位置|

## <a name="non-machine-learned-entity-example-of-roles"></a>非機器學習的實體範例中的角色

在 [utterance] 「 排程 8 到 9 的會議 」，這兩個的數字表示的時間，但每次在 [utterance] 中有不同的意義。 角色提供名稱的差異。 

```
Schedule the meeting from 8 to 9
```

|實體類型|角色名稱|值|
|--|--|--|
|預先建置的 datetimeV2|starttime|8|
|預先建置的 datetimeV2|結束時間|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>會在 [utterance] 中的多個實體與角色相同的工作嗎？ 

多個實體可以存在於 [utterance]，並可以不使用角色中擷取。 如果表示句子的內容之實體的版本具有的值，，則應該使用的角色。 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>不使用角色來進行沒有意義的重複項目

如果 [utterance] 包含一份位置`I want to travel to Seattle, Cairo, and London.`，這是一個清單，每個項目沒有額外的意義。 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>如果重複項目表示的意義，請使用角色

如果 [utterance] 包含一份有意義的位置`I want to travel from Seattle, with a layover in Londen, landing in Cairo.`，應該擷取來源、 中途與目的地的這個意義與角色。

### <a name="roles-can-indicate-order"></a>角色可以表示順序

如果 [utterance] 變更為指出您想要擷取的順序`I want to first start with Seattle, second London, then third Cairo`，您可以擷取數種方式。 您可以標記表示角色時，語彙基元`first start with`， `second`， `third`。 您也可以使用預先建置的實體**序數**並**GeographyV2**預先建置的實體，在複合實體中，擷取順序和位置的概念。 

## <a name="how-are-roles-used-in-example-utterances"></a>角色中的使用方式範例的發音？

當實體有一個角色，並將此實體標記中範例 [utterance] 時，您可以選擇選取只是實體，或選取的實體和角色。 

下列範例中表達方式使用實體和角色：

|語彙基元的檢視|實體的檢視|
|--|--|
|我要興趣進一步了解**西雅圖**|我想要深入了解 {Location}|
|來自西雅圖的票證購買紐約|購買票證 {位置： 原始} 到 {位置： 目的地}|

## <a name="how-are-roles-related-to-hierarchical-entities"></a>階層式實體關聯角色的方式？

角色現在可供範例談話，以及先前使用的模式中的所有實體。 它們可用的所有位置，因為它們會取代階層式實體的需求。 應該與角色，而不是使用階層式實體建立新的實體。 

階層式實體最終會被取代。

## <a name="how-are-roles-used-in-patterns"></a>如何在模式中使用角色？
在模式的範本語句中，角色會使用於語句內： 

|模式與實體角色|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>模式中的角色語法
實體和角色都會以大括號 `{}` 括住。 實體與角色是以冒號隔開。 

## <a name="entity-roles-versus-collaborator-roles"></a>實體與共同作業者角色的角色

實體的角色會套用至 LUIS 應用程式的資料模型。 [共同作業者](luis-concept-collaborator.md)角色適用於撰寫存取層級。 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>後續步驟

* 使用[實際操作教學課程](tutorial-entity-roles.md)與非機器學習的實體使用實體的角色
* 了解如何新增[角色](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
