---
title: 取得意圖 (Node.js)
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在本快速入門中，使用可用的公用 LUIS 應用程式，從交談文字判斷使用者的意圖。 使用 Node.js，以文字形式將使用者的意圖傳送至公用應用程式的 HTTP 預測端點。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 92e10b1f4ec8be1dc67ff449df32ef76e365b5f2
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162659"
---
# <a name="quickstart-get-intent-using-nodejs"></a>快速入門：使用 Node.js 取得意圖

在本快速入門中，會將將語句傳遞至 LUIS 端點，並取回意圖和實體。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>必要條件

* [Node.js](https://nodejs.org/) (英文) 程式設計語言 
* [Visual Studio Code](https://code.visualstudio.com/)
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> 從 [**LUIS-Samples** GitHub 存放庫](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node)可取得完整的 Node.js 解決方案。

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用瀏覽器取得意圖

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>以程式設計方式取得意圖

您可以使用 Node.js 來存取您在上一個步驟的瀏覽器視窗中看到的相同結果。

1. 複製下列程式碼片段：

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. 建立包含下列文字的 `.env` 檔案，或在系統環境中設定這些變數：

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. 將 `LUIS_ENDPOINT_KEY` 環境變數設為您的金鑰。

4. 藉由在命令列執行下列命令來安裝相依性：`npm install`。

5. 使用 `npm start` 來執行程式碼。 它會顯示您稍早在瀏覽器視窗中看到的相同值。

## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清除資源

刪除 Node.js 檔案。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [新增表達方式](luis-get-started-node-add-utterance.md)
