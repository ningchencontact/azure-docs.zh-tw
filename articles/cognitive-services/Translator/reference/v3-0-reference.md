---
title: 翻譯工具文字 API V3.0 參考
titleSuffix: Azure Cognitive Services
description: 翻譯工具文字 API V3.0 參考文件。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: cb5a3b8572cebfd6c0731a9e572e966fda280be6
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772794"
---
# <a name="translator-text-api-v30"></a>Microsoft Translator Text API v3.0

## <a name="whats-new"></a>新功能

第 3 版翻譯工具文字 API 提供最新 JSON 型 Web API。 其藉由將現有功能合併成較少的作業來提升可用性和效能，並提供新功能。

 * 轉換功能，可將某種語言的文字從某個字集轉換成另一個字集。
 * 透過一個要求即可翻譯成多種語言。
 * 透過一個要求即可進行語言偵測、翻譯和轉換。
 * 可查閱字詞替代翻譯、找到反向翻譯及顯示字詞實際應用範例的字典。
 * 包含更多資訊的語言偵測結果。

## <a name="base-urls"></a>基底 URL

Microsoft Translator 透過多個資料中心位置來提供服務。 目前位於10個[Azure 地理](https://azure.microsoft.com/global-infrastructure/regions)位置:

* **美洲：** 美國東部、美國中南部、美國中西部和美國西部2 
* **亞太地區：** 南韓南部、日本東部、東南亞和澳大利亞東部
* **歐洲︰** 北歐和西歐

對於 Microsoft Translator Text API 的要求大多會由最接近要求發起來源的資料中心負責處理。 如果發生資料中心失敗, 要求可能會在 Azure 地理位置外部路由傳送。

若要強制由特定 Azure 地理位置處理要求, 請將 API 要求中的全域端點變更為所需的區域端點:

|描述|Azure 地理位置|基底 URL|
|:--|:--|:--|
|Azure|全域 (非區域)|   api.cognitive.microsofttranslator.com|
|Azure|美國|   api-nam.cognitive.microsofttranslator.com|
|Azure|歐洲|  api-eur.cognitive.microsofttranslator.com|
|Azure|亞太地區|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>驗證

訂閱翻譯工具文字 API 或認知服務 Microsoft 認知服務中的[多服務](https://azure.microsoft.com/pricing/details/cognitive-services/), 並使用您的訂用帳戶金鑰 (可在 Azure 入口網站中取得) 來進行驗證。 

有三個標頭可供用來驗證您的訂用帳戶。 下表提供其各自的使用方式說明：

|標頭|描述|
|:----|:----|
|Ocp-Apim-Subscription-Key|如果您要傳遞祕密金鑰，請使用認知服務訂用帳戶。<br/>此值是您 Translator Text API 訂用帳戶的 Azure 祕密金鑰。|
|Authorization|如果您要傳遞驗證權杖，請使用認知服務訂用帳戶。<br/>此值是持有人權杖：`Bearer <token>`。|
|Ocp-Apim-Subscription-Region|*如果您要傳遞多服務秘密金鑰, 請搭配使用與認知服務多服務訂用帳戶。*<br/>此值是多服務訂用帳戶的區域。 當不使用多服務訂用帳戶時, 這個值是選擇性的。|

###  <a name="secret-key"></a>祕密金鑰
第一個選項是使用 `Ocp-Apim-Subscription-Key` 標頭來進行驗證。 只需在要求中新增 `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` 標頭即可。

### <a name="authorization-token"></a>授權權杖
或者，您可以用秘密金鑰交換存取權杖。 此權杖會隨附在每個要求中作為 `Authorization` 標頭。 若要取得授權權杖，請對下列 URL 提出 `POST` 要求：

| 環境     | 驗證服務 URL                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

以下要求範例示範如何取得具有祕密金鑰的權杖：

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

成功的要求會以純文字形式在回應本文中傳回已編碼的存取權杖。 有效的權杖會傳遞至翻譯工具服務，以作為授權的持有人權杖。

```
Authorization: Bearer <Base64-access_token>
```

驗證權杖的有效時間為 10 分鐘。 對翻譯工具 API 執行多次呼叫時，應重複使用權杖。 不過，如果您的程式會在超過一段時間後才對翻譯工具 API 提出要求，則您的程式必須以固定間隔 (例如每 8 分鐘) 要求新存取權杖。

### <a name="multi-service-subscription"></a>多服務訂用帳戶

最後一個驗證選項是使用認知服務的多服務訂用帳戶。 這可讓您使用單一祕密金鑰來驗證多個服務的要求。 

當您使用多服務秘密金鑰時, 您必須在要求中包含兩個驗證標頭。 第一個標頭傳遞祕密金鑰，第二個標頭指定與訂用帳戶相關聯的區域。 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

多服務文字 API 訂用帳戶需要區域。 您選取的區域是您在使用多服務訂用帳戶金鑰時, 可以用於文字翻譯的唯一區域, 而且必須是您透過 Azure 入口網站註冊多服務訂用帳戶時所選取的相同區域。

可用的區域`australiaeast`為`brazilsouth`、 `canadacentral`、 `centralindia` 、、`centralus`、 `centraluseuap`、 、、`eastus`、、、、 `eastasia` `eastus2` `francecentral` `japaneast` `japanwest` `koreacentral`、 、`northcentralus` 、、`southeastasia`、 、、`uksouth`、、和`southafricanorth`。 `southcentralus` `northeurope` `westcentralus` `westeurope` `westus` `westus2`

如果您在查詢字串中使用參數 `Subscription-Key` 傳遞祕密金鑰，則必須使用查詢參數 `Subscription-Region` 來指定區域。

如果您使用持有人權杖，則必須從區域端點取得權杖：`https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`。


## <a name="errors"></a>錯誤

標準錯誤回應是名稱/值組為 `error` 的 JSON 物件。 此值也可以是具有下列屬性的 JSON 物件：

  * `code`:伺服器定義的錯誤碼。

  * `message`:以人類可閱讀形式表示錯誤的字串。

例如，持有免費試用訂用帳戶的客戶會在免費配額用完時，收到下列錯誤：

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
錯誤碼是 6 位數的數字，其中結合了 3 位數的 HTTP 狀態碼，後面接著將錯誤進一步分類的 3 位數數字。 常見的錯誤碼包括：

| 程式碼 | 描述 |
|:----|:-----|
| 400000| 其中一個要求輸入無效。|
| 400001| "scope" 參數無效。|
| 400002| "category" 參數無效。|
| 400003| 語言指定名稱遺漏或無效。|
| 400004| 目標指令碼指定名稱 ("To script") 遺漏或無效。|
| 400005| 輸入文字遺漏或無效。|
| 400006| 語言與指令碼的組合無效。|
| 400018| 來源指令碼指定名稱 ("From script") 遺漏或無效。|
| 400019| 不支援其中一個指定的語言。|
| 400020| 輸入文字陣列中的其中一個元素無效。|
| 400021| API 版本參數遺漏或無效。|
| 400023| 其中一個指定的語言組無效。|
| 400035| 來源語言 ("From" 欄位) 無效。|
| 400036| 目標語言 ("To" 欄位) 無效。|
| 400042| 其中一個指定的選項 ("Options" 欄位) 無效。|
| 400043| 用戶端追蹤識別碼 ID (ClientTraceId 欄位或 X-ClientTranceId 標頭) 遺漏或無效。|
| 400050| 輸入文字太長。 View[要求限制](../request-limits.md)。|
| 400064| "translation" 參數遺漏或無效。|
| 400070| 目標指令碼 (ToScript 參數) 數目與目標語言 (To 參數) 數目不符。|
| 400071| 值不是有效的 TextType 值。|
| 400072| 輸入文字陣列的元素太多。|
| 400073| 指令碼參數無效。|
| 400074| 要求本文不是有效的 JSON。|
| 400075| 語言組與類別組合無效。|
| 400077| 已超過要求大小上限。 View[要求限制](../request-limits.md)。|
| 400079| 所要求用來在來源與目標語言之間進行翻譯的自訂系統不存在。|
| 400080| 語言或腳本不支援音譯。|
| 401000| 要求未獲授權，因為認證遺漏或無效。|
| 401015| 「提供的認證是 Speech API 的認證。 此要求需要的是「文字 API」的認證。 請使用「翻譯工具文字 API」的訂用帳戶。」|
| 403000| 不允許此作業。|
| 403001| 不允許此作業，因為訂用帳戶已超出其可用配額。|
| 405000| 要求方法不是所要求資源支援的方法。|
| 408001| 正在準備所要求的轉譯系統。 請稍待數分鐘後重試。|
| 408002| 要求已等候傳入串流。 用戶端未在伺服器準備等候的時間內產生要求。 用戶端可能會在不修改的時間之後, 重複要求。|
| 415000| Content-Type 標頭遺漏或無效。|
| 429000、429001、429002| 伺服器已拒絕要求, 因為用戶端已超過要求限制。|
| 500000| 發生意外錯誤。 如果錯誤持續存在，請回報錯誤並提供錯誤的日期/時間、來自回應標頭 X-RequestId 的要求識別碼，以及來自要求標頭 X-ClientTraceId 的用戶端識別碼。|
| 503000| 服務暫時無法使用。 請再試一次。 如果錯誤持續存在，請回報錯誤並提供錯誤的日期/時間、來自回應標頭 X-RequestId 的要求識別碼，以及來自要求標頭 X-ClientTraceId 的用戶端識別碼。|

