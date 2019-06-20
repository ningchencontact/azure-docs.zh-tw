---
title: Azure Data Factory 中的 Azure 函式活動 | Microsoft Docs
description: 了解如何使用 Azure 函式活動在 Data Factory 管線中執行 Azure 函式
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: dfdfb9e38f16d0077175587933b0800b87cc1931
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144121"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Azure 函式活動

Azure 函式活動可讓您在 Data Factory 管線中執行 [Azure 函式](../azure-functions/functions-overview.md)。 若要執行 Azure 函式，您需要建立連結服務連線和可指定所打算執行 Azure 函式的活動。

如需此功能的簡介與示範，請觀看下列 8 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 函式連結服務

Azure 函式的傳回型別必須是有效的 `JObject`。 (請記住，[JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)「不是」  一個`JObject`)。任何傳回型別以外`JObject`失敗，並引發使用者錯誤*回應內容不是有效的 JObject*。

| **屬性** | **說明** | **必要** |
| --- | --- | --- |
| type   | 類型屬性必須設定為：**AzureFunction** | 是 |
| 函式應用程式 URL | Azure 函式應用程式的 URL。 格式為 `https://<accountname>.azurewebsites.net`。 此 URL 是您在 Azure 入口網站中檢視函式應用程式時位於 [URL]  區段底下的值  | 是 |
| 函式金鑰 | Azure 函式的存取金鑰。 按一下個別函式的 [管理]  區段，然後複製 [函式金鑰]  或 [主機金鑰]  。 前往此處深入了解：[Azure Functions HTTP 觸發程序和繫結](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | 是 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 函式活動

| **屬性**  | **說明** | **允許的值** | **必要** |
| --- | --- | --- | --- |
| name  | 管線中的活動名稱  | 字串 | 是 |
| type  | 活動類型是 ‘AzureFunctionActivity’ | 字串 | 是 |
| 連結服務 | 相對應 Azure 函式應用程式的 Azure 函式連結服務  | 連結服務參考 | 是 |
| 函式名稱  | 此活動會在 Azure 函式應用程式中呼叫的函式名稱 | 字串 | 是 |
| method  | 函式呼叫的 REST API 方法 | 字串支援的類型："GET"、"POST"、"PUT"   | 是 |
| 頁首  | 傳送至要求的標頭。 例如，用來在要求上設定語言和類型："headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 字串 (或含有字串之 resultType 的運算式) | 否 |
| body  | 與要求一起傳送至函式 API 方法的主體  | 字串 (或含有字串之 resultType 的運算式) 或物件。   | PUT/POST 方法的必要項 |
|   |   |   | |

請在 [要求乘載結構描述](control-flow-web-activity.md#request-payload-schema) 一節中查看要求乘載的結構描述。

## <a name="routing-and-queries"></a>路由及查詢

Azure 函式活動支援**路由**。 例如，如果您的 Azure 函式有端點`https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`，則`functionName`若要在 Azure 函式活動中使用`<functionName>/<value>`。 您可以參數化此函式可提供所需`functionName`在執行階段。

Azure 函式活動也支援**查詢**。 查詢必須為一部分`functionName`。 例如，當函式名稱是`HttpTriggerCSharp`且您想要包含的查詢是`name=hello`，然後您可以建構`functionName`做為 Azure 函式活動中`HttpTriggerCSharp?name=hello`。 此函式可參數化，因此值可以在執行階段決定。

## <a name="timeout-and-long-running-functions"></a>逾時和長時間執行的函式

Azure 函式逾時之後 230 秒，不論`functionTimeout`設定中，您已設定的設定。 如需詳細資訊，請參閱 [本篇文章](../azure-functions/functions-versions.md#timeout)。 若要解決這個問題，請遵循非同步模式，或使用長期函式。 Durable Functions 的優點是，他們提供自己的狀態追蹤機制，因此您不需要實作您自己。

深入了解 Durable Functions 中[這篇文章](../azure-functions/durable/durable-functions-overview.md)。 您可以設定 Azure 函式活動來呼叫永久性函式，會將回應傳回具有不同的 URI，例如[本例](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery)。 因為`statusQueryGetUri`傳回 HTTP 狀態 202，而函式正在執行時，您可以使用 Web 活動輪詢函式的狀態。 只要設定 Web 活動，具有`url`欄位設定為`@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`。 長期函式完成時，函式的輸出會是 Web 活動的輸出。


## <a name="sample"></a>範例

您可以找到要解壓縮 tar 檔案解壓縮檔案的內容中使用 Azure Function 的 Data Factory 的範例[此處](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)。

## <a name="next-steps"></a>後續步驟

在 [Azure Data Factory 中的管線和活動](concepts-pipelines-activities.md)中深入了解 Data Factory 中的活動。
