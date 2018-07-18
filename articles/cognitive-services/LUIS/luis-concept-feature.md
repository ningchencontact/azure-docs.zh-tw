---
title: 理解 LUIS 應用程式在 Azure 中的特性 | Microsoft Docs
description: 了解有助於改善 LUIS 應用程式效能的特性。 這些特性包括片語清單和用於辨識規則運算式的模式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 416fadaf52d7a71dcaab81aab3bf6099213e5af5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35371059"
---
# <a name="phrase-list-features-in-luis"></a>LUIS 中的片語清單功能

在機器學習中，「特性」是您的系統觀察到的資料特徵或屬性。 

將特性新增至語言模型，以針對如何辨識您想要標示或分類的輸入，提供相關提示。 特性可協助 LUIS 辨識意圖和實體，但特性不是意圖或實體本身。 相反地，替性可能會提供相關字詞的範例。  

## <a name="what-is-a-phrase-list-feature"></a>什麼是片語清單特性？
片語清單包含一組屬於相同類別的值 (字組或片語)，而且必須以類似的方式處理 (例如城市或產品的名稱)。 LUIS 對於其中一個值的認識也會自動套用到其他值。 這不是相符字組的封閉[清單實體](luis-concept-entity-types.md#types-of-entities) (全文相符項目)。

片語清單會新增至應用程式網域的詞彙，作為向 LUIS 指示這些字的第二個信號。

## <a name="how-to-use-phrase-lists"></a>如何使用片語清單
在旅行代理程式應用程式中，建立名為「城市」的片語清單，其中包含倫敦、巴黎和開羅等值。 如果您將其中一個值標示為意圖中[範例語句](luis-how-to-add-example-utterances.md#add-simple-entity-label)中的簡單實體，LUIS 就學會辨識其他值。 

片語清單可能可互換或不可互換。 「可互換」片語清單適用於同義字的值，而「不可互換」片語清單適用於不是同義字，但另一方面類似的值。 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>片語清單有助於識別簡單的可互換實體
可互換片語清單是調整 LUIS 應用程式效能的好方法。 如果您的應用程式無法將語句預測為正確的意圖，或辨識實體，請思考語句是否包含不尋常的字組，或意義可能會模稜兩可的字組。 這些字組是要包含在片語清單中的理想候選項目。

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>片語清單有助於以更加理解的內容來識別意圖
使用罕見、專屬和外來字組的片語清單。 LUIS 可能無法辨識罕見與專屬字組，以及外來字組 (應用程式的文化特性之外)，因此應該將它們新增至片語清單。 此片語清單應標記為不可互換，表示一些罕見字組會構成 LUIS 應學習辨識的類別，但這些字組並不是同義字或可彼此互換。

片語清單不會指示 LUIS 執行嚴格的比對，或一律標示片語清單中完全相同的所有字詞。 這只是一個提示。 例如，您可能有一個指出 "Patti" 和 "Selma" 都是名稱的片語清單，但是 LUIS 仍可使用內容資訊將它們認定為在 "Make a reservation for 2 at Patti's Diner for dinner" 和 "Five me driving directions to Selma, Georgia" 中有不同的意義。 

新增片語清單是將更多範例語句新增至意圖的替代方式。 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>何時使用片語清單與清單實體
雖然片語清單和清單實體都可能影響所有意圖的語句，但各自會以不同方式帶來影響。 使用片語清單來影響意圖預測分數。 使用清單實體來影響全文相符實體的擷取。 

### <a name="use-a-phrase-list"></a>使用片語清單
使用片語清單，LUIS 仍可將內容納入考量並且一般化，以識別類似但並未與清單中的項目完全相符的項目。 如果您希望 LUIS 應用程式能夠一般化並識別類別中的新項目，請使用字詞清單。 

當您想要能夠辨識實體的新執行個體時 (應可辨識新連絡人名稱的會議排程器，或應可辨識新產品的清查應用程式)，請使用另一種機器學習的實體類型，例如簡單或階層式實體。 然後建立字組和片語的片語清單，協助 LUIS 尋找其他類似於實體的字組。 這份清單會將額外的重要性新增至這些個字組的值，引導 LUIS 辨識實體範例。 

片語清單就像是網域專屬詞彙，有助於增強意圖和實體的理解品質。 片語清單的常見用法是專有名詞，例如城市名稱。 城市名稱可以是包含連字號或單引號的幾個字組。
 
### <a name="dont-use-a-phrase-list"></a>請勿使用片語清單 
清單實體會明確地定義實體可以採用的每個值，而且只會識別完全相符的值。 清單實體可能適合於下列應用程式：其中實體的所有執行個體均為已知且不會經常變更，例如餐廳菜單上不常變更的食物項目。 如果您需要全文相符的實體，請勿使用片語清單。 

## <a name="best-practices"></a>最佳作法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

請參閱[新增功能](luis-how-to-add-features.md)，深入了解如何將功能新增至 LUIS 應用程式。