---
title: 實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: '實體會從語句中解壓縮資料。 實體類型可讓您進行可預測的資料提取。 實體有兩種類型: 機器學習和非機器學習。 請務必知道您在語句中使用哪一種類型的實體。'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 9919b6d07e874bd306bdba9da2cd3357bedc48f0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563999"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>實體類型和其在 LUIS 中的目的

實體會從語句中解壓縮資料。 實體類型可讓您進行可預測的資料提取。 實體有兩種類型: 機器學習和非機器學習。 請務必知道您在語句中使用哪一種類型的實體。 

## <a name="entity-compared-to-intent"></a>實體與意圖的比較

實體代表語句中您想要擷取的單字或片語。 一個語句可以包含許多實體，也可以完全不包含實體。 用戶端應用程式可能需要實體執行其工作, 或使用它做為使用者呈現數個選擇的指南。 

實體:

* 表示類別, 包括類似物件的集合 (位置、專案、人員、事件或概念)。 
* 描述與意圖相關的資訊


例如，「新聞搜尋」應用程式可能包含「主題」、「來源」、「關鍵字」及「發佈日期」等實體，這些都是搜尋新聞的關鍵資料。 在旅遊預訂應用程式中，「地點」、「日期」、「航空公司」、「艙等」及「機票」則是航班預訂 (與「預訂航班」意圖相關) 的關鍵資訊。

相較之下，意圖代表了整個語句的預測。 

## <a name="entities-help-with-data-extraction-only"></a>實體僅有助於資料擷取

在實體上加上標籤或標記只能用於實體擷取，無助於意圖預測。

## <a name="entities-represent-data"></a>實體代表資料

實體是您想要從語句中提取的資料。 這可以是名稱、日期、產品名稱或任何單字的群組。 

|語句|實體|Data|
|--|--|--|
|購買 3 張到紐約的機票|預先建置的號碼<br>Location.Destination|3<br>New York|
|購買 1 張 3 月 5 日從紐約到倫敦的機票|Location.Origin<br>Location.Destination<br>預先建置的 datetimeV2|New York<br>倫敦<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>實體是選擇性的，但強烈建議使用

意圖是必要的，實體則為選擇性。 您不需要為應用程式中的每個概念建立實體，只需針對用戶端應用程式執行動作所需的部分建立實體即可。 

如果您的語句中沒有 Bot 繼續工作所需的詳細資料，就無須新增這些資料。 您可以隨著應用程式趨於成熟，稍後再新增這些資料。 

如果您不確定會如何使用該資訊，可以新增一些常見的預先建置實體，例如 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序數](luis-reference-prebuilt-ordinal.md)、[電子郵件](luis-reference-prebuilt-email.md)及[電話號碼](luis-reference-prebuilt-phonenumber.md)。

## <a name="label-for-word-meaning"></a>單字意義的標籤

如果單字選擇或單字排列相同，但意義不同，請勿以實體標記它。 

下列語句中的 `fair` 一字是一個同形異義字。 其拼字相同，但意義不同：

|語句|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

如果您想要讓事件實體尋找所有事件資料，請標記第一個語句中的 `fair` 一字，但不要標記第二個語句中的該字。

## <a name="entities-are-shared-across-intents"></a>實體會跨意圖共用

實體會在意圖間共用。 它們不屬於任何單一意圖。 意圖和實體可以在語意上相關，但並不具有獨佔關聯性。

在「為我預訂到巴黎的機票」語句中，「巴黎」是代表地點的實體。 LUIS 可透過辨識使用者語句中提到的實體，協助用戶端應用程式選擇要執行以滿足使用者要求的特定動作。

## <a name="mark-entities-in-none-intent"></a>標記 None 意圖中的實體

如果可能，所有意圖 (包括 **None** 意圖) 都應有標記的實體。 這可協助 LUIS 進一步了解實體在語句中的哪裡，以及實體周圍有哪些單字。 

## <a name="entity-status-for-predictions"></a>用於預測的實體狀態

LUIS 入口網站會在範例語句中的實體與標記實體不同時，或太靠近另一個實體以至於不清楚時，讓您知道有此狀況。 這會在範例語句中以紅色底線表示。 

