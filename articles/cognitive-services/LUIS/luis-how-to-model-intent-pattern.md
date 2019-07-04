---
title: 新增精確度的模式
titleSuffix: Language Understanding - Azure Cognitive Services
description: 加入模式以提高預測準確度，Language Understanding (LUIS) 應用程式中的範本。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60196636"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何新增模式，以提高預測準確度
LUIS 應用程式接收端點表達方式之後，請使用[模式](luis-concept-patterns.md)以提高預測準確度的顯示模式，以文字順序和 word 選擇的發音。 模式使用特定[語法](luis-concept-patterns.md#pattern-syntax)若要指出的位置：[實體](luis-concept-entity-types.md)，實體[角色](luis-concept-roles.md)，及選擇性的文字。

## <a name="add-template-utterance-to-create-pattern"></a>將範本語句新增至建立模式
1. 在 [我的應用程式]  頁面上選取您的應用程式名稱加以開啟，然後在 [改善應用程式效能]  之下，選取左面板中的 [模式]  。

    ![模式清單的螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. 選取模式的正確意圖。 

    ![選取意圖](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. 在範本文字方塊中，輸入範本語句並選取 Enter。 當您想要輸入實體名稱時，請使用正確的模式實體語法。 實體語法的開頭為 `{`。 實體清單隨即顯示。 選取正確的實體，然後選取 Enter。 

    ![模式的實體螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    如果您的實體包含[角色](luis-concept-roles.md)，表示單一的冒號，角色`:`之後的實體名稱，例如`{Location:Origin}`。 實體的角色清單會顯示在清單中。 選取角色，然後選取 Enter。 

    ![實體與角色的螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    在您選取正確的實體之後，完成模式輸入，然後選取 Enter。 當您輸入好模式時，請[訓練](luis-how-to-train.md)您的應用程式。

    ![包含兩種實體類型的已輸入模式螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>在利用模式變更模型後訓練您的應用程式
在您新增、編輯、移除或重新指派模式之後，[訓練](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)您的應用程式，您的變更才會影響端點查詢。 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>使用 [內容] 工具列

模式清單上方的 [內容] 工具列可讓您：

* 搜尋模式
* 編輯模式
* 將個別模式重新指派至不同的意圖
* 將數個模式重新指派至不同的意圖
* 刪除的單一模式
* 刪除數個模式
* 依實體篩選模式清單
* Filter-pattern-list-by-intent
* 移除實體或意圖篩選條件
* 在意圖或實體頁面上從現有的語句新增模式

## <a name="next-steps"></a>後續步驟

* 了解如何[建置的模式](luis-tutorial-pattern.md)pattern.any 與教學課程的角色。
* 了解如何[訓練](luis-how-to-train.md)您的應用程式。
