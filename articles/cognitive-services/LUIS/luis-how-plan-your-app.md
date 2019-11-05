---
title: 規劃您的應用程式-LUIS
titleSuffix: Azure Cognitive Services
description: 概述相關的應用程式意圖和實體，然後在 Language Understanding Intelligent Service (LUIS) 中建立應用程式計畫。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467709"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>規劃您的 LUIS 應用程式架構與主旨網域和資料提取

LUIS 應用程式架構包含與您的主體網域相關的意圖和實體。 這些意圖會分類使用者語句，而實體會從使用者語句解壓縮資料。 

## <a name="identify-your-domain"></a>識別您的領域

LUIS 應用程式是以領域特定的主題為核心。  例如，您可能準備建立一個旅遊應用程式，能夠預訂票證、航班、旅館及汽車租借。 另一個應用程式則可以提供與運動、追蹤健身進度及設定目標相關的內容。 識別網域有助於您尋找對您的網域具有重要性的單字或片語。

> [!TIP]
> LUIS 有針對許多常見案例提供[預先建置的領域](luis-how-to-use-prebuilt-domains.md)。
> 請確認您是否能使用預先建置的領域作為應用程式的起點。

## <a name="identify-your-intents"></a>識別您的意圖

想想各種對您應用程式的工作較為重要的[意圖](luis-concept-intent.md)。 

讓我們以一個旅遊應用程式作為例子。這個應用程式能夠預訂航班，並可查看使用者目的地的天氣。 您可以為這些動作定義 `BookFlight` 和 `GetWeather` 意圖。 

在具有更多功能的更複雜應用程式中，您會有更多意圖，而且您應該謹慎定義它們，讓意圖不是特定的。 例如，`BookFlight` 和 `BookHotel` 可能需要不同的意圖，但 `BookInternationalFlight` 和 `BookDomesticFlight` 可能會太類似。

> [!NOTE]
> 最佳做法是僅使用必要的意圖數目來執行應用程式的功能。 若您定義過多的意圖，LUIS 可能會無法正確地分類語句。 如果您定義的太少，它們可能會重迭。

如果您不需要識別整體使用者意圖，請將所有範例使用者語句新增至 [無] 意圖。 如果您的應用程式成長到需要更多意圖，您可以稍後再建立。 

## <a name="create-example-utterances-for-each-intent"></a>建立每個意圖的範例語句

決定意圖之後，請為每個意圖建立15到30個範例語句。 一開始，請不要少於此數目，或為每個意圖建立太多語句。 每個語句都應該與上一個語句不同。 良好的語句變化包括整體字數、用字選擇、動詞時態和標點符號。 

如需詳細資訊，請參閱[語句](luis-concept-utterance.md)。

## <a name="identify-your-entities"></a>識別您的實體

在範例語句中，識別您要擷取的實體。 若要預訂航班，您需要「目的地」、「日期」、「航空公司」、「票證」類別和「旅遊」等資訊。 建立這些資料類型的實體，然後在範例語句中標示[實體](luis-concept-entity-types.md)，因為它們對於完成意圖很重要。 

當您判斷出要在應用程式中使用哪些實體之後，請記得您可以使用不同類型的實體，來擷取不同類型物件之間的關聯性。 [LUIS 中的實體](luis-concept-entity-types.md)能提供有關這些不同類型的詳細資料。

## <a name="next-steps"></a>後續步驟

瞭解一般的[開發週期](luis-concept-app-iteration.md)。  