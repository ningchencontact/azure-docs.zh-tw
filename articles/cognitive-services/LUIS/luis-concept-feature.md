---
title: 特性
titleSuffix: Language Understanding - Azure Cognitive Services
description: 將特性新增至語言模型，以針對如何辨識您想要標示或分類的輸入，提供相關提示。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 89d18ebd2f52467a19a76940044fea3ae254970a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770158"
---
# <a name="phrase-list-features-in-your-luis-app"></a>LUIS 應用程式中的片語清單功能

在機器學習中，*特性*是您的系統觀察到的資料特徵或屬性。 

將特性新增至語言模型，以針對如何辨識您想要標示或分類的輸入，提供相關提示。 特性可協助 LUIS 辨識意圖和實體，但特性不是意圖或實體本身。 相反地，替性可能會提供相關字詞的範例。  

## <a name="what-is-a-phrase-list-feature"></a>什麼是片語清單特性？
片語清單是應用程式相當重要的單字或片語清單，重要性比表達中的其他字詞高。 片語清單會新增至應用程式網域的詞彙，做為向 LUIS 指示這些字的額外信號。 LUIS 對於其中一個值的認識也會自動套用到其他值。 這個清單不是全文相符項目的封閉[清單實體](luis-concept-entity-types.md#types-of-entities)。

片語清單無助於詞幹分析，因此您需要新增對於任何重要詞彙單字和片語使用各種詞幹分析的表達範例。

## <a name="phrase-lists-help-all-models"></a>片語清單適用於所有模型

片語清單不會連結至特定的意圖或實體，而是為所有意圖或實體顯著提升效能。 其目的是要改善意圖偵測和實體分類。

## <a name="how-to-use-phrase-lists"></a>如何使用片語清單

您的應用程式有如下所示的重要單字或片語時，建立片語清單：

* 業界術語
* 俚語
* 縮寫
* 公司特定語言
* 來自另一種語言但通常在您的應用程式中使用的語言
* 範例表達中的關鍵字或片語

您輸入幾個單字或片語後，使用**建議**功能尋找相關的值。 加入到您的片語清單值之前，請檢閱相關的值。

|清單類型|目的|
|--|--|
|可交換|同義字或字組 (當變更為清單中的另一個字組時) 具有相同的意圖和實體擷取。|
|不可交換|應用程式詞彙 (特別是您的應用程式) 通常比該語言中的其他字組還要多。|

### <a name="interchangeable-lists"></a>可交換清單

*可交換*片語清單列出同義字的值。 比方說，如果您想要找到所有關於水的內容，而且您有範例表達，例如： 

* 哪些城市靠近五大湖？ 
* 哪條路沿著哈瓦蘇湖開闢？
* 尼羅河的源頭和出海口在哪裡？ 

無論關於水的內容為何，對於每個表達都應該會決定意圖和實體： 

* 哪座城市靠近 [bodyOfWater]？
* 哪條路沿著 [bodyOfWater] 開闢？
* [bodyOfWater] 的源頭和出海口在哪裡？ 

因為關於水的內容所用的單字或片語都是同義詞，而且可以在表達中交換使用，因此使用片語清單的**可交換**設定。 

### <a name="non-interchangeable-lists"></a>不可交換清單

不可交換片語清單是將偵測提升到 LUIS 的信號。 片語清單指出比其他字詞更重要的單字或片語。 這有助於決定意圖和實體偵測。 例如，假設有全球旅遊之類的主旨範圍（表示跨文化特性，但仍然使用單一語言）。 有些單字和片語對於應用程式很重要但並非同義詞。 

此外，例如，針對罕見、專用及外來的字詞，應使用不可交換片語清單。 LUIS 可能無法辨識罕見與專用的文字以及外來文字 (應用程式的文化特性之外)。 不可交換的設定表示這組罕見文字會構成 LUIS 應學習辨識的類別，但這些文字並不是同義字或可彼此交換的。

請勿將每個可能的單字或片語新增到片語清單中，一次僅新增幾個單字或片語，然後重新定型和發佈。 

在片語清單隨時間而增加的同時，您可能會發現一些字詞有許多形式 (同義字)。 將這些字詞分解為另一個可交換的片語清單。 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>片語清單有助於識別簡單的可交換實體
可交換的片語清單是調整 LUIS 應用程式效能的好方法。 如果您的應用程式無法將語句預測為正確的意圖，或辨識實體，請思考語句是否包含不尋常的字組，或意義可能會模稜兩可的字組。 這些字組是要包含在片語清單中的理想候選項目。

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>片語清單有助於以更加理解的內容來識別意圖
片語清單不會指示 LUIS 執行嚴格的比對，或一律標示片語清單中完全相同的所有字詞。 這只是一個提示。 例如，您可能有一個指出 "Patti" 和 "Selma" 都是名稱的片語清單，但 LUIS 仍可使用內容資訊來將它們認定為在 "Make a reservation for 2 at Patti's Diner for dinner" (在 Patti's Diner 預訂兩人份的晚餐) 和 "Find me driving directions to Selma, Georgia" (為我指引到 Selma, Georgia 的行車路線) 中有不同的意義。 

新增片語清單是將更多範例語句新增至意圖的替代方式。 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>何時使用片語清單與清單實體
雖然片語清單和清單實體都可能影響所有意圖的語句，但各自會以不同方式帶來影響。 使用片語清單來影響意圖預測分數。 使用清單實體來影響全文相符實體的擷取。 

### <a name="use-a-phrase-list"></a>使用片語清單
使用片語清單，LUIS 仍可將內容納入考量並且一般化，以識別類似但並未與清單中的項目完全相符的項目。 如果您希望 LUIS 應用程式能夠一般化並識別類別中的新項目，請使用字詞清單。 

當您想要能夠辨識實體的新執行個體時 (應可辨識新連絡人名稱的會議排程器，或應可辨識新產品的清查應用程式)，請使用另一種機器學習的實體類型，例如簡單或階層式實體。 然後建立字組和片語的片語清單，協助 LUIS 尋找其他類似於實體的字組。 這份清單會將額外的重要性新增至這些個字組的值，引導 LUIS 辨識實體範例。 

片語清單就像是網域專屬詞彙，有助於增強意圖和實體的理解品質。 片語清單的常見用法是專有名詞，例如城市名稱。 城市名稱可以是包含連字號或單引號的幾個字組。
 
### <a name="dont-use-a-phrase-list"></a>請勿使用片語清單 
清單實體會明確地定義實體可以採用的每個值，而且只會識別完全相符的值。 清單實體可能適用於下列應用程式：其中實體的所有執行個體均為已知且不會經常變更。 範例為餐廳菜單上不常變更的食物項目。 如果您需要全文相符的實體，請勿使用片語清單。 

## <a name="best-practices"></a>最佳作法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

請參閱[新增功能](luis-how-to-add-features.md)，深入了解如何將功能新增至 LUIS 應用程式。
