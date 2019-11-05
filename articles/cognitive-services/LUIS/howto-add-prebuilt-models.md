---
title: Language Understanding 的預建模型
titleSuffix: Azure Cognitive Services
description: LUIS 包含一組預先建立的模型，可快速新增常見的交談式使用者案例。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501024"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>為一般使用案例新增預先建立的模型 

LUIS 包含一組來自預先建置定義域的預先建置意圖，可用來快速新增常見的意圖和語句。 這是一個既快速又簡便的方式，讓您無須為功能設計模型，即可為您的交談式用戶端應用程式新增功能。 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>新增預建網域

1. 在 [My Apps] \(我的應用程式\) 頁面上，選取您的應用程式。 這會讓您的應用程式開啟至應用程式的 [Build] \(組建\) 區段。 

1. 在 [Intents] \(意圖\) 頁面上，從左下方的工具列中選取 [Add prebuilt domains] \(新增預先建置的定義域\)。 

1. 選取 [Calendar] \(行事曆\) 意圖，然後選取 [Add domain] \(新增定義域\)。

    ![新增 [Calendar] \(行事曆\) 預先建置定義域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. 在左側導覽中選取 [Intents] \(意圖\) 以檢視 [Calendar] \(行事曆\) 意圖。 每個來自此定義域的意圖前面都會加上 `Calendar.`。 此定義域的兩個實體 `Calendar.Location` 和 `Calendar.Subject` 會與語句一起新增至應用程式。 

### <a name="train-and-publish"></a>定型和發佈

1. 新增定義域之後，選取右上方工具列中的 [Train] \(定型\) 來進行應用程式定型。 

1. 在頂端工具列中，選取 [Publish] \(發佈\)。 請發佈至 [Production] \(生產環境\)。 

1. 出現綠色成功通知時，選取 [Refer to the list of endpoints] \(參考端點清單\) 連結來查看端點。

1. 選取端點。 隨即會有新瀏覽器索引標籤開啟至該端點。 瀏覽器索引標籤保持開啟，然後繼續進行＜測試＞一節。

### <a name="test"></a>測試

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

## <a name="add-a-prebuilt-intent"></a>新增預先建置的意圖

1. 在 [My Apps] \(我的應用程式\) 頁面上，選取您的應用程式。 這會讓您的應用程式開啟至應用程式的 [Build] \(組建\) 區段。 

1. 在 [Intents] \(意圖\) 頁面上，從意圖清單上方的工具列中選取 [Add prebuilt intent] \(新增預先建置的意圖\)。 

1. 從快顯對話方塊中選取 [Utilities.Cancel] 意圖。 

    ![新增預先建置的意圖](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. 選取 [Done] \(完成\) 按鈕。

### <a name="train-and-test"></a>定型和測試

1. 新增意圖之後，選取右上方工具列中的 [Train] \(定型\) 來進行應用程式定型。 

1. 選取右側工具列中的 [Test] \(測試\) 來測試新的意圖。 

1. 在文字方塊中，輸入表達取消的語句：

    |測試語句|預測分數|
    |--|:--|
    |我想要取消我的航班。|0.67|
    |取消購買。|0.52|
    |取消會議。|0.56|

    ![測試預先建置的意圖](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>新增預先建置的實體

1. 在 [我的應用程式] 頁面上按一下應用程式名稱來開啟應用程式，然後按一下左側的 [實體]。 

1. 在 [實體] 頁面上，按一下 [先增預先建置的實體]。

1. 在 [Add prebuilt entities] \(新增預先建置的實體\) 對話方塊中，選取 [datetimeV2] 預先建置的實體。 

    ![新增預先建置的實體對話方塊](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. 選取 [完成]。 新增實體之後，您不需要進行應用程式定型。 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>發行以從預測端點中查看預先建立的模型

若要查看預建模型的值，最簡單的方式是從已發行的端點進行查詢。 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>標示包含預先建置實體權杖的實體
 如果您有想要標示為自訂實體的文字，例如 `HH-1234`，_且_您已將[預先建置的數字](luis-reference-prebuilt-number.md)新增至模型，您將無法在 LUIS 入口網站中標示自訂實體。 您可以使用 API 來標示它。 

 若要標示這種類型的權杖，其中一部分已使用預先建置的實體進行標示，請從 LUIS 應用程式移除預先建置的實體。 您不需要定型應用程式。 然後使用您自己的自訂實體標示權杖。 然後將預先建置的實體新增回 LUIS 應用程式。

 針對另一個範例，請將語句視為類別喜好設定的清單： `I want first year spanish, second year calculus, and fourth year english lit.` 如果 LUIS 應用程式已新增建置前序數，`first`、`second`和 `fourth` 將已標記為序數。 如果您想要擷取序數和類別，您可以建立複合實體，並將其包裝在預先建置序數和自訂實體作為類別名稱。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用 REST Api 從 .csv 建立模型](./luis-tutorial-node-import-utterances-csv.md)
