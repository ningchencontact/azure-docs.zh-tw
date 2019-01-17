---
title: Azure Data Factory 中的 Azure 函式活動 | Microsoft Docs
description: 了解如何使用 Azure 函式活動在 Data Factory 管線中執行 Azure 函式
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: ee99733440d74424f98a2ed16de83c88bae53ff1
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321784"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory 中的 Azure 函式活動

Azure 函式活動可讓您在 Data Factory 管線中執行 [Azure 函式](../azure-functions/functions-overview.md)。 若要執行 Azure 函式，您需要建立連結服務連線和可指定所打算執行 Azure 函式的活動。

如需此功能的簡介與示範，請觀看下列 8 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 函式連結服務

Azure 函式的傳回型別必須是有效的 `JObject`。 (請記住，[JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)「不是」一個`JObject`)。`JObject` 以外的所有傳回型別都會失敗，並引發「呼叫端點時發生錯誤」一般使用者錯誤。

| **屬性** | **說明** | **必要** |
| --- | --- | --- |
| type   | 類型屬性必須設定為：**AzureFunction** | 是 |
| 函式應用程式 URL | Azure 函式應用程式的 URL。 格式為 `https://<accountname>.azurewebsites.net`。 此 URL 是您在 Azure 入口網站中檢視函式應用程式時位於 [URL] 區段底下的值  | 是 |
| 函式金鑰 | Azure 函式的存取金鑰。 按一下個別函式的 [管理] 區段，然後複製 [函式金鑰] 或 [主機金鑰]。 前往此處深入了解：[Azure Functions HTTP 觸發程序和繫結](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | 是 |
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

## <a name="more-info"></a>其他資訊

Azure 函式活動支援**路由**。 例如，如果您的應用程式使用下列路由 - `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>` - 則 `functionName` 是 `functionName/{value}`，您可將其參數化以在執行階段提供所需的 `functionName`。

Azure 函式活動也支援**查詢**。 查詢必須是 `functionName` 的一部分 - 例如，`HttpTriggerCSharp2?name=hello` - 其中 `function name` 是 `HttpTriggerCSharp2`。

## <a name="next-steps"></a>後續步驟

在 [Azure Data Factory 中的管線和活動](concepts-pipelines-activities.md)中深入了解 Data Factory 中的活動。
