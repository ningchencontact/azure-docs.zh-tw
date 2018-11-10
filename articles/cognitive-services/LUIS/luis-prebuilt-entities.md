---
title: Language Understanding (LUIS) 預先建置的實體
titleSuffix: Azure Cognitive Services
description: LUIS 包含一組預先建置的實體，用於辨識常見的資訊類型，例如日期、時間、數字、計量及貨幣。 預先建置的實體支援會因 LUIS 應用程式的文化特性而異。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 0fe9dbed302fd2d61305167a3bda25b1b403b761
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139969"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>預先建置的實體可辨識常見的資料類型

LUIS 包含一組預先建置的實體，用於辨識常見的資訊類型，例如日期、時間、數字、計量及貨幣。 

## <a name="add-a-prebuilt-entity"></a>新增預先建置的實體

1. 在 [我的應用程式] 頁面上按一下應用程式名稱來開啟應用程式，然後按一下左側的 [實體]。 

1. 在 [實體] 頁面上，按一下 [先增預先建置的實體]。

1. 在 [Add prebuilt entities] \(新增預先建置的實體\) 對話方塊中，選取 [datetimeV2] 預先建置的實體。 

    ![新增預先建置的實體對話方塊](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. 選取 [完成] 。

## <a name="publish-the-app"></a>發佈應用程式

檢視預先建置之實體值的最簡單方式就是從已發佈的端點查詢。 

1. 在頂端工具列中，選取 [Publish] \(發佈\)。 請發佈至 [Production] \(生產環境\)。 

1. 出現綠色成功通知時，選取 [Refer to the list of endpoints] \(參考端點清單\) 連結來查看端點。

1. 選取端點。 隨即會有新瀏覽器索引標籤開啟至該端點。 請將瀏覽器索引標籤保持開啟，然後繼續進行＜測試＞一節。

## <a name="test"></a>測試
新增實體之後，您不需要進行應用程式定型。 

藉由為 **q** 參數新增一個值，在端點測試新的意圖。 請使用以下針對 **q** 提供建議語句的表格：

|測試語句|實體值|
|--|:--|
|預訂明天的班機|2018-10-19|
|取消 3 月 3 日的約會|LUIS 傳回的是過去時間中最接近的 3 月 3 日 (2018-03-03) 及未來的 3 月 3 日 (2019-03-03)，因為語句中並未指定年份。|
|排定 10 點的會議|10:00:00|


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [預先建置的實體參考](./luis-reference-prebuilt-entities.md)
