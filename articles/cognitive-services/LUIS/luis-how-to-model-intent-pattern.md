---
title: 模式新增精確度-LUIS
titleSuffix: Azure Cognitive Services
description: 新增模式範本，以改善 Language Understanding （LUIS）應用程式中的預測精確度。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 39b56c5e73c8ce85a020402dafb622b90c536a1e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143815"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何新增模式以改善預測精確度
LUIS 應用程式收到端點語句之後，請使用[模式](luis-concept-patterns.md)來改善以單字順序和字組選擇顯示模式之語句的預測精確度。 模式會使用特定的[語法](luis-concept-patterns.md#pattern-syntax)來指出的位置：[實體](luis-concept-entity-types.md)、實體[角色](luis-concept-roles.md)和選擇性文字。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-template-utterance-to-create-pattern"></a>將範本語句新增至建立模式

1. 在 [我的應用程式]頁面上選取您的應用程式名稱加以開啟，然後在 [改善應用程式效能] 之下，選取左面板中的 [模式]。

    > [!div class="mx-imgBorder"]
    > ![模式清單的螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. 選取模式的正確意圖。 

1. 在範本文字方塊中，輸入範本語句並選取 Enter。 當您想要輸入實體名稱時，請使用正確的模式實體語法。 實體語法的開頭為 `{`。 實體清單隨即顯示。 選取正確的實體。 

    > [!div class="mx-imgBorder"]
    > ![模式的實體螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    如果您的實體包含[角色](luis-concept-roles.md)，請在機構名稱後面指定具有單一冒號的角色 `:`，例如 `{Location:Origin}`。 實體的角色清單會顯示在清單中。 選取角色，然後選取 Enter。 

    > [!div class="mx-imgBorder"]
    > ![具有角色](./media/luis-how-to-model-intent-pattern/patterns-4.png) 的實體螢幕擷取畫面

    在您選取正確的實體之後，完成模式輸入，然後選取 Enter。 當您輸入好模式時，請[訓練](luis-how-to-train.md)您的應用程式。

    > [!div class="mx-imgBorder"]
    > ![輸入模式的螢幕擷取畫面，其中包含兩種類型的實體](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

## <a name="use-contextual-toolbar"></a>使用內容相關的工具列

模式清單上方的內容相關工具列可讓您：

* 搜尋模式
* 編輯模式
* 將個別模式重新指派至不同的意圖
* 將數個模式重新指派至不同的意圖
* Delete-a-單一模式
* 刪除數個模式
* 依實體篩選模式清單
* 篩選模式-依意圖列出
* 移除實體或意圖篩選條件
* 在意圖或實體頁面上從現有的語句新增模式

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用模式[建立模式](luis-tutorial-pattern.md)。任何和角色都有教學課程。
* 了解如何[訓練](luis-how-to-train.md)您的應用程式。
