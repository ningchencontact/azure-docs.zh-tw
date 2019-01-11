---
title: 端點區域，Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: 使用 Node.js，以端點金鑰和 LUIS 的應用程式識別碼尋找發佈區域。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 4d14569219c8db503fc91f52a6867de85373aa05
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724380"
---
# <a name="programmatically-find-endpoint-region-with-nodejs"></a>使用 Node.js 以程式設計方式尋找端點區域
如果您有 LUIS 應用程式識別碼和 LUIS 訂用帳戶識別碼，可以尋找哪個區域要用於端點查詢。

> [!NOTE] 
> 從 [**Azure-Samples** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/find-region/nodejs/)可取得完整的 Node.js 解決方案。

## <a name="luis-endpoint-query-strategy"></a>LUIS 端點查詢策略
每個 LUIS 端點查詢都需要：

* 端點金鑰
* 應用程式識別碼
* 區域

如果 LUIS 端點查詢使用正確的端點金鑰和應用程式識別碼，但使用錯誤的區域，則回應碼為 401。 401 要求不會計入訂用帳戶配額。 將此要求變成策略，以輪詢所有區域，進而尋找正確的區域。 正確的區域是唯一傳回 2xx 狀態碼的要求。 

|Response code|參數|
|--|--|
|2xx|正確的端點金鑰<br>正確的應用程式識別碼<br>正確的主機區域|
|401|正確的端點金鑰<br>正確的應用程式識別碼<br>_正確的_主機區域|

## <a name="nodejs-code-to-find-region"></a>用以尋找區域的 Node.js 程式碼
主控台應用程式會採用 LUIS 應用程式識別碼和端點金鑰，並傳回與其相關聯的所有區域。 目前，端點金鑰是依照區域來建立的，所以只能傳回一個區域。

包括 NPM 相依性：

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

新增常數。 將 `subscriptionKey` 和 `appId` 的變數值取代為您自己的值。  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

新增 `searchRegions` 以尋找區域。 所有不正確的區域都會傳回 401 (已攔截並忽略)。

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

呼叫 `searchRegions` 函式並傳回單一區域：

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

執行應用程式時，終端機會顯示應用程式識別碼的區域。

![顯示 LUIS 區域的主控台應用程式螢幕擷取畫面](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>後續步驟

深入了解 LUIS [區域](luis-reference-regions.md)。
