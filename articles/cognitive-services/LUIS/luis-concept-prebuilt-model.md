---
title: 預先建置的模型
titleSuffix: Language Understanding - Azure Cognitive Services
description: 預先建置的模型會提供定義域、意圖、語句及實體。 您可以使用預先建置的定義域來開始您的應用程式，或是稍後將相關的定義域新增至您的應用程式。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: beed7f428576605c870288ed464e782cd4955111
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218711"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>預先建置的定義域、意圖及實體模型

預先建置的模型會提供定義域、意圖、語句及實體。 您可以使用預先建置的定義域來開始您的應用程式，或是稍後將相關的定義域新增至您的應用程式。 

## <a name="types-of-prebuilt-models"></a>預先建置的模型類型

LUIS 提供 3 種預先建置的模型類型。 您可以隨時將各個模型新增至您的應用程式。 

|模型類型|包括|
|--|--|
|網域|意圖、語句、實體|
|對應方式|意圖、語句|
|實體|僅限實體| 

## <a name="prebuilt-domains"></a>預先建置的定義域

Language Understanding (LUIS) 提供「預建網域」，這是預建的[意圖](luis-how-to-add-intents.md)和[實體](luis-concept-entity-types.md)集合，可針對網域或常見的用戶端應用程式類別一起運作。 

預先建置的定義域已定型，並已準備好新增到您的 LUIS 應用程式。 在您將預先建置定義域中的意圖和實體新增到應用程式之後，可以完全自訂這些意圖和實體。 

如果您從自訂整個預建網域著手，請刪除您的應用程式不需要使用的意圖和實體。 您也可以將一些意圖或實體新增至預建網域已經提供的集合。 例如，如果您對運動事件應用程式使用 [事件] 預建網域，則可為運動團隊新增實體。 當您開始[提供語句](luis-how-to-add-example-utterances.md)給 LUIS 時，請包含您應用程式專屬的條款。 LUIS 會學習辨識這些條款，並針對您應用程式的需求調整預建網域的意圖和實體。 

> [!TIP]
> 預建網域中的意圖和實體一起運作的效果最好。 最好儘可能結合來自相同網域的意圖和實體。
> 公用程式預先建置的定義域含有可供您自訂以在任何定義域中使用的意圖。 例如，您可以將 `Utilities.Repeat` 新增至您的應用程式並訓練它辨識使用者可能想要在您的應用程式中重複的動作。 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>變更預建網域意圖的行為

您可能會發現，預建網域包含的意圖類似於您希望存在 LUIS 應用程式中，但行為不同的意圖。 例如，[地點] 預建網域會提供 `MakeReservation` 意圖以供預訂餐廳，但您希望您的應用程式使用該意圖來預訂旅館。 在此情況下，您可以修改該意圖的行為，做法是將有關預訂旅館的語句提供給 LUIS 並使用 `MakeReservation` 意圖加以標記，那麼就可以在預訂飯店要求中重新訓練 LUIS 來辨識 `MakeReservation` 意圖。

您可以在[預建網域參考](./luis-reference-prebuilt-domains.md)中發現完整的預建網域清單。

## <a name="prebuilt-intents"></a>預先建置的意圖

LUIS 提供預先建置的意圖及其語句。 您可以新增意圖而不新增整個定義域。 新增意圖是一個新增意圖及其語句的程序。 意圖名稱和語句清單都可供修改。  

## <a name="prebuilt-entities"></a>預先建置的實體

LUIS 包含一組預先建置的實體，用於辨識常見的資訊類型，例如日期、時間、數字、計量及貨幣。 預先建置的實體支援會因 LUIS 應用程式的文化特性而異。 如需 LUIS 所支援預先建置實體的完整清單，包括文化特性支援的實體在內，請參閱[預先建置的實體參考](./luis-reference-prebuilt-entities.md)。

當應用程式中包含預先建置的實體時，其預測會包含在已發佈的應用程式中。 預先建置實體的行為已預先定型且**無法**修改。 請遵循下列步驟，以查看預先建置實體的運作方式：

> [!NOTE]
> **builtin.datetime** 已被取代。 它已由 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) 取代，可供辨識日期與時間範圍，以及改善辨識語意模糊的日期與時間。

## <a name="next-steps"></a>後續步驟

了解如何[將預先建置的實體](luis-prebuilt-entities.md)新增到您的應用程式。
