---
title: 儀表板-Language Understanding
titleSuffix: Azure Cognitive Services
description: 修正 「 意圖 」 分析儀表板，視覺化的報告工具。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: 055d113a2bc77f8de1b4b881718007c869470532
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236952"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>如何使用儀表板，以改善您的應用程式

尋找和修正定型應用程式的意圖的問題，當您使用範例的發音。 儀表板會顯示整體的應用程式資訊，以反白顯示的對應方式，必須先解決。 

檢閱儀表板分析是反覆的程序，一再重複，您變更，並改善您的模型。

此頁面不會有相關的分析之應用程式的意圖，稱為中沒有任何範例談話_模式僅限_應用程式。 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>從儀表板可以修正什麼問題嗎？

三個儀表板中已解決的問題是：

|問題|圖表色彩|說明|
|--|--|--|
|資料不平衡|-|會發生這種情況時大幅範例談話數量而有所不同。 所有的對應方式必須能夠_大約_相同數目的範例談話-無的意圖 除外。 它應該只有 10%-15%的總數量的表達方式在應用程式。<br><br> 如果資料不平衡，但意圖精確度高於某個臨界值，此不平衡並不會報告為問題。<br><br>**開始本文承蒙-它可能是其他問題的根本原因。**|
|不清楚的預測|橘色|這發生前的意圖和下一步 的意圖分數時關閉它們可能翻轉的下一步 的訓練課程，因為[negative 取樣](luis-how-to-train.md#train-with-all-data)或加入意圖的詳細範例談話。 |
|不正確的預測|紅色|會發生這種情況是當範例 [utterance] 不會加上標籤的意圖 （在意圖） 的預測。|

正確的預測是以藍色表示。

儀表板會顯示這些問題並告訴您哪一個意圖會受到影響，並建議您應該如何改善應用程式。 

## <a name="before-app-is-trained"></a>應用程式在定型之前 

訓練應用程式之前，請在儀表板不包含任何建議的修正程式。 訓練您的應用程式，以查看這些建議。  

## <a name="check-your-publishing-status"></a>檢查您的發佈狀態

**發佈狀態**卡片包含作用中的相關資訊的最後發行版本。 

請檢查使用中的版本是您想要修正的版本。 

![儀表板會顯示應用程式的外部服務，發行區域，並彙總端點叫用。](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

這也會顯示任何外部服務]、 [已發佈的區域，並彙總端點叫用。 

## <a name="review-training-evaluation"></a>檢閱訓練評估

**定型評估**卡片包含您的應用程式的整體精確度依區域列出的彙總的摘要。 分數表示意圖的品質。 

![訓練評估卡片包含的第一個區域，您的應用程式的整體精確度的相關資訊。](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

圖表會指出正確預測的意圖和問題區域，以不同的色彩。 因為您改善應用程式與建議，此分數會增加。 

建議的修正的問題類型會被分，是最重要的應用程式。 如果您想要檢閱並修正問題，每個使用的意圖 **[發生錯誤的意圖](#intents-with-errors)** 卡片底部的頁面。 

每個問題 區域則需要修正的意圖。 當您選取的意圖的名稱，**意圖**頁面開啟時套用至的發音的篩選。 此篩選器可讓您專注於造成此問題的發音。

### <a name="compare-changes-across-versions"></a>比較不同版本之間的變更

應用程式進行變更之前建立的新版本。 在新的版本，建議變更的意圖範例談話，然後重新定型。 在 [儀表板] 頁面**訓練評估**卡片上，使用**從定型版本顯示變更**來比較所做的變更。 

![比較不同版本之間的變更](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>藉由新增或編輯範例表達方式重新定型修正版本

修正您的應用程式的主要方法是新增或編輯範例談話並重新定型。 新增或變更談話要遵循的指導方針[發音各不相同](luis-concept-utterance.md)。

加入範例談話應該完成的其他人人員：

* 具有較高程度的了解的表達方式是在不同的用途
* 知道如何在一個意圖的表達方式可能會不清楚與另一個意圖
* 能夠決定是否兩個的意圖，經常會與彼此混淆，應會摺疊成單一的意圖，以及不同的資料提取的實體

### <a name="patterns-and-phrase-lists"></a>模式和片語清單

[分析] 頁面未指出使用的時機[模式](luis-concept-patterns.md)或是[片語清單](luis-concept-feature.md)。 如果您需要新增它們，它可以協助使用不正確或不清楚的預測，但不會協助進行資料不平衡。 

### <a name="review-data-imbalance"></a>檢閱資料不平衡

開始本文承蒙-它可能是其他問題的根本原因。

**的資料不平衡**意圖清單會顯示需要更多的談話，若要更正的資料不平衡的狀態的對應方式。 

**若要修正此問題**:

* 新增更多的表達方式與意圖，然後重新定型。 

除非所建議的儀表板上，請勿為 None 的意圖新增表達方式。

> [!Tip]
> 在頁面上，使用第三個區段**發音意圖每**使用**談話 （數字）** 設定，其中對應方式需要更多的表達方式的快速視覺輔助線為。  
    ![您可以使用 '談話 （數字）'，找出與資料不平衡的意圖。](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>檢閱不正確的預測

**不正確的預測**意圖清單會顯示已做為範例針對特定的意圖，但針對不同的用途預測的發音的意圖。 

**若要修正此問題**:

* 編輯為更特定的意圖和訓練一次的談話。
* 如果談話太接近對齊，並重新定型，結合 「 意圖 」。

### <a name="review-unclear-predictions"></a>檢閱不清楚的預測

**清楚預測**意圖清單會顯示與使用預測分數不是從其最接近的競爭對手，[utterance] 的最上層的意圖可能變更的下一步的訓練課程，因為夠遠方式的發音的意圖[negative 取樣](luis-how-to-train.md#train-with-all-data)。

**若要修正此問題**;

* 編輯為更特定的意圖和訓練一次的談話。
* 如果談話太接近對齊，並重新定型，結合 「 意圖 」。

## <a name="utterances-per-intent"></a>每個意圖的表達方式

這張卡片會顯示整體的應用程式健全狀況之間的對應方式。 當您修正意圖和重新定型，繼續來看看下列這張卡的問題。

下圖顯示良好平衡的應用程式，幾乎沒有修正問題。

![下圖顯示良好平衡的應用程式，幾乎沒有修正問題。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

下圖顯示不平衡的應用程式，具有許多修正的問題。

![下圖顯示良好平衡的應用程式，幾乎沒有修正問題。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

暫留以取得項目的相關資訊的每個目的列。 

![下圖顯示良好平衡的應用程式，幾乎沒有修正問題。](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

使用**排序**來排列根據問題類型的對應方式，讓您可以專注於最有問題的對應方式，與該問題的功能。 

## <a name="intents-with-errors"></a>發生錯誤的意圖

這張卡片可讓您檢閱問題以取得特定的意圖。 這張卡片的預設檢視是最有問題的意圖，因此您知道哪裡可以將心力。

![這些意圖具有錯誤卡可讓您檢閱問題以取得特定的意圖。 卡片會篩選最有問題的意圖，根據預設，讓您知道哪裡可以將心力。](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

最上層的環圈圖會顯示目的的問題，跨三個問題類型。 如果在三種問題類型有問題，每個類型都以下，自己圖表，以及任何競爭對手的意圖。 

### <a name="filter-intents-by-issue-and-percentage"></a>篩選的問題和百分比的意圖

卡片的這個區段可讓您尋找正在下降超出錯誤閾值的範例談話。 在理想情況下，您會想重大正確的預測。 百分比是業務和客戶的需求。 

決定您想要透過適合您企業的臨界值百分比。 

篩選條件可讓您尋找特定問題的意圖：

|Filter|建議的百分比|目的|
|--|--|--|
|最有問題的意圖|-|**從這裡開始**-修正談話中此意圖將可改善應用程式，多個其他修正程式。|
|以下的正確預測|60%|這是所選的意圖正確無誤，但信心分數低於臨界值的發音的百分比。 |
|上述的清楚預測|15%|這是在選取的對應方式與最接近的競爭對手意圖會混淆的發音的百分比。|
|上述的不正確預測|15%|這是所選的用意在不正確預測的發音的百分比。 |

### <a name="correct-prediction-threshold"></a>正確預測的臨界值

您有信心的預測信心分數是什麼？ 在開始開發應用程式時，60%可能會將您的目標。 使用**更正預測下**包含選取需要修正的用意在尋找任何談話的 60%的百分比。

### <a name="unclear-or-incorrect-prediction-threshold"></a>不明確或不正確預測的臨界值

這兩個篩選可讓您找到所選的意圖，超出臨界值的談話。 您可以將這些兩個百分比的錯誤百分比。 如果您熟悉預測 10-15%錯誤率，設定篩選的臨界值為 15%，以尋找所有的談話，高於此值。 

## <a name="next-steps"></a>後續步驟

* [管理 Azure 資源](luis-how-to-azure-subscription.md)
