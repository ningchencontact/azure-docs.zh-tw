---
title: 使用 Javascript 來分析 Language Understanding (LUIS) 中的自然語言文字 - 認知服務 - Azure 認知服務 | Microsoft Docs
description: 在本快速入門中，使用可用的公用 LUIS 應用程式，從交談文字判斷使用者的意圖。 使用 Javascript，以文字形式將使用者的意圖傳送至公用應用程式的 HTTP 預測端點。 在端點，LUIS 會套用公用應用程式的模型來分析自然語言文字的意義、判斷整體意圖，並擷取與應用程式主體網域相關的資料。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 10210c3759611a77c4430a97896a19a6b97b9fa9
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2018
ms.locfileid: "43750314"
---
# <a name="quickstart-analyze-text-using-javascript"></a>快速入門：使用 Javascript 來分析文字

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>必要條件

* [Visual Studio Code](https://code.visualstudio.com/)
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>使用瀏覽器來分析文字

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>使用 Javascript 來分析文字 

您可以使用 Javascript，來存取您在上一個步驟的瀏覽器視窗中所看到的相同結果。 

1. 複製後面的程式碼並將它儲存成 HTML 檔案：

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. 在瀏覽器中開啟檔案。 在表單中輸入您的 LUIS 端點金鑰，然後選取 [提交]。

    ![Html 範例會在瀏覽器中顯示，具有「家庭自動化」應用程式的 LUIS 結果](./media/luis-get-started-js-get-intent/html-results.png)

    結果會在表單下顯示。 

## <a name="luis-keys"></a>LUIS 金鑰

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清除資源

刪除 Javascript 檔案。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [新增表達方式](luis-get-started-javascript-add-utterance.md)
