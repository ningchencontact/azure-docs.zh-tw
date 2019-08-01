---
title: 最佳做法-LUIS
titleSuffix: Azure Cognitive Services
description: 了解從 LUIS 應用程式的模型獲得最佳結果的 LUIS 最佳做法。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 7a2802bd4daa1a009c610688120c9a56583b054f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639284"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>使用認知服務建置語言理解應用程式的最佳做法
使用應用程式撰寫流程來建立 LUIS 應用程式: 

* 建置語言模型
* 新增一些定型範例語句 (每一意圖 10-15 個)
* 發行 
* 從端點測試 
* 新增功能

一旦您的應用程式[發佈](luis-how-to-publish-app.md)之後, 請使用撰寫迴圈來新增功能、發佈, 並從端點進行測試。 請勿以新增更多範例語句的方式來開始下一個撰寫循環。 這並不會讓 LUIS 藉由真實世界使用者語句學習您的模型。 

為了讓 LUIS 能有效率地進行學習，請勿擴充語句，直到目前這組範例和端點語句傳回確信的高預測分數為止。 請使用[主動式學習](luis-concept-review-endpoint-utterances.md)、[模式](luis-concept-patterns.md)及[片語清單](luis-concept-feature.md)來提升分數。 

## <a name="do-and-dont"></a>建議事項和避免事項
以下清單包含 LUIS 應用程式的最佳做法：

