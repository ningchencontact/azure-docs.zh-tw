---
title: 規劃 LUIS 應用程式 | Microsoft Docs
description: 概述相關的應用程式意圖和實體，然後在 Language Understanding Intelligent Service (LUIS) 中建立應用程式計畫。
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 7aec5d5b90ac7145ce9f337ec74c590b4b88c6b1
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266357"
---
# <a name="plan-your-luis-app"></a>規劃 LUIS 應用程式

在開始於 LUIS 中建立應用程式之前，請務必先做出相關規劃。 請針對與您應用程式領域特定主題相關的可能意圖和實體，準備一份概要或綱要。  

## <a name="identify-your-domain"></a>識別您的領域
LUIS 應用程式是以領域特定的主題為核心。  例如，您可能準備建立一個旅遊應用程式，能夠預訂票證、航班、旅館及汽車租借。 另一個應用程式則可以提供與運動、追蹤健身進度及設定目標相關的內容。 

> [!TIP]
> LUIS 有針對許多常見案例提供[預先建置的領域](luis-how-to-use-prebuilt-domains.md)。
> 請確認您是否能使用預先建置的領域作為應用程式的起點。

## <a name="identify-your-intents"></a>識別您的意圖
想想各種對您應用程式的工作較為重要的[意圖](luis-concept-intent.md)。 讓我們以一個旅遊應用程式作為例子。這個應用程式能夠預訂航班，並可查看使用者目的地的天氣。 您可以針對那些動作定義 "BookFlight" \(預訂航班\) 和 "GetWeather" \(取得天氣\) 意圖。 在一個更複雜且具有更多功能的應用程式中，您將會有更多意圖，並應該小心定義它們，使意圖不至於太過明確。 例如，"BookFlight" \(預訂航班\) 和 "BookHotel" \(預訂旅館\) 可能有必要區分為個別的意圖，但 "BookInternationalFlight" \(預訂國際航班\) 和 "BookDomesticFlight" \(預訂國內航班\) 則可能有點過於相似。

> [!NOTE]
> 最佳做法是僅使用必要的意圖數目來執行應用程式的功能。 若您定義過多的意圖，LUIS 可能會無法正確地分類語句。 若您定義的意圖太少，意圖可能會因為過於籠統而互相重疊。


## <a name="identify-your-entities"></a>識別您的實體
若要預訂航班，您需要如目的地、日期、航空公司、票證類別及艙等之類的資訊。 您可以將這些新增為[實體](luis-concept-entity-types.md)，因為它們對於達成意圖來說是非常重要的。 

當您判斷出要在應用程式中使用哪些實體之後，請記得您可以使用不同類型的實體，來擷取不同類型物件之間的關聯性。 [LUIS 中的實體](luis-concept-entity-types.md)能提供有關這些不同類型的詳細資料。

### <a name="simple-entity"></a>簡單實體
簡單實體能描述單一概念。

![簡單實體](./media/luis-plan-your-app/simple-entity.png)

請參閱[資料擷取](luis-concept-data-extraction.md#simple-entity-data)，以深入了解如何從端點 JSON 查詢回應擷取簡單實體。 嘗試簡單實體[快速入門](luis-quickstart-primary-and-secondary-data.md)，以深入了解如何使用簡單實體。

### <a name="hierarchical-entity"></a>階層式實體
階層式實體是特殊類型的**簡單**實體，能夠以父子式關聯性的形式定義類別及其成員。

![階層式實體](./media/luis-plan-your-app/hierarchical-entity.png)

請參閱[資料擷取](luis-concept-data-extraction.md#hierarchical-entity-data)，以深入了解如何從端點 JSON 查詢回應擷取階層式實體。 嘗試階層式實體[快速入門](luis-quickstart-intent-and-hier-entity.md)，以深入了解如何使用階層式實體。

### <a name="composite-entity"></a>複合實體
複合實體是由其他個別的組件實體所構成。 

![複合實體](./media/luis-plan-your-app/composite-entity.png)

請參閱[資料擷取](luis-concept-data-extraction.md#composite-entity-data)，以深入了解如何從端點 JSON 查詢回應擷取複合實體。 嘗試複合實體[教學課程](luis-tutorial-composite-entity.md)，以深入了解如何使用複合實體。

### <a name="prebuilt-entity"></a>預先建置的實體
LUIS 針對 `Number` 之類的常見類型 (可在訂票中作為訂票數目)，提供了[預先建置的實體](Pre-builtEntities.md)。

![Number 預先建置的實體](./media/luis-plan-your-app/number-entity.png)

請參閱[資料擷取](luis-concept-data-extraction.md#prebuilt-entity-data)，以深入了解如何從端點 JSON 查詢回應擷取規則運算式實體。 

### <a name="list-entity"></a>清單實體 
清單實體為明確指定的值清單。 每個值都包含一或多個同義字。 以旅遊應用程式為例，您可能會建立代表機場名稱的清單實體。

![清單實體](./media/luis-plan-your-app/list-entity.png)

請參閱[資料擷取](luis-concept-data-extraction.md#list-entity-data)，以深入了解如何從端點 JSON 查詢回應擷取清單實體。 嘗試[快速入門](luis-quickstart-intent-and-list-entity.md)，以深入了解如何使用清單實體。

### <a name="regular-expression-entity"></a>規則運算式實體
規則運算式實體可讓 LUIS 根據 RegEx 運算式從語句擷取資料。

![規則運算式實體](./media/luis-plan-your-app/regex-entity.png)

請參閱[資料擷取](luis-concept-data-extraction.md#regular-expression-entity-data)，以深入了解如何從端點 JSON 查詢回應擷取規則運算式實體。 嘗試[快速入門](luis-quickstart-intents-regex-entity.md)，以深入了解如何使用規則運算式實體。

## <a name="after-getting-endpoint-utterances"></a>取得端點語句之後
在您的應用程式取得端點語句之後，請規畫使用[主動學習](label-suggested-utterances.md)、[片語清單](luis-concept-feature.md)及[模式](luis-concept-patterns.md)來實作預測上的改善。 

### <a name="patternany-entity"></a>Pattern.any 實體
Patterns.any 為僅用於[模式](luis-concept-patterns.md)範本語句的可變長度預留位置，以用來標記實體開始及結束的位置。 範本語句需符合[適當語法](luis-concept-patterns.md#pattern-syntax)以識別實體及可忽略的文字。


## <a name="next-steps"></a>後續步驟
* 請參閱[建立您的第一個 Language Understanding Intelligent Service (LUIS) 應用程式][luis-get-started-create-app]，以取得建立 LUIS 應用程式的快速逐步解說。

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app