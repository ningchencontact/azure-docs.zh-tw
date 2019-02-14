---
title: 取得意圖 (Java)
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在這個 Java 快速入門中，使用可用的公用 LUIS 應用程式，從交談文字判斷使用者的用意。
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: f7ecf2aaedfda9f7c78146b6e355cf4d8be02003
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879723"
---
# <a name="quickstart-get-intent-using-java"></a>快速入門：使用 Java 取得意圖

在本快速入門中，會將將語句傳遞至 LUIS 端點，並取回意圖和實體。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>必要條件

* [JDK SE](https://aka.ms/azure-jdks) (英文) (Java 開發套件，標準版)
* [Visual Studio Code](https://code.visualstudio.com/)
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用瀏覽器取得意圖

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>以程式設計方式取得意圖 

您可以使用 Java 來存取您在上一個步驟的瀏覽器視窗中看到的相同結果。 

1. 複製下列程式碼，以在名為 `LuisGetRequest.java` 的檔案中建立類別：

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. 以您的 LUIS 金鑰取代 `YOUR-KEY` 變數的值。

3. 使用 `javac -cp ":lib/*" LuisGetRequest.java` 來編譯 java 程式。 

4. 使用 `java -cp ":lib/*" LuisGetRequest.java`執行應用程式。 它會顯示您稍早在瀏覽器視窗中看到的相同 JSON。

    ![主控台視窗會顯示來自 LUIS 的 JSON 結果](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清除資源

刪除 Java 檔案。 

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [新增表達方式](luis-get-started-java-add-utterance.md)
