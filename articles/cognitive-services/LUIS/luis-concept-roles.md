---
title: 理解如何在以模式為基礎的實體中使用角色 - Azure | Microsoft Docs
description: 了解如何在以模式為基礎的實體中使用角色，為內容實體子類型提供名稱。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35371055"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>模式中的實體角色為內容子類型
角色是實體的具名內容子類型，只能使用於[模式](luis-concept-patterns.md)中。

例如，在 `buy a ticket from New York to London` 語句中，紐約和倫敦都是城市，但是在句子中各有不同的意義。 紐約是出發城市，而輪多是目的地城市。 

角色會賦予這些差異一個名稱：

|實體|角色|目的|
|--|--|--|
|位置|來源|飛機起飛的位置|
|位置|目的地|飛機降落的位置|

## <a name="how-are-roles-used-in-patterns"></a>如何在模式中使用角色？
在模式的範本語句中，角色會使用於語句內： 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>模式中的角色語法
實體和角色都會以大括號 `{}` 括住。 實體與角色是以冒號隔開。 

## <a name="roles-versus-hierarchical-entities"></a>角色與階層式實體
階層式實體會提供與角色相同的內容資訊，但僅限於**意圖**中的語句。 同樣地，角色會提供與階層式實體相同的內容資訊，但僅限於**模式**。

|內容學習|使用於|
|--|--|
|階層式實體|意圖|
|角色|模式|

## <a name="next-steps"></a>後續步驟

* 了解如何新增[角色](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
