---
title: Language Understanding 的預建實體
titleSuffix: Azure Cognitive Services
description: LUIS 包含一組預先建置的實體，用於辨識常見的資訊類型，例如日期、時間、數字、計量及貨幣。 預先建置的實體支援會因 LUIS 應用程式的文化特性而異。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: db0fb8962030b536b6ea73f4141da551434cd528
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560348"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>預先建置的實體可辨識常見的資料類型

LUIS 包含一組預先建置的實體，用於辨識常見的資訊類型，例如日期、時間、數字、計量及貨幣。 

## <a name="add-a-prebuilt-entity"></a>新增預先建置的實體

1. 在 [我的應用程式]  頁面上按一下應用程式名稱來開啟應用程式，然後按一下左側的 [實體]  。 

1. 在 [實體]  頁面上，按一下 [先增預先建置的實體]  。

1. 在 [Add prebuilt entities] \(新增預先建置的實體\)  對話方塊中，選取 [datetimeV2] 預先建置的實體。 

    ![新增預先建置的實體對話方塊](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. 選取 [完成]。

## <a name="publish-the-app"></a>發佈應用程式

檢視預先建置之實體值的最簡單方式就是從已發佈的端點查詢。 

1. 在頂端工具列中，選取 [Publish] \(發佈\)  。 請發佈至 [Production] \(生產環境\)  。 

1. 出現綠色成功通知時，選取 [Refer to the list of endpoints] \(參考端點清單\)  連結來查看端點。

1. 選取端點。 隨即會有新瀏覽器索引標籤開啟至該端點。 請將瀏覽器索引標籤保持開啟，然後繼續進行＜測試＞  一節。

## <a name="test"></a>測試
新增實體之後，您不需要進行應用程式定型。 

藉由為 **q** 參數新增一個值，在端點測試新的意圖。 請使用以下針對 **q** 提供建議語句的表格：

|測試語句|實體值|
|--|:--|
|預訂明天的班機|2018-10-19|
|取消 3 月 3 日的約會|LUIS 傳回的是過去時間中最接近的 3 月 3 日 (2018-03-03) 及未來的 3 月 3 日 (2019-03-03)，因為語句中並未指定年份。|
|排定 10 點的會議|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>標示包含預先建置實體權杖的實體
 如果您有想要標示為自訂實體的文字，例如 `HH-1234`，_且_您已將[預先建置的數字](luis-reference-prebuilt-number.md)新增至模型，您將無法在 LUIS 入口網站中標示自訂實體。 您可以使用 API 來標示它。 

 若要標示這種類型的權杖，其中一部分已使用預先建置的實體進行標示，請從 LUIS 應用程式移除預先建置的實體。 您不需要定型應用程式。 然後使用您自己的自訂實體標示權杖。 然後將預先建置的實體新增回 LUIS 應用程式。

 針對其他案例，請將語句視為類別喜好設定的清單：`I want first year spanish, second year calculus, and fourth year english lit.` 如果 LUIS 應用程式已新增預先建置序數，`first`、`second` 及 `fourth` 就會以序數標示。 如果您想要擷取序數和類別，您可以建立複合實體，並將其包裝在預先建置序數和自訂實體作為類別名稱。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [預先建置的實體參考](./luis-reference-prebuilt-entities.md)
