---
title: Language Understa 的預先建置定義域
titleSuffix: Azure Cognitive Services
description: LUIS 包含一組預先建置的定義域，可用來快速新增常見的交談式使用者情節。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: e1b99396c4739dc6f1b7a4da0164553d4c25ef3c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60198919"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>新增常見使用方式情節的預先建置定義域 

LUIS 包含一組來自預先建置定義域的預先建置意圖，可用來快速新增常見的意圖和語句。 這是一個既快速又簡便的方式，讓您無須為功能設計模型，即可為您的交談式用戶端應用程式新增功能。 

## <a name="add-a-prebuilt-domain"></a>新增預建網域

1. 在 [My Apps] \(我的應用程式\)  頁面上，選取您的應用程式。 這會讓您的應用程式開啟至應用程式的 [Build] \(組建\)  區段。 

1. 在 [Intents] \(意圖\)  頁面上，從左下方的工具列中選取 [Add prebuilt domains] \(新增預先建置的定義域\)  。 

1. 選取 [Calendar] \(行事曆\)  意圖，然後選取 [Add domain] \(新增定義域\)  。

    ![新增 [Calendar] \(行事曆\) 預先建置定義域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. 在左側導覽中選取 [Intents] \(意圖\)  以檢視 [Calendar] \(行事曆\) 意圖。 每個來自此定義域的意圖前面都會加上 `Calendar.`。 此定義域的兩個實體 `Calendar.Location` 和 `Calendar.Subject` 會與語句一起新增至應用程式。 

## <a name="train-and-publish"></a>定型和發佈

1. 新增定義域之後，選取右上方工具列中的 [Train] \(定型\)  來進行應用程式定型。 

1. 在頂端工具列中，選取 [Publish] \(發佈\)  。 請發佈至 [Production] \(生產環境\)  。 

1. 出現綠色成功通知時，選取 [Refer to the list of endpoints] \(參考端點清單\)  連結來查看端點。

1. 選取端點。 隨即會有新瀏覽器索引標籤開啟至該端點。 請將瀏覽器索引標籤保持開啟，然後繼續進行＜測試＞  一節。

## <a name="test"></a>測試

藉由為 **q** 參數新增一個值，在端點測試新的意圖：`Schedule a meeting with John Smith in Seattle next week`。

LUIS 會傳回正確的意圖和會議主旨：

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [預先建置的定義域參考](./luis-reference-prebuilt-domains.md)
