---
title: 使用 PHP 來分析 Language Understanding (LUIS) 中自然語言文字的快速入門 - 認知服務 - Azure 認知服務 | Microsoft Docs
description: 在本快速入門中，使用可用的公用 LUIS 應用程式，從交談文字判斷使用者的意圖。 使用 PHP，以文字形式將使用者的意圖傳送至公用應用程式的 HTTP 預測端點。 在端點，LUIS 會套用公用應用程式的模型來分析自然語言文字的意義、判斷整體意圖，並擷取與應用程式主體網域相關的資料。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 14ac32ffc0f4332d0cbc0da59a55ccc500e216d7
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2018
ms.locfileid: "43750311"
---
# <a name="quickstart-analyze-text-using-php"></a>快速入門：使用 PHP 分析文字

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>必要條件

* [PHP](http://php.net/) (英文) 程式設計語言
* [Visual Studio Code](https://code.visualstudio.com/)
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>使用瀏覽器來分析文字

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-php"></a>使用 PHP 來分析文字 

您可以使用 PHP 來存取您在上一個步驟的瀏覽器視窗中看到的相同結果。 

1. 複製後面的程式碼並且以檔案名稱 `endpoint-call.php` 儲存：

   [!code-php[PHP code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/php/endpoint-call.php)]

2. 使用您的端點金鑰取代 `"YOUR-KEY"`。

3. 使用 `php endpoint-call.php` 執行 PHP 應用程式。 它會顯示您稍早在瀏覽器視窗中看到的相同 JSON。

## <a name="luis-keys"></a>LUIS 金鑰

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清除資源

刪除 PHP 檔案。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增表達方式](luis-get-started-php-add-utterance.md)