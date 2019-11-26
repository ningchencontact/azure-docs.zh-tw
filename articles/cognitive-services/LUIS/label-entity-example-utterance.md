---
title: 標記實體範例語句
titleSuffix: Azure Cognitive Services
description: 了解如何在 LUIS 入口網站的意圖詳細資料頁面中，以範例語句中的子元件標記機器學習實體。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134201"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>在範例語句中標記機器學習實體

在範例語句中標記實體，會顯示 LUIS 具有實體的範例，以及實體可在語句中出現的位置。 

## <a name="labeling-machine-learned-entity"></a>標記機器學習實體

請考量片語 `hi, please I want a cheese pizza in 20 minutes`。 

1. 選取最左邊的文字，然後選取實體的最右邊文字。 _完成順序_會在下圖中標示。

    > [!div class="mx-imgBorder"]
    > ![標記完整機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. 從快顯視窗中選取實體。 標記的完整披薩訂單實體包含標示的所有單字 (由左到右的英文)。 

> [!TIP]
> 快顯視窗中可用的實體會相對於出現文字的內容。 例如，如果您有 5 層級機器學習實體，而且您選取的是第 3 層的文字 (在範例語句底下由標記實體名稱表示)，則快顯視窗中可用的實體會限制為第 3 層的子元件內容 (第 4 層子元件)。 

## <a name="review-labeled-text"></a>檢閱標記文字

標記之後，請檢閱範例語句。 LUIS 會在標記之後，將目前的模型套用至範例語句。 實線表示文字已標記。 

> [!div class="mx-imgBorder"]
> ![標記完整機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>定型時機

如果目前的模型應該支援您的標記實體，但是範例語句繼續將文字顯示為已預測但未標記，請將您的應用程式定型。  

## <a name="confirm-predicted-entity"></a>確認預測實體

如果視覺指標高於語句，表示文字已預測，但是_尚未標記_。 若要將預測轉換為標記，請選取語句，然後選取 [確認實體預測]  。

> [!div class="mx-imgBorder"]
> ![預測完整機器學習實體](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>藉由使用實體調色盤資料指標進行繪製，來標記子元件實體

1. 若要更正預測 (出現在範例語句上方的實體)，請開啟實體調色盤。 

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色盤](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. 選取實體子元件。 此動作會以視覺化方式顯示新的資料指標。 當您在入口網站中移動時，資料指標會跟隨滑鼠。 

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色盤](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. 在範例語句中，使用資料指標_繪製_實體。 

    > [!div class="mx-imgBorder"]
    > ![機器學習實體的實體調色盤](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>將相符文字實體標記為機器學習實體

相符文字實體包括預先建置的實體、規則運算式實體和清單實體。 當您建立或編輯機器學習實體時，您可以將這些實體新增至機器學習實體，作為子元件的條件約束。 

**新增這些條件約束之後，您就不需要在範例語句中標記相符文字。**

## <a name="entity-prediction-errors"></a>實體預測錯誤

實體預測錯誤會顯示警告指示器。 這表示預測的實體不符合標記的實體。 

> [!div class="mx-imgBorder"]
> ![機器學習實體的實體調色盤](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>後續步驟

使用[儀表板](luis-how-to-use-dashboard.md)並且[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)，來改善應用程式的預測品質。
