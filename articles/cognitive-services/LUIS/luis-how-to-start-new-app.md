---
title: 使用 LUIS 建立新應用程式 | Microsoft Docs
description: 在 Language Understanding (LUIS) 網頁上建立及管理應用程式。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: diberry
ms.openlocfilehash: 3adeecd4a4e2040a92689b7c92be9630c9a0d93b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225409"
---
# <a name="create-an-app"></a>建立應用程式
您可使用不同的方法來建立新的應用程式： 

* 從空的應用程式[著手](#create-new-app)，並建立意圖、語句和實體。
* 從空的應用程式[著手](#create-new-app)，並新增[預建網域](luis-how-to-use-prebuilt-domains.md)。
* 從已經包含意圖、語句和實體的 JSON 檔案[匯入 LUIS 應用程式](#import-new-app)。

## <a name="what-is-an-app"></a>何謂應用程式
應用程式包含您的模型[版本](luis-how-to-manage-versions.md)，以及應用程式的任何[共同作業者](luis-how-to-collaborate.md)。 建立應用程式時，您選取的文化特性 ([語言](luis-supported-languages.md)) **稍後無法變更**。 

新應用程式的預設版本為 "0.1"。 

您可以在 [我的應用程式] 頁面上建立和管理應用程式。 在 [LUIS](luis-reference-regions.md) 網站的上方瀏覽列上選取 [我的應用程式]一律可以存取此頁面。 

[![](media/luis-create-new-app/apps-list.png "應用程式清單的螢幕擷取畫面")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>建立新的應用程式

1. 在 [我的應用程式] 頁面上，選取 [建立新的應用程式]。
2. 在對話方塊中，將您的應用程式命名為 "TravelAgent"。

    ![建立新的應用程式對話方塊](./media/luis-create-new-app/create-app.png)

3. 選擇您的應用程式文化特性 (對於 TravelAgent 應用程式，選擇 [英文])，然後選取 [完成]。 

    >[!NOTE]
    >建立應用程式之後便無法變更文化特性 (Culture)。 

## <a name="import-new-app"></a>匯入新的應用程式
您可以在 JSON 檔案中設定應用程式的名稱 (最多 50 個字元)、版本 (最多 10 個字元) 及描述。 在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight) 可取得應用程式 JSON 檔案的範例。

1. 在 [我的應用程式] 頁面上，選取 [匯入新的應用程式]。
2. 在 [匯入新的應用程式] 對話方塊中，選取可供定義 LUIS 應用程式的 JSON 檔案。

    ![匯入新的應用程式對話方塊](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>匯出應用程式
1. 在 [我的應用程式] 頁面上，選取應用程式資料列結尾處的省略符號 (***...***)。

    [![](media/luis-create-new-app/apps-list.png "每個應用程式動作的快顯對話方塊螢幕擷取畫面")](media/luis-create-new-app/three-dots.png#lightbox)

2. 從功能表中選取 [匯出應用程式]。 

## <a name="rename-app"></a>將應用程式重新命名

1. 在 [我的應用程式] 頁面上，選取應用程式資料列結尾處的省略符號 (***...***)。 
2. 從功能表中選取 [重新命名]。
3. 輸入新的應用程式名稱，然後選取 [完成]。

## <a name="delete-app"></a>刪除應用程式

> [!CAUTION]
> 您會為所有共同作業者與擁有者刪除此應用程式。 先 [[匯出]](#export-app) 應用程式，然後予以刪除。 

1. 在 [我的應用程式] 頁面上，選取應用程式資料列結尾處的省略符號 (***...***)。 
2. 從功能表中選取 [刪除]。
3. 在確認視窗中選取 [確定]。

## <a name="export-endpoint-logs"></a>匯出端點記錄
記錄包含查詢、UTC 時間和 LUIS JSON 回應。

1. 在 [我的應用程式] 頁面上，選取應用程式資料列結尾處的省略符號 (***...***)。 
2. 從功能表中選取 [匯出端點記錄]。

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>後續步驟

您在應用程式中的第一項工作是[新增對應方式](luis-how-to-add-intents.md)。