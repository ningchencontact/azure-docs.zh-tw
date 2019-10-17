---
title: 開發人員資源-Language Understanding
titleSuffix: Azure Cognitive Services
description: 開發人員有 REST Api 和 Language Understanding 的 Sdk。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 8accac7fe6068007180403fdab27013da161b28c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437184"
---
# <a name="developer-resources-for-language-understanding"></a>Language Understanding 的開發人員資源

開發人員可以同時使用 REST Api 和 Sdk 進行 Language Understanding。 

## <a name="azure-resource-management"></a>Azure 資源管理

使用 Azure 認知服務管理層來建立、編輯、列出及刪除 Language Understanding 或認知服務資源。

尋找以工具為基礎的參考檔：

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding 撰寫和預測要求

Language Understanding 服務會從您需要建立的 Azure 資源進行存取。 有兩個資源：撰寫和預測端點資源。 這兩個資源都可讓您控制您的 LUIS 資源。 

深入瞭解[V3 預測端點](luis-migration-api-v3.md)。

### <a name="rest-apis"></a>REST API

撰寫和預測端點 API 都可從 REST Api 取得：

* 撰寫[參考檔](https://go.microsoft.com/fwlink/?linkid=2092087)
* 預測執行時間[參考檔](https://go.microsoft.com/fwlink/?linkid=2092356)

### <a name="language-based-sdks"></a>以語言為基礎的 Sdk

|語言 |參考文件|Package|範例|快速入門|
|--|--|--|--|--|
|C#|[編寫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[預測](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 製作](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 預測](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[建立和管理應用程式](sdk-csharp-quickstart-authoring-app.md)<br>[查詢預測端點](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[撰寫和預測](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[使用 REST 撰寫](luis-get-started-go-add-utterance.md)<br>[使用 REST 進行預測](luis-get-started-go-get-intent.md)|
|Java|[撰寫和預測](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven 撰寫](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 預測](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[編寫](luis-get-started-java-add-utterance.md)<br>[預測](luis-get-started-java-get-intent.md)
|Node.js|[編寫](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[預測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 撰寫](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM 預測](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[編寫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[預測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[使用 REST 撰寫](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[使用 REST 進行預測](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[撰寫和預測](sdk-python-quickstart-authoring-app.md)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[編寫](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[編寫](sdk-python-quickstart-authoring-app.md)<br>[使用 REST 進行預測](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>其他工具和 Sdk

Bot framework 提供各種語言的[SDK](https://github.com/Microsoft/botframework) ，以及使用[Azure bot service](https://dev.botframework.com/)做為服務。 

Bot framework 提供[數種工具](https://github.com/microsoft/botbuilder-tools)來協助 Language Understanding，包括：

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -使用 markdown 檔案建立 LUIS 語言理解模型
* [LUIS Cli](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -建立和管理您的 LUIS.ai 應用程式
* [分派](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-管理父系和子應用程式
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -自動為您C#的 LUIS 意圖和實體產生支援/Typescript 類別。
* [Bot 模擬器](https://github.com/Microsoft/BotFramework-Emulator/releases)-桌面應用程式，可讓 Bot 開發人員測試和偵測使用 BOT Framework SDK 建立的 bot


## <a name="next-steps"></a>後續步驟

瞭解常見的[HTTP 錯誤碼](luis-reference-response-codes.md)