如需詳細資訊，請參閱[實體狀態預測](luis-how-to-add-example-utterances.md#entity-status-predictions)。 

## <a name="types-of-entities"></a>實體類型

LUIS 會提供許多類型的實體。 您可以根據擷取資料的方式和資料在擷取之後的呈現方式，來選擇實體。

實體可以透過機器學習來擷取，如此可讓 LUIS 繼續了解實體在語句中出現的方式。 實體也可不使用機器學習來進行擷取，而是比對確切文字或規則運算式。 模式中的實體可以透過混合式實作來擷取。 

擷取實體後，實體資料就可以表示為單一的資訊單位，或與其他實體結合，以形成用戶端應用程式可使用的資訊單位。

|機器學習|可標記|教學課程|範例<br>回應|實體類型|用途|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**複合**](#composite-entity)|實體群組，無論何種實體類型。|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**清單**](#list-entity)|透過比對確切文字來擷取的項目和其同義字清單。|
|混合||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|難以判斷實體結尾的實體。|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**預建**](#prebuilt-entity)|已訓練為擷取各種資料類型。|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**規則運算式**](#regular-expression-entity)|要比對文字的使用者規則運算式。|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**簡單**](#simple-entity)|在字組或片語中包含單一概念。|

只有機器學習的實體需要在範例語句中標示。 機器學習實體在透過[端點查詢](luis-concept-test.md#endpoint-testing)及[檢閱端點語句](luis-how-to-review-endoint-utt.md)來進行測試時，效果最佳。 

Pattern.any 實體需要在[模式](luis-how-to-model-intent-pattern.md)範本裡的範例 (而不是意圖裡使用者所提供的範例) 中進行標記。 

混合實體會使用實體偵測方法的組合。

## <a name="machine-learned-entities-use-context"></a>機器學習的實體使用內容

機器學習實體會從語句中的內容學習。 這會使位置變得語句顯著。 

## <a name="non-machine-learned-entities-dont-use-context"></a>非機器學習的實體未使用內容

下列非機器學習的實體在符合實體時, 不會將語句內容納入考慮: 

* [預先建置實體](#prebuilt-entity)
* [Regex 實體](#regular-expression-entity)
* [清單實體](#list-entity) 

這些實體不需要標記或定型模型。 一旦您加入或設定實體, 就會將實體解壓縮。 其取捨是可以 overmatched 這些實體, 其中如果已將內容納入考慮, 則不會進行比對。 

這會經常在新模型上使用清單實體。 您會使用清單實體來建立及測試您的模型, 但當您發行模型並從端點接收查詢時, 您會發現您的模型因缺少內容而 overmatching。 

如果您想要比對單字或片語並將內容納入考慮, 您有兩個選項。 第一種方式是使用與片語清單配對的簡單實體。 片語清單不會用來比對, 而是會協助發出相對相似的文字 (可互換的清單)。 如果您必須有完全相符的專案, 而不是片語清單的變化, 請使用具有角色的清單實體, 如下所述。

### <a name="context-with-non-machine-learned-entities"></a>具有非機器學習實體的內容

如果您想要讓非機器學習實體的語句內容很重要, 您應該使用[角色](luis-concept-roles.md)。

如果您有非機器學習的實體, 例如[預先](#prebuilt-entity)建立的實體、 [RegEx](#regular-expression-entity)實體或[清單](#list-entity)實體 (比對您想要的實例還符合), 請考慮使用兩個角色來建立一個實體。 其中一個角色將會捕捉您要尋找的內容, 而一個角色將會捕捉您不想要的內容。 在範例語句中, 這兩個版本都必須加上標籤。  

## <a name="composite-entity"></a>複合實體

[複合實體](reference-entity-composite.md)是由其他實體所組成, 例如預建實體、簡單、正則運算式和列出實體。 個別實體會構成一個完整的提體。 

## <a name="list-entity"></a>清單實體

[清單實體](reference-entity-list.md)代表一組固定且封閉的相關單字及其同義字。 LUIS 並不會探索清單實體的額外值。 使用**建議**功能，以根據目前的清單查看適用於新字組的建議。 如果有多個清單實體具有相同的值，則在端點查詢中會傳回每個實體。 

## <a name="patternany-entity"></a>Pattern.any 實體

[Pattern。 any](reference-entity-pattern-any.md)是僅用於模式範本語句的可變長度預留位置, 用來標記實體開始和結束的位置。  

## <a name="prebuilt-entity"></a>預建實體

預建實體是代表一般概念 (例如電子郵件、URL 和電話號碼) 的內建類型。 預先建置的實體名稱為保留名稱。 已新增至應用程式的[所有預建實體](luis-prebuilt-entities.md)都會在端點預測查詢中傳回 (如果有在語句中找到的話)。 

以下時機適用此實體：

* 資料符合預建實體所支援的常見使用案例 (以您的語言文化為基礎)。 

可以隨時新增或移除預建實體。

![Number 預先建置的實體](./media/luis-concept-entities/number-entity.png)

[教學課程](luis-tutorial-prebuilt-intents-entities.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#prebuilt-entity-data)

在開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案中已定義部分這些預建實體。 如果目前不支援您的特定文化特性或實體，請向專案提出。 

### <a name="troubleshooting-prebuilt-entities"></a>針對預先建立的實體進行疑難排解

在 LUIS 入口網站中, 如果預建實體已標記而不是您的自訂實體, 您可以選擇幾個方法來修正此問題。

新增至應用程式的預建實體_一律_會傳回, 即使語句應針對相同的文字解壓縮自訂實體也一樣。 

#### <a name="change-tagged-entity-in-example-utterance"></a>在範例語句中變更標記的實體

如果預先建立的實體與自訂實體具有相同的文字或標記, 請選取範例語句中的文字, 並變更已標記的語句。 

如果預先建立的實體是以比自訂實體更多的文字或標記來標記, 您有幾個方法可以選擇修正此問題:

* [移除範例語句](#remove-example-utterance-to-fix-tagging)方法
* [移除預先](#remove-prebuilt-entity-to-fix-tagging)建立的實體方法

#### <a name="remove-example-utterance-to-fix-tagging"></a>移除範例語句以修正標記 

您的第一個選擇是移除範例語句。 

1. 刪除範例語句。
1. 重新定型應用程式。 
1. 將實體 (標示為預先建立的實體) 的單字或片語僅加回, 以作為完整的範例語句。 單字或片語仍然會標示預先建立的實體。 
1. 在 [**意圖**] 頁面上, 選取範例語句中的實體, 並變更為您的自訂實體, 然後再次訓練。 這應該會讓 LUIS 無法在使用該文字的任何範例語句中, 將這項確切的文字標示為預先建立的實體。 
1. 將整個原始範例語句新增回到意圖。 自訂實體應該會繼續標示, 而不是預先建立的實體。 如果未標記自訂實體, 您需要在語句中新增該文字的更多範例。

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>移除預先建立的實體以修正標記

1. 從應用程式中移除預先建立的實體。 
1. 在 [**意圖**] 頁面上, 將範例語句中的自訂實體標記為。
1. 將應用程式定型。
1. 將預先建立的實體新增回應用程式, 並將應用程式定型。 此修正會假設預先建立的實體不是複合實體的一部分。

## <a name="regular-expression-entity"></a>規則運算式實體 

[正則運算式實體](reference-entity-regular-expression.md)會根據您所提供的正則運算式模式來解壓縮實體。

## <a name="simple-entity"></a>簡單實體

[簡單實體](reference-entity-simple.md)是一個機器學習值。 它可以是一個單字或片語。
## <a name="entity-limits"></a>實體限制

請檢閱[限制](luis-boundaries.md#model-boundaries)，以了解您可以將多少個每一種類型的實體新增到模型中。

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果您所需的實體超出實體數目上限 

您可能需要搭配使用複合實體與實體角色。

複合實體代表一個整體的多個部分。 例如，名為 PlaneTicketOrder 的複合實體可能會有 Airline、Destination、DepartureCity、DepartureDate 及 PlaneTicketClass 子實體。

LUIS 也提供非機器學習但可讓您的 LUIS 應用程式指定固定值清單的清單實體類型。 請參閱 [LUIS 界限](luis-boundaries.md)參考，以檢閱「清單」實體類型的限制。 

如果您已考慮這些實體, 而且仍然需要超過限制, 請聯絡支援人員。 若要這樣做，請收集關於您系統的詳細資訊、前往 [LUIS](luis-reference-regions.md#luis-website) 網站，然後選取 [支援]  。 如果您的 Azure 訂用帳戶包含支援服務，請與 [Azure 技術支援人員](https://azure.microsoft.com/support/options/)連絡。 

## <a name="next-steps"></a>後續步驟

了解良好[語句](luis-concept-utterance.md)的相關概念。 

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
