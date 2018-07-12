---
title: 使用從 LUIS 預先建置的 Cortana 應用程式 | Microsoft Docs
description: 使用 Cortana 個人助理，此為從 Language Understanding Intelligent Service (LUIS) 預先建置的應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: b792d090d037ef180258a1634d4bd063c0a71b9a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369134"
---
# <a name="cortana-prebuilt-app"></a>預先建置的 Cortana 應用程式

> [!IMPORTANT]
> 我們建議您使用[預先建置的網域](./luis-how-to-use-prebuilt-domains.md)，而不是預先建置的 Cortana 應用程式。 例如，不使用 **builtin.intent.calendar.create_calendar_entry**，而是從從預先建置的 **Calendar** 網域使用 **Calendar.Add**。
> 預先建置的網域提供下列優點： 
> * 它們提供預先建置和預先定型意圖與實體的套件，其設計可讓彼此能夠搭配運作。 您可以將預先建置的網域直接整合到您的應用程式。 例如，如果您正在建置健身追蹤器，便可以新增 **Fitness** 網域，而且有一整組適用於追蹤健身活動的意圖與實體，包括用於追蹤體重與餐點計劃的意圖、剩餘的時間或距離，以及儲存健身活動筆記。
> * 預先建置的網域意圖皆可自訂。 例如，如果您想要提供旅館的檢閱，可將來自 **Places** 網域的 **Places.GetReviews** 意圖定型並進行自訂，以辨識旅館檢閱的要求。
> * 預先建置的網域均為可擴充的。 例如，如果您想要在聊天機器人中使用預先建置的 **Places** 網域來搜尋餐廳，而且需要取得料理類型的意圖，則可以建置 **Places.GetCuisine** 意圖並為其定型。

除了可讓您建置自己的應用程式，LUIS 也提供來自 Microsoft Cortana 個人助理的意圖與實體作為預先建置的應用程式。 您無法變更這個可公開使用的 LUIS 應用程式行為。 您無法編輯此應用程式中的意圖與實體，或將其整合到其他 LUIS 應用程式。 如果您想要讓用戶端應用程式可以存取此預先建置的應用程式和您自己的 LUIS 應用程式，則用戶端應用程式必須參考這兩個 LUIS 應用程式。

預先建置的個人助理應用程式適用於下列文化特性 (地區設定)：英文、法文、義大利文、西班牙文和中文。

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>取得適用於預先建置之 Cortana 應用程式的端點

您可以使用下列端點來存取 Cortana 預先建置的應用程式： 

| 語言 | 端點|
|--------| ------------------|
| English| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    中文| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    法文| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    西班牙文| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    義大利文| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> 您也可以從[應用程式 - 取得個人助理應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) \(英文\) API 取得端點 URL。

## <a name="try-out-the-personal-assistant-app"></a>嘗試使用個人助理應用程式
若要呼叫端點，您可以將訂用帳戶金鑰引數和查詢字串附加至端點。 

例如，如果您想要解譯的語句為「建立小組會議的約會」，則可將該語句附加到端點 URL。 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

您可以將此 URL 貼到網頁瀏覽器中，並針對 `{YOUR-SUBSCRIPTION-KEY}` 欄位來替代您的訂用帳戶金鑰。

在瀏覽器中，您可以看到預先建置的 Cortana 應用程式會將 `builtin.intent.calendar.create_calendar_entry` 識別為意圖，將 `builtin.calendar.title` 識別為實體類型，並適用於`create an appointment for team meeting`的語句。

![使用預先建置的 Cortana 應用程式](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [預先建置的 Cortana 應用程式參考](./luis-reference-cortana-prebuilt.md)

