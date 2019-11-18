---
title: 描述元-LUIS
titleSuffix: Azure Cognitive Services
description: 使用 Language Understanding (LUIS) 來新增應用程式功能，可改善對類別和模式進行意圖和實體的偵測或預測
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123140"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>使用描述項來提升單字清單的信號

您可以將功能新增到 LUIS 應用程式來改善它的準確性。 這些功能協助 LUIS 的方式是提供某些字組和片語屬於某個應用程式網域詞彙的提示。 

[描述](luis-concept-feature.md)項（片語清單）包含一組屬於相同類別的值（單字或片語），而且必須以類似的方式處理（例如，城市或產品的名稱）。 LUIS 對於其中一個值的認識也會自動套用到其他值。 這份清單與相符單字的[清單實體](reference-entity-list.md)（全文相符專案）不相同。

描述項會將新增至應用程式域的詞彙，做為 LUIS 有關這些單字的第二個信號。

請參閱[功能概念](luis-concept-feature.md)，以瞭解使用描述項的時機和原因。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>新增描述項

1. 在**我的應用程式** 頁面上按一下應用程式的名稱，然後按一下 **組建**，再按一下應用程式左側面板中的 **描述**。 

1. 在 [**描述**項] 頁面上，按一下 [ **+ 新增描述**元]。 
 
1. 在 [**建立新的片語清單描述**元] 對話方塊中，輸入描述項的名稱，例如 `Cities`。 在 [**值**] 方塊中，輸入描述項的值，例如 `Seattle`。 您可以一次輸入一個值，或輸入一組以逗號分隔的值，然後按 **Enter**。

    > [!div class="mx-imgBorder"]
    > ![新增描述元城市](./media/luis-add-features/add-phrase-list-cities.png)

    一旦您輸入足夠的 LUIS 值，就會出現建議。 您可以 **+ 新增所有**提議的值，或選取個別的詞彙。

1. 如果新增的描述元值是可以交換使用的替代專案，請將**這些值保留為可互換**。

1. 選取 [完成]。 新的描述項會加入至 [**描述**項] 頁面。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 您可以從 [**描述**項] 頁面上的內容相關工具列中刪除或停用描述元。

## <a name="next-steps"></a>後續步驟

新增、編輯、刪除或停用描述元之後，請再次[訓練並測試應用程式](luis-interactive-test.md)，以查看效能是否改善。
