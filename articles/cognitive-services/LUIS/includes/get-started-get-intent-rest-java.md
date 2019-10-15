---
title: 在 Java 中使用 REST 呼叫取得意圖
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838562"
---
## <a name="prerequisites"></a>必要條件

* [JDK SE](https://aka.ms/azure-jdks) (英文) (Java 開發套件，標準版)
* [Visual Studio Code](https://code.visualstudio.com/) 或您最愛的 IDE
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>以程式設計方式取得意圖

您可以使用 Java 來存取您在上一個步驟的瀏覽器視窗中看到的相同結果。 務必將 Apache 程式庫新增至您的專案。

1. 複製下列程式碼，以在名為 `LuisGetRequest.java` 的檔案中建立類別：

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. 以您的 LUIS 金鑰取代 `YOUR-KEY` 變數的值。

3. 取代為您的檔案路徑，並從命令列編譯 Java 程式：`javac -cp .;<FILE_PATH>\* LuisGetRequest.java`。

4. 取代為您的檔案路徑，並從命令列執行應用程式：`java -cp .;<FILE_PATH>\* LuisGetRequest.java`。 它會顯示您稍早在瀏覽器視窗中看到的相同 JSON。

    ![主控台視窗會顯示來自 LUIS 的 JSON 結果](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清除資源

當您完成本快速入門時，請關閉 Visual Studio 專案，並從檔案系統中移除專案目錄。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Java 新增表達並定型](../luis-get-started-java-add-utterance.md)