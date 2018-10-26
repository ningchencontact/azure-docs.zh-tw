---
title: 翻譯文字 API 轉換方法
titlesuffix: Azure Cognitive Services
description: 使用翻譯文字 API 轉換方法。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 613cdd14ad196058458b090024cc6b9a4b8a80b6
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018616"
---
# <a name="translator-text-api-30-transliterate"></a>翻譯文字 API 3.0：轉換

將某種語言的文字從某個指令碼轉換成另一個指令碼。

## <a name="request-url"></a>要求 URL

將 `POST` 要求傳送至：

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>要求參數

在查詢字串上傳遞的要求參數為：

<table width="100%">
  <th width="20%">查詢參數</th>
  <th>說明</th>
  <tr>
    <td>api-version</td>
    <td>必要參數。<br/>用戶端要求的 API 版本。 值必須為 `3.0`。</td>
  </tr>
  <tr>
    <td>語言</td>
    <td>必要參數。<br/>指定要從某個指令碼轉換成另一個指令碼的文字語言。 向服務查詢其[支援語言](.\v3-0-languages.md)而取得的 `transliteration` 範圍列出可能的語言。</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*必要參數*。<br/>指定輸入文字所使用的指令碼。 使用 `transliteration` 範圍查閱[支援語言](.\v3-0-languages.md)，以尋找可用於所選取語言的輸入指令碼。</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*必要參數*。<br/>指定輸出指令碼。 使用 `transliteration` 範圍查閱[支援的語言](.\v3-0-languages.md)，以尋找可用於所選取之輸入語言和輸入指令碼組合的輸出指令碼。</td>
  </tr>
</table> 

要求標頭包括：

<table width="100%">
  <th width="20%">headers</th>
  <th>說明</th>
  <tr>
    <td>_一個授權_<br/>_標頭_</td>
    <td>必要的要求標頭。<br/>請參閱[可用的驗證選項](./v3-0-reference.md#authentication)。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>必要的要求標頭。<br/>指定承載的內容類型。 可能的值為：`application/json`。</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>必要的要求標頭。<br/>要求本文的長度。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*選擇性*。<br/>用於識別唯一要求的 GUID，由用戶端產生。 請注意，若您使用名為 `ClientTraceId` 的查詢參數在查詢字串中包含追蹤識別碼，您就可以省略此標頭。</td>
  </tr>
</table> 

## <a name="request-body"></a>Request body

要求的本文是 JSON 陣列。 每個陣列項目都是字串屬性名為 `Text` 的 JSON 物件，其代表要轉換的字串。

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

套用下列限制：

* 陣列最多可以有 10 個項目。
* 陣列項目的文字值不能超過 1,000 個字元，包括空格。
* 要求中包含的完整文字不能超過 5,000 個字元，包括空格。

## <a name="response-body"></a>Response body

成功的回應是輸入陣列的每個字串各有一個結果的 JSON 陣列。 結果物件包含下列屬性：

  * `text`：字串，這是將輸入字串轉換成輸出指令碼的結果。
  
  * `script`：字串，指定在輸出中所使用的指令碼。

範例 JSON 回應如下：

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>回應標頭

<table width="100%">
  <th width="20%">headers</th>
  <th>說明</th>
  <tr>
    <td>X-RequestId</td>
    <td>服務產生的值，用於識別要求。 作為疑難排解之用。</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>回應狀態碼

以下是要求傳回的可能 HTTP 狀態碼。 

<table width="100%">
  <th width="20%">狀態碼</th>
  <th>說明</th>
  <tr>
    <td>200</td>
    <td>成功。</td>
  </tr>
  <tr>
    <td>400</td>
    <td>缺少其中一個查詢參數，或查詢參數無效。 請先修正要求參數再重試。</td>
  </tr>
  <tr>
    <td>401</td>
    <td>無法驗證要求。 請確認認證已指定且有效。</td>
  </tr>
  <tr>
    <td>403</td>
    <td>要求未經授權。 請查看詳細錯誤訊息。 這通常表示試用訂用帳戶提供的所有免費翻譯都已用完。</td>
  </tr>
  <tr>
    <td>429</td>
    <td>呼叫者傳送過多要求。</td>
  </tr>
  <tr>
    <td>500</td>
    <td>發生意外錯誤。 若錯誤仍然存在，請回報：失敗的日期和時間、來自回應標頭 `X-RequestId` 的要求識別碼，以及來自要求標頭 `X-ClientTraceId` 的用戶端識別碼。</td>
  </tr>
  <tr>
    <td>503</td>
    <td>暫時無法使用伺服器。 重試要求。 若錯誤仍然存在，請回報：失敗的日期和時間、來自回應標頭 `X-RequestId` 的要求識別碼，以及來自要求標頭 `X-ClientTraceId` 的用戶端識別碼。</td>
  </tr>
</table> 

## <a name="examples"></a>範例

下列範例示範如何將兩個日文字串轉換成羅馬拼音的日文。

# <a name="curltabcurl"></a>[curl](#tab/curl)

此範例中要求的 JSON 酬載：

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

若您要在不支援 Unicode 字元的命令列視窗中使用 cURL，請採用下列 JSON 承載，並將它儲存成名為 `request.txt` 的檔案。 請務必使用 `UTF-8` 編碼儲存該檔案。

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
