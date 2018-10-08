---
title: 使用預先建置的網域在 LUIS 應用程式中更快速地建置應用程式
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) 提供預先建置的網域，這是預先建置的意圖和實體集合，可針對網域或常見的用戶端應用程式類別一起運作。 預建網域已經過預先訓練，並準備好讓您新增至 LUIS 應用程式。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 1aa7e1bf9c1a584803a60a5061b4ae4cc8664ff4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037366"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>在 LUIS 應用程式中使用預建網域  

Language Understanding (LUIS) 提供「預建網域」，這是預建的[意圖](luis-how-to-add-intents.md)和[實體](luis-concept-entity-types.md)集合，可針對網域或常見的用戶端應用程式類別一起運作。 預建網域已經過預先訓練，並準備好讓您新增至 LUIS 應用程式。 將這些網域新增至您的應用程式之後，即可完全自訂預建網域中的意圖和實體 - 您可以使用來自您系統的語句來訓練它們，讓您的使用者能夠使用。 您可以使用整個預建網域作為自訂起點，或是只要從預建網域借用少數意圖或實體。 

瀏覽 [預建網域] 索引標籤，查看您可以在應用程式中使用的其他預建網域。 按一下網域的圖格，將其新增至您的應用程式，或按一下其圖格中的 [深入了解] 以了解其意圖和實體。

> [!TIP]
> 您可以在[預建網域參考](./luis-reference-prebuilt-domains.md)中發現完整的預建網域清單。

![新增預建網域](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>新增預建網域
在 [預建網域] 索引標籤中，尋找 RestaurantReservation 網域並按一下 [新增網域]。 將預建網域新增至 LUIS 應用程式後，開啟 [意圖]，然後按一下 RestaurantReservation.Reserve 意圖。 您可以看到已經提供了許多範例語句並標示實體。

![RestaurantReservation.Reserve 意圖](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>設計來自預建網域的 LUIS 應用程式
在 LUIS 應用程式中使用預建網域時，可以自訂整個預建網域，或是只要從幾個意圖和實體著手。

## <a name="customizing-an-entire-prebuilt-domain"></a>自訂整個預建網域
預建網域會設計為一般網域。 它們包含您可從中選擇的許多意圖和實體，以針對您的需求自訂應用程式。 如果您從自訂整個預建網域著手，請刪除您的應用程式不需要使用的意圖和實體。 您也可以將一些意圖或實體新增至預建網域已經提供的集合。 例如，如果您對運動事件應用程式使用 [事件] 預建網域，則可為運動團隊新增實體。 當您開始[提供語句](luis-how-to-add-example-utterances.md)給 LUIS 時，請包含您應用程式專屬的條款。 LUIS 會學習辨識這些條款，並針對您應用程式的需求調整預建網域的意圖和實體。 

> [!TIP]
> 預建網域中的意圖和實體一起運作的效果最好。 最好儘可能結合來自相同網域的意圖和實體。
> * 最佳做法是使用來自相同網域的相關意圖。 例如，如果您要自訂 [地點] 網域中的 `MakeReservation` 意圖，則從 [地點] 網域選取 `Cancel` 意圖，而不是 [事件] 或 [公用程式] 網域中的 [取消] 意圖。

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>變更預建網域意圖的行為
您可能會發現，預建網域包含的意圖類似於您希望存在 LUIS 應用程式中，但行為不同的意圖。 例如，[地點] 預建網域會提供 `MakeReservation` 意圖以供預訂餐廳，但您希望您的應用程式使用該意圖來預訂旅館。 在此情況下，您可以修改該意圖的行為，做法是將有關預訂旅館的語句提供給 LUIS 並使用 `MakeReservation` 意圖加以標記，那麼就可以在預訂飯店要求中重新訓練 LUIS 來辨識 `MakeReservation` 意圖。

> [!TIP]
> 查看 [公用程式] 網域中您可自訂以便使用於任何網域的預建意圖。 例如，您可以將 `Utilities.Repeat` 新增至您的應用程式並訓練它辨識使用者可能想要在您的應用程式中重複的動作。


## <a name="next-step"></a>後續步驟

新增更多範例語句以自訂預建網域。

> [!div class="nextstepaction"]
> [新增範例語句](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>其他資源

如需預建網域的詳細資訊，請參閱[預建網域參考](./luis-reference-prebuilt-domains.md)。
