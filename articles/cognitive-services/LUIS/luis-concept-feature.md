---
title: 功能-LUIS
titleSuffix: Azure Cognitive Services
description: 將特性新增至語言模型，以針對如何辨識您想要標示或分類的輸入，提供相關提示。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dab4b4c6f41a95623a40e5d3fd859f9613afac27
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949605"
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

當您的應用程式具有對應用程式很重要的單字或片語時，請[建立片語](luis-how-to-add-features.md)清單，例如：

* 業界術語
* 俚語
* 縮寫
* 公司特定語言
* 來自另一種語言但通常在您的應用程式中使用的語言
* 範例表達中的關鍵字或片語

您輸入幾個單字或片語後，使用**建議**功能尋找相關的值。 加入到您的片語清單值之前，請檢閱相關的值。

片語清單適用于同義字的值。 比方說，如果您想要找到所有關於水的內容，而且您有範例表達，例如： 

* 哪些城市靠近五大湖？ 
* 哪條路沿著哈瓦蘇湖開闢？
* 尼羅河的源頭和出海口在哪裡？ 

無論關於水的內容為何，對於每個表達都應該會決定意圖和實體： 

* 哪座城市靠近 [bodyOfWater]？
* 哪條路沿著 [bodyOfWater] 開闢？
* [bodyOfWater] 的源頭和出海口在哪裡？ 

因為水主體的單字或片語是同義的，而且可以在語句中交替使用。 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>片語清單有助於識別簡單的可互換實體
可交換的片語清單是調整 LUIS 應用程式效能的好方法。 如果您的應用程式無法將語句預測為正確的意圖，或辨識實體，請思考語句是否包含不尋常的字組，或意義可能會模稜兩可的字組。 這些字組是要包含在片語清單中的理想候選項目。

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>片語清單有助於以更加理解的內容來識別意圖
片語清單不會指示 LUIS 執行嚴格的比對，或一律標示片語清單中完全相同的所有字詞。 這只是一個提示。 例如，您可能有一個指出 "Patti" 和 "Selma" 都是名稱的片語清單，但 LUIS 仍可使用內容資訊來將它們認定為在 "Make a reservation for 2 at Patti's Diner for dinner" (在 Patti's Diner 預訂兩人份的晚餐) 和 "Find me driving directions to Selma, Georgia" (為我指引到 Selma, Georgia 的行車路線) 中有不同的意義。 

新增片語清單是將更多範例語句新增至意圖的替代方式。 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>何時使用片語清單與清單實體
雖然片語清單和[清單實體](reference-entity-list.md)都可能影響所有意圖的語句，但每個都會以不同的方式來執行這項工作。 使用片語清單來影響意圖預測分數。 使用清單實體來影響全文相符實體的擷取。 

### <a name="use-a-phrase-list"></a>使用片語清單
使用片語清單，LUIS 仍可將內容納入考量並且一般化，以識別類似但並未與清單中的項目完全相符的項目。 如果您希望 LUIS 應用程式能夠一般化並識別類別中的新項目，請使用字詞清單。 

當您想要能夠辨識實體的新實例時（例如，應辨識新連絡人名稱的會議排程器），或應辨識新產品的清查應用程式時，請使用另一種機器學習的實體，例如簡單實體。 然後建立字組和片語的片語清單，協助 LUIS 尋找其他類似於實體的字組。 這份清單會將額外的重要性新增至這些個字組的值，引導 LUIS 辨識實體範例。 

片語清單就像是網域專屬詞彙，有助於增強意圖和實體的理解品質。 片語清單的常見用法是專有名詞，例如城市名稱。 城市名稱可以是包含連字號或單引號的幾個字組。
 
### <a name="dont-use-a-phrase-list"></a>請勿使用片語清單 
清單實體會明確地定義實體可以採用的每個值，而且只會識別完全相符的值。 清單實體可能適用於下列應用程式：其中實體的所有執行個體均為已知且不會經常變更。 範例為餐廳菜單上不常變更的食物項目。 如果您需要全文相符的實體，請勿使用片語清單。 

## <a name="best-practices"></a>最佳做法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

請參閱[新增功能](luis-how-to-add-features.md)，深入了解如何將功能新增至 LUIS 應用程式。
