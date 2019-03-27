---
title: 快速入門：使用 LUIS 入口網站部署應用程式
titleSuffix: Language Understanding - Azure Cognitive Services
description: 當應用程式準備好可將表達預測傳回用戶端應用程式 (例如聊天機器人) 後，您必須將應用程式部署至預測端點。 在此快速入門中，您會透過建立預測端點資源、將資源指派給應用程式、將應用程式定型，以及發佈應用程式，了解如何部署應用程式。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cf7464188bb502d77c673284cba226ce91a5725
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259715"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>快速入門：在 LUIS 入口網站中部署應用程式

當應用程式準備好可將表達預測傳回用戶端應用程式 (例如聊天機器人) 後，您必須將應用程式部署至預測端點。 

在此快速入門中，您會透過建立預測端點資源、將資源指派給應用程式、將應用程式定型，以及發佈應用程式，了解如何部署應用程式。 

## <a name="prerequisites"></a>必要條件

* [Azure 訂用帳戶](https://azure.microsoft.com/free)。
* 完成[先前的入口網站快速入門](get-started-portal-build-app.md)，或[下載並匯入應用程式](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)。 


## <a name="create-endpoint-resource"></a>建立端點資源

在 Azure 入口網站中建立預測端點資源。 此資源僅適用於端點預測查詢。 請勿使用此資源撰寫應用程式的變更。 

1. 登入 **[Azure 入口網站](https://ms.portal.azure.com/)**。 

1. 選取左上方面板的綠色 **+** 符號，然後在市集中搜尋 `Cognitive Services`，再加以選取。 

1. 使用下列設定來設定訂用帳戶：

    |設定|值|目的|
    |--|--|--|
    |Name|`my-cognitive-service-resource`|Azure 資源的名稱。 當您將資源指派給 LUIS 入口網站中的應用程式時，會需要此名稱。|
    |訂用帳戶|您的訂用帳戶|選取其中一個與您的帳戶相關聯的訂用帳戶。|
    |位置|美國西部|此資源的 Azure 區域。|
    |定價層|**S0**|此資源的預設定價層。|
    |資源群組|`my-cognitive-service-resource-group`|為所有認知服務資源，建立新的資源群組。 當您完成使用資源後，可以刪除資源群組來清除您的訂用帳戶。 | 

    ![Azure API 選擇](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. 選取 [建立] 以建立 Azure 資源。 

    在下一節中，您將了解如何將這個新資源連線至 LUIS 入口網站中的 LUIS 應用程式。 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>將資源金鑰指派給 LUIS 入口網站中的 LUIS 應用程式

每當您為 LUIS 建立新資源時，都需要將資源指派給 LUIS 應用程式。 指派之後，除非您建立新的資源，否則不需要再次執行此步驟。 您可以建立新的資源，以擴充您的應用程式區域，或支援更多預測查詢數目。 

1. 登入 [LUIS 入口網站](https://www.luis.ai)，從應用程式清單中選擇 **myEnglishApp** 應用程式。

1. 選取右上方功能表中的 [管理]，然後選取 [金鑰和端點]。

1. 若要新增 LUIS，請選取 [指派資源 +]。

    [![將資源指派給應用程式](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. 選取您的租用戶、訂用帳戶及資源名稱，然後選取 [指派資源]。 

    ![將資源指派給應用程式](./media/get-started-portal-deploy-app/assign-resource.png)

1. 在資料表中尋找新的資料列並複製端點 URL。 以正確方式建構，可對 LUIS API 端點提出 HTTP GET 要求進行預測。 

## <a name="train-and-publish-the-app"></a>將應用程式定型並發佈 

每當您準備好進行測試時，都應該將其定型。 每當您想讓用戶端應用程式可從預測端點執行階段使用目前定型的版本時，就應該發佈應用程式。 

1. 如果應用程式仍未定型，請從右上方功能表中選取 [定型]。

1. 從右上方功能表中選取 [發佈]。 接受預設的環境設定，然後選取 [發佈]。

1. 當瀏覽器視窗上方出現綠色成功通知列時，請選取 [參考端點清單] 連結。 

    ![瀏覽器中的成功發佈應用程式通知列](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. 這會帶您前往 [金鑰和端點設定] 頁面。 指派的資源和對應端點 URL 清單位於頁面底部。 

1. 選取與新資源名稱相關聯的端點 URL。 這會以正確建構的 URL 開啟網頁瀏覽器，對預測端點執行階段提出 **GET** 要求。 

1. URL 結尾的 `q=` 是 **query** 的縮寫，而使用者會在此將其表達附加至 GET 要求。 在 `q=` 後面，輸入於先前快速入門結尾使用的相同使用者表達：

    ```Is there a form named hrf-234098```

    瀏覽器回應會和用戶端應用程式收到的 JSON 相同：

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    此回應會比先前教學課程中的預設 [測試] 窗格，提供更詳細的資訊。 如果您想要在 [測試] 窗格中看到和此相同層級的資訊，務必先發佈應用程式。 然後在 [測試] 窗格中，選取 [與已發行比較]。 在發佈的測試窗格中，使用 [顯示 JSON 檢視]，即可查看和上一個步驟相同的 JSON。 這可讓您比較目前正在處理的應用程式和已發佈至端點的應用程式。

    [![比較目前正在編輯與已發佈的應用程式版本](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>清除資源
當您完成本快速入門時，請從頂端導覽功能表中，選取 [我的應用程式]。 然後從清單中選取應用程式左側的核取方塊，再從清單上方的內容工具列，選取 [刪除]。 

[![刪除 [我的應用程式] 清單中的應用程式](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [找出常見的意圖和實體](luis-tutorial-prebuilt-intents-entities.md)