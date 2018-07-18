---
title: 教學課程：了解如何使用 Node.js 呼叫 Language Understanding Intelligent Services (LUIS) 應用程式 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Node.js 呼叫 LUIS 應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5e9df272be3c48602d38f551a791376c043bf409
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263647"
---
# <a name="tutorial-call-a-luis-endpoint-using-nodejs"></a>教學課程：使用 Node.js 呼叫 LUIS 端點
將語句傳遞至 LUIS 端點，並取回意圖和實體。

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立 LUIS 訂用帳戶並複製金鑰值以供日後使用
> * 從瀏覽器檢視公用範例 IoT 應用程式的 LUIS 端點結果
> * 建立 Visual Studio C# 主控台應用程式，以對 LUIS 端點進行 HTTPS 呼叫

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="create-luis-subscription-key"></a>建立 LUIS 訂用帳戶金鑰
您需要認知服務 API 金鑰，才能呼叫此逐步解說中使用的範例 LUIS 應用程式。 

若要取得 API 金鑰，請遵循下列步驟︰ 

1. 您必須先在 Azure 入口網站中建立[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

2. 在 https://portal.azure.com 上登入 Azure 入口網站。 

3. 請依照[使用 Azure 建立訂用帳戶金鑰](./luis-how-to-azure-subscription.md)中的步驟來取得金鑰。

4. 回到 [LUIS](luis-reference-regions.md) 網站並使用您的 Azure 帳戶登入。 

    [![](media/luis-get-started-node-get-intent/app-list.png "應用程式清單的螢幕擷取畫面")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>了解 LUIS 傳回的內容

若要了解 LUIS 應用程式傳回的內容，您可以將範例 LUIS 應用程式的 URL 貼入瀏覽器視窗。 範例應用程式是一個 IoT 應用程式，可偵測使用者要開啟或關閉燈號。

1. 範例應用程式的端點採用以下格式：`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` 複製此 URL，並以 `subscription-key` 欄位值取代您的訂用帳戶金鑰。
2. 將 URL 貼入瀏覽器視窗，然後按 Enter。 瀏覽器會顯示 JSON 結果，指出 LUIS 偵測到 `HomeAutomation.TurnOn` 意圖和 `HomeAutomation.Room` 實體 (值為 `bedroom`)。

    ![JSON 結果偵測到意圖 TurnOn](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. 將 URL 中的 `q=` 參數值變更為 `turn off the living room light`，然後按 Enter。 結果現在指出 LUIS 偵測到 `HomeAutomation.TurnOff` 意圖和 `HomeAutomation.Room` 實體 (值為 `living room`)。 

    ![JSON 結果偵測到意圖 TurnOn](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-nodejs"></a>使用端點 API 搭配 Node.js 來取用 LUIS 結果

您可以使用 Node.js 來存取您在上一個步驟的瀏覽器視窗中看到的相同結果。

1. 複製下列程式碼片段：

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/endpoint-api-samples/node/call-endpoint.js)]

2. 如程式碼註解中所述，設定 `LUIS_APP_ID` 環境變數。 

3. 將 `LUIS_SUBSCRIPTION_KEY` 環境變數設定為您的認知服務訂用帳戶金鑰。

4. 執行程式碼。 它會顯示您稍早在瀏覽器視窗中看到的相同值。
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-Node.js-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>清除資源
在本教學課程中建立的兩個資源為 LUIS 訂用帳戶金鑰和 C# 專案。 從 Azure 入口網站刪除 LUIS 訂用帳戶金鑰。 關閉 Visual Studio 專案，並從檔案系統中移除目錄。 

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [新增語句](luis-get-started-node-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website