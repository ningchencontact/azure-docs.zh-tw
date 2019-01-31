---
title: 取得意圖 (Python)
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在本快速入門中，會將將語句傳遞至 LUIS 端點，並取回意圖和實體。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 93ae7e0cc6898648e8b49677070512b919890d29
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218218"
---
# <a name="quickstart-get-intent-using-python"></a>快速入門：使用 Python 取得意圖
在本快速入門中，會將將語句傳遞至 LUIS 端點，並取回意圖和實體。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>必要條件

* [Python 3.6](https://www.python.org/downloads/) 或更新版本。
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用瀏覽器取得意圖

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>以程式設計方式取得意圖

您可以使用 Python 來存取您在上一個步驟的瀏覽器視窗中看到的相同結果。

1. 將下列其中一個程式碼片段，複製到名為 `quickstart-call-endpoint.py` 的檔案：

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. 以您的 LUIS 端點金鑰，取代 `Ocp-Apim-Subscription-Key` 欄位的值。

3. 使用 `pip install requests` 安裝相依性。

4. 使用 `python ./quickstart-call-endpoint.py` 執行指令碼。 它會顯示您稍早在瀏覽器視窗中看到的相同 JSON。

## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清除資源
刪除 Python 檔案。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增表達方式](luis-get-started-python-add-utterance.md)