|建議事項|避免事項|
|--|--|
|[定義不同的意圖](#do-define-distinct-intents) |[將許多範例語句新增至意圖](#dont-add-many-example-utterances-to-intents) |
|[找出每個意圖的太攏統與太特定之間的最佳點](#do-find-sweet-spot-for-intents)|[使用 LUIS 作為定型平台](#dont-use-luis-as-a-training-platform)|
|[以反覆方式建置您的應用程式](#do-build-the-app-iteratively)|[新增許多相同格式的範例語句而忽略其他格式](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[在稍後的反覆項目中新增片語清單和模式](#do-add-phrase-lists-and-patterns-in-later-iterations)|[混合意圖和實體的定義](#dont-mix-the-definition-of-intents-and-entities)|
|[讓您的語句在所有意圖之間達到平衡](#balance-your-utterances-across-all-intents)，除了 None 意圖之外。<br>[將範例語句新增至 None 意圖](#do-add-example-utterances-to-none-intent)|[建立含有所有可能值的片語清單](#dont-create-phrase-lists-with-all-the-possible-values)|
|[利用主動式學習的建議功能](#do-leverage-the-suggest-feature-for-active-learning)|[新增太多模式](#dont-add-many-patterns)|
|[監視您應用程式的效能](#do-monitor-the-performance-of-your-app)|[新增每一個範例語句都進行定型和發佈](#dont-train-and-publish-with-every-single-example-utterance)|
|[針對每個應用程式反覆項目使用版本](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>請務必定義不同的意圖
請確定每個意圖的詞彙都僅適用於該意圖，而未與不同的意圖重疊。 例如，如果您想要有一個處理旅遊安排 (例如航班和飯店) 的應用程式，則可以選擇讓這些主體區域成為語句內具有特定資料實體的個別意圖或相同意圖。

如果兩個意圖之間的詞彙相同，請將意圖結合，然後使用實體。 

請思考一下以下的範例語句：

|範例語句|
|--|
|預訂班機|
|預訂飯店|

"Book a flight" 和 "Book a hotel" 使用相同的 "book a "詞彙。 此格式相同，因此它應該是相同意圖，但是以班機和飯店這兩個不同的字組作為擷取的實體。 

其他資訊：
* 概念：[在您 LUIS 應用程式中關於意圖的概念](luis-concept-intent.md)
* 教學課程：[建置 LUIS 應用程式來判斷使用者的用意](luis-quickstart-intents-only.md)
* HOW TO：[新增意圖以判斷語句的使用者用意](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>請務必找出意圖的最佳點
請使用來自 LUIS 的預測資料來判斷您的意圖是否重疊。 重疊的意圖會混淆 LUIS。 結果會造成最高分的意圖太接近另一個意圖。 由於 LUIS 不會每次都使用完全相同的資料路徑來進行定型，因此重疊的意圖有可能在定型中成為第一或第二。 您會希望每個意圖的語句分數都儘量拉開，以便避免發生這類不確定情況。 良好的意圖區別應該每次都產生預期的最高分意圖。 
 
## <a name="do-build-the-app-iteratively"></a>請務必以反覆方式建置應用程式
保留一組未用來作為[範例語句](luis-concept-utterance.md)或端點語句的個別語句。 持續針對測試集改善應用程式。 調適測試集以反映真實的使用者語句。 使用此測試集來評估應用程式的每個反覆項目或版本。 

開發人員應該有三組資料。 第一組是用來建置模型的範例語句。 第二組用來在端點測試模型。 第三組是在[批次測試](luis-how-to-batch-test.md)中使用的盲性測試資料。 這最後一組既不用來進行應用程式定型，也不會在端點上傳送。  

其他資訊：
* 概念：[撰寫 LUIS 應用程式的循環](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>請務必在稍後的反覆項目中新增片語清單和模式

最佳做法是在您的應用程式進行測試之前，請勿套用這些做法。 在新增[片語清單](luis-concept-feature.md)和[模式](luis-concept-patterns.md)之前, 您應該先瞭解應用程式的行為, 因為這些功能的加權高於範例語句, 而且會扭曲信賴度。 

一旦您了解應用程式缺乏上述功能時的運作方式之後，新增每個適用於您應用程式的功能。 您不需要在每次[反覆運算](luis-concept-app-iteration.md)時新增這些功能，也不需要為每個版本變更功能。 

在您的模型設計一開始新增它們並無任何壞處，但是，透過表達測試模型之後，則可更輕鬆地查看每個功能變更結果的方式。 

最佳做法是透過[端點](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance)進行測試，以便取得[主動式學習](luis-concept-review-endpoint-utterances.md)的額外好處。 [互動式測試窗格](luis-interactive-test.md)也是一種有效的測試方法。 
 

### <a name="phrase-lists"></a>片語清單

[片語清單](luis-concept-feature.md)可讓您定義與應用程式定義域相關的單字字典。 請在您的片語清單中植入一些單字，然後使用建議功能，讓 LUIS 知道您專屬詞彙中的更多單字。 片語清單可透過提升與應用程式重要的單字或片語相關聯的訊號，來改善意圖偵測及實體分類。 

請勿將每個單字都新增到詞彙中，因為片語清單並非完全相符項目。 

其他資訊：
* 概念：[LUIS 應用程式中的片語清單功能](luis-concept-feature.md)
* 操作說明：[使用片語清單來提升字組清單訊號](luis-how-to-add-features.md)

### <a name="patterns"></a>模式

來自端點的真實使用者語句彼此非常相似，可能顯露單字選擇和位置的模式。 [模式](luis-concept-patterns.md)功能會搭配規則運算式採用此單字選擇和位置，以改善預測準確性。 模式中的規則運算式可考量您打算忽略的單字和標點符號，同時仍可比對模式。 

請針對標點符號使用模式的[選擇性語法](luis-concept-patterns.md)，以便忽略標點符號。 請使用[明確清單](luis-concept-patterns.md#explicit-lists)來彌補 pattern.any 的語法問題。 

其他資訊：
* 概念：[模式可改善預測精確度](luis-concept-patterns.md)
* 操作說明：[如何新增模式以改善預測準確度](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>讓您的語句在所有意圖之間達到平衡

為了讓 LUIS 預測能正確，每個意圖的範例語句數量必須相對相等 (None 意圖除外)。 

如果您有一個包含 100 個範例語句的意圖和一個包含 20 個範例語句的意圖，100 個語句的意圖會有較高的預測評等。  

## <a name="do-add-example-utterances-to-none-intent"></a>請務必將範例語句新增至 None 意圖

此意圖是後援意圖，應用程式外的所有項目都以此表示。 請在您 LUIS 應用程式的其餘部分，每 10 個範例語句中，便將一個範例語句新增至 None 意圖。

其他資訊：
* 概念：[了解適合您 LUIS 應用程式的語句](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>請務必利用主動式學習的建議功能

請定期使用[主動式學習](luis-how-to-review-endpoint-utterances.md)的**檢閱端點語句**，而不要將更多範例語句新增至意圖。 由於應用程式會不斷接收端點語句，因此這份清單會不斷成長並變更。

其他資訊：
* 概念：[檢閱端點語句以啟用主動式學習的概念](luis-concept-review-endpoint-utterances.md)
* 教學課程：[教學課程：檢閱端點語句以修正不確定的預測](luis-tutorial-review-endpoint-utterances.md)
* 操作說明：[如何在 LUIS 入口網站中檢閱端點語句](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>請務必監視您應用程式的效能

請使用[批次測試](luis-concept-batch-test.md)集合來監視預測準確性。 

## <a name="dont-add-many-example-utterances-to-intents"></a>請勿將許多範例語句新增至意圖

發佈應用程式之後，請只從反覆程序中的主動式學習新增語句。 如果語句太類似，則請新增模式。 

## <a name="dont-use-luis-as-a-training-platform"></a>請勿使用 LUIS 作為定型平台

LUIS 是語言模型定義域特定的。 不是用來作為一般的自然語言訓練平台使用。 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>請勿新增許多相同格式的範例語句而忽略其他格式

LUIS 會預期意圖的語句中有所變化。 語句可以在改變的同時仍保有相同的整體意義。 變化可以包括語句長度、單字選擇，以及單字位置。 

|請勿使用相同的格式|請務必使用變化格式|
|--|--|
|購買一張到西雅圖的機票<br>購買一張到巴黎的機票<br>購買一張到奧蘭多的機票|購買 1 張到西雅圖的機票<br>預約兩個下週一到巴黎的紅眼航班機位<br>我想要預訂 3 張到奧蘭多的春假機票|

第二欄使用不同的動詞 (購買、預約、預訂)、不同的量詞 (1、兩、3) 及不同的單字排列，但全都具有購買旅遊機票的相同意圖。 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>請勿混合意圖和實體的定義

請針對您 Bot 將執行的任何動作建立意圖。 使用實體作為促成該動作的參數。 

針對將預訂航班的 Chatbot，請建立 **BookFlight** 意圖。 請勿為每個航空公司或每個目的地都建立意圖。 請使用這些資料片段作為[實體](luis-concept-entity-types.md)，然後在範例語句中標示它們。 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>請勿建立含有所有可能值的片語清單

請在[片語清單](luis-concept-feature.md)中提供一些範例，但不要提供每個單字。 LUIS 會將內容一般化並納入考量。 

## <a name="dont-add-many-patterns"></a>請勿新增許多模式

請勿新增太多[模式](luis-concept-patterns.md)。 LUIS 旨在透過更少的範例快速學習。 請勿不必要地讓系統多載。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>請勿對每一個範例語句都進行定型和發佈

請新增 10 或 15 個語句之後，再進行定型和發佈。 這將可讓您了解對預測準確性的影響。 新增單一語句對分數可能不會有明顯的影響。 

## <a name="do-use-versions-for-each-app-iteration"></a>針對每個應用程式反覆項目使用版本

每個撰寫循環都應該在新[版本](luis-concept-version.md) (從現有版本複製) 之內。 LUIS 沒有版本限制。 版本名稱會用來作為 API 路由的一部份，因此請務必挑選可在 URL 中使用的字元，以及每個版本的名稱需保持在 10 個字元數以內。 開發版本名稱策略可讓您規劃版本編排。 

其他資訊：
* 概念：[了解如何及何時使用 LUIS 版本](luis-concept-version.md)
* 操作說明：[使用版本進行編輯和測試而不影響預備或生產應用程式](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>後續步驟

* 了解如何在 LUIS 應用程式中[規劃您的應用程式](luis-how-plan-your-app.md)。
