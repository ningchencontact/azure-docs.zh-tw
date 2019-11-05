---
title: 實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: 實體會從語句中解壓縮資料。 實體類型可讓您進行可預測的資料提取。 實體有兩種類型：機器學習和非機器學習。 請務必知道您在語句中使用哪一種類型的實體。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487593"
---
# <a name="entities-and-their-purpose-in-luis"></a>實體及其在 LUIS 中的用途

實體的主要用途是提供用戶端應用程式可預測的資料提取。 _選擇性_的次要目的是要使用描述項來提升意圖的預測。 

實體有兩種類型： 

* 機器學習-從內容
* 非機器學習-適用于確切文字相符專案

一律從機器學習的實體開始，因為這會提供最廣泛的資料提取選擇。

## <a name="entity-compared-to-intent"></a>實體與意圖的比較

實體代表您要解壓縮之語句內的資料概念。 

請考慮下列3個語句：

|語句|資料已解壓縮|說明|
|--|--|--|
|`Help`|-|沒有要解壓縮的內容。|
|`Send Bob a present`|Bob，存在|Bob 一定要完成工作。 這可能是足夠的資訊，或 bot 可能需要以後續的問題來澄清目前狀態。|
|`Send Bob a box of chocolates.`|這兩個重要的資料片段（Bob 和巧克力的方塊）對於完成使用者的要求很重要。|

一個語句可以包含許多實體，也可以完全不包含實體。 用戶端應用程式_可能_需要實體來執行其工作。 

相較之下，語句的意圖預測是_必要_的，代表整個語句。 LUIS 需要意圖中包含範例語句。 如果對用戶端應用程式而言，語句的主要目的並不重要，請將所有語句新增至 None 意圖。 

如果您稍後在應用程式生命週期中發現，您會想要打破語句，您可以輕鬆地執行此動作。 這可能是在您撰寫時組織語句，也可以在用戶端應用程式中使用預測的意圖。 

不需要在用戶端應用程式中使用預測的意圖，但會在預測端點回應中傳回。

## <a name="entities-represent-data"></a>實體代表資料

實體是您想要從語句中提取的資料。 這可以是名稱、日期、產品名稱或任何單字的群組。 

|語句|實體|資料|
|--|--|--|
|購買 3 張到紐約的機票|預先建置的號碼<br>Location.Destination|3<br>紐約|
|購買 1 張 3 月 5 日從紐約到倫敦的機票|Location.Origin<br>Location.Destination<br>預先建置的 datetimeV2|紐約<br>倫敦<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>實體是選擇性的，但強烈建議使用

意圖是必要的，實體則為選擇性。 您不需要為應用程式中的每個概念建立實體，只需針對用戶端應用程式執行動作所需的部分建立實體即可。 

如果您的語句中沒有 Bot 繼續工作所需的詳細資料，就無須新增這些資料。 您可以隨著應用程式趨於成熟，稍後再新增這些資料。 

如果您不確定會如何使用該資訊，可以新增一些常見的預先建置實體，例如 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序數](luis-reference-prebuilt-ordinal.md)、[電子郵件](luis-reference-prebuilt-email.md)及[電話號碼](luis-reference-prebuilt-phonenumber.md)。

## <a name="design-entities-for-decomposition"></a>設計用於分解的實體

使用機器學習的實體開始您的實體設計。 這可讓您在一段時間內輕鬆地設計成長和實體的變更。 新增具有**條件約束**和**描述**元的**子元件**（子實體），以完成實體設計。 

分解的設計可讓 LUIS 將深度的實體解析傳回給您的用戶端應用程式。 這可讓您的用戶端應用程式專注于商務規則，並將資料解析留給 LUIS。

### <a name="machine-learned-entities-are-primary-data-collections"></a>機器學習的實體是主要資料集合

機器學習的實體是最上層的資料單位。 子元件是機器學習實體的子實體。 

**條件約束**是套用規則來識別和解壓縮資料的精確文字相符實體。 **描述**項是為了提升預測之單字或片語的相關性而套用的功能。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>實體類型

您可以根據擷取資料的方式和資料在擷取之後的呈現方式，來選擇實體。

|實體類型|目的|
|--|--|
|[**機器學習**](#composite-entity)|實體的父群組，不論實體類型為何。 機器學習實體會從語句中的內容學習。 這會使位置變得語句顯著。 |
|[**清單**](#list-entity)|以**完全相符文字**解壓縮的專案及其同義字清單。|
|[**Pattern.any**](#patternany-entity)|難以判斷實體結尾的實體。 |
|[**預建**](#prebuilt-entity)|已定型，可將特定類型的資料（例如 URL 或電子郵件）解壓縮。 在開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案中已定義部分這些預建實體。 如果目前不支援您的特定文化特性或實體，請向專案提出。|
|[**規則運算式**](#regular-expression-entity)|會使用正則運算式來比對**完全相符的文字**。|

### <a name="entity-role-defines-context"></a>實體角色定義內容

實體的角色是以語句內的內容為基礎的命名別名。 例如，有兩個位置、來源和目的地的航班預約語句。

`Book a flight from Seattle to Cairo`

需要解壓縮 `location` 實體的兩個範例。 用戶端應用程式必須知道每個的位置類型，才能完成票證購買。 `location` 實體需要 `origin` 和 `destination` 兩個角色，而且兩者都需要在範例語句中標示。 

如果 LUIS 找到 `location`，但無法判斷角色，則仍會傳回 location 實體。 用戶端應用程式需要追蹤問題，以判斷使用者所代表的位置類型。 

語句中可以有多個實體，而且不需要使用角色就可以進行解壓縮。 如果句子的內容指出實體值，則應該使用角色。

如果語句包含位置的清單，`I want to travel to Seattle, Cairo, and London.`，這就是一個清單，其中的每個專案都不會有其他意義。 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果您所需的實體超出實體數目上限 

如果您需要超過限制，請聯絡支援人員。 若要這樣做，請收集關於您系統的詳細資訊、前往 [LUIS](luis-reference-regions.md#luis-website) 網站，然後選取 [支援]。 如果您的 Azure 訂用帳戶包含支援服務，請與 [Azure 技術支援人員](https://azure.microsoft.com/support/options/)連絡。 

## <a name="entity-prediction-status"></a>實體預測狀態

LUIS 入口網站會顯示實體（在範例中為語句）與您所選取的實體具有不同的實體預測。 這種不同的分數是以目前定型的模型為基礎。 

## <a name="next-steps"></a>後續步驟

了解良好[語句](luis-concept-utterance.md)的相關概念。 

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
