---
title: 實體的角色-LUIS
titleSuffix: Azure Cognitive Services
description: 角色是實體的具名內容子類型，只能使用於模式中。 例如，在 `buy a ticket from New York to London` 語句中，紐約和倫敦都是城市，但是在句子中各有不同的意義。 紐約是出發城市，而輪多是目的地城市。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b4bd61ea74055a04718d8a9d8d5ccd42671af2ac
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638343"
---
# <a name="entity-roles-for-contextual-subtypes"></a>內容子類型的實體角色

角色可讓實體具有命名子類型。 角色可以與任何預先建置或自訂實體型別一起使用，並在範例表達和模式中使用。 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>機器學習的角色實體範例

在「從**紐約**到**倫敦**購買票證」語句中, 紐約和倫敦都是城市, 但是在句子中各有不同的意義。 紐約是出發城市，而輪多是目的地城市。 

```
buy a ticket from New York to London
```

角色會賦予這些差異一個名稱：

|實體類型|實體名稱|Role|用途|
|--|--|--|--|
|簡單|Location|來源|飛機起飛的位置|
|簡單|Location|destination|飛機降落的位置|

## <a name="non-machine-learned-entity-example-of-roles"></a>非機器學習的角色實體範例

在 [排程從8到9的會議] 語句中, 這兩個數字都表示一個時間, 但每次在語句中有不同的意義。 角色會提供差異的名稱。 

```
Schedule the meeting from 8 to 9
```

|實體類型|角色名稱|值|
|--|--|--|
|預先建置的 datetimeV2|時間|8|
|預先建置的 datetimeV2|Endtime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>語句中的多個實體與角色相同嗎？ 

語句中可以有多個實體, 而且不需要使用角色就可以進行解壓縮。 如果句子的內容指出實體的哪個版本具有值, 則應該使用角色。 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>請勿使用角色進行重複, 而不需要意義

如果語句包含位置的清單, `I want to travel to Seattle, Cairo, and London.`這就是一個清單, 其中的每個專案都沒有其他意義。 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>如果重複表示意義, 請使用角色

如果語句包含具有意義`I want to travel from Seattle, with a layover in Londen, landing in Cairo.`的位置清單, 則應該使用角色來捕捉來源、站大廈和目的地這種意義。

### <a name="roles-can-indicate-order"></a>角色可以表示訂單

如果語句變更為指出您想要解壓縮的順序, `I want to first start with Seattle, second London, then third Cairo`您可以用幾種方式來解壓縮。 您可以標記指出角色、 `first start with` `second`、、 `third`的權杖。 您也可以使用預先建立的實體**序數**和複合實體中的**GeographyV2**預建實體, 來捕捉順序和位置的概念。 

## <a name="how-are-roles-used-in-example-utterances"></a>範例語句中使用的角色為何？

當實體具有角色, 且實體在範例語句中標示時, 您可以選擇只選取實體, 或選取實體和角色。 

下列範例語句使用實體和角色:

|Token 視圖|實體視圖|
|--|--|
|我有興趣深入瞭解**西雅圖**|我有興趣深入瞭解 {Location}|
|從西雅圖購買機票至紐約|從 {Location: 原點} 購買票證到 {Location: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>如何在模式中使用角色？
在模式的範本語句中，角色會使用於語句內： 

|模式與實體角色|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>模式中的角色語法
實體和角色都會以大括號 `{}` 括住。 實體與角色是以冒號隔開。 

## <a name="entity-roles-versus-collaborator-roles"></a>實體角色與共同作業者角色

實體角色適用于 LUIS 應用程式的資料模型。 [共同作業者](luis-concept-collaborator.md)角色適用于撰寫存取層級。 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>後續步驟

* 使用[實際操作教學](tutorial-entity-roles.md)課程搭配非機器學習的實體來使用實體角色
* 了解如何新增[角色](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
