---
title: 取得意圖 (C#)
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在這個 C# 快速入門中，使用可用的公用 LUIS 應用程式，從交談文字判斷使用者的用意。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 5bb4bbb1c080fcbf1f374c68ede8f28c1e4e3c5f
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853897"
---
# <a name="quickstart-get-intent-using-c"></a>快速入門：使用 C# 取得意圖

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>必要條件

* [Visual Studio Community 2017 版本](https://visualstudio.microsoft.com/vs/community/)
* C# 程式設計語言 (隨附於 VS Community 2017)
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用瀏覽器取得意圖

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>以程式設計方式取得意圖

使用 C# 來查詢預測端點 GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) (英文)，以取得與您在上一節中瀏覽器視窗所見的相同結果。 

1. 在 Visual Studio 中建立新的主控台應用程式。 

    ![在 Visual Studio 中建立新的主控台應用程式](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. 在 Visual Studio 專案的 [方案總管] 中，選取 [新增參考]，然後從 [組件] 索引標籤選取 [System.Web]。

    ![選取 [新增參考]，然後從 [組件] 索引標籤中選取 [System.Web]](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. 使用下列程式碼覆寫 Program.cs：
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. 以您的 LUIS 金鑰取代 `YOUR_KEY` 值。

5. 建置並執行主控台應用程式。 它會顯示您稍早在瀏覽器視窗中看到的相同 JSON。

    ![主控台視窗會顯示來自 LUIS 的 JSON 結果](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清除資源

當您完成本快速入門時，請關閉 Visual Studio 專案，並從檔案系統中移除專案目錄。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 C# 新增語句並且定型](luis-get-started-cs-add-utterance.md)