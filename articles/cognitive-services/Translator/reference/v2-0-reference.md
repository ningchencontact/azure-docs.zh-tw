---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: Translator Text API v2.0 的參考文件。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: c18c062d5537603284acb37081ac0a4eb8d2fd20
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797823"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> 這版 Translator Text API 已淘汱。 [檢視第 3 版的 Translator Text API 的文件](v3-0-reference.md)。

第 2 版的 Translator Text API 可以順暢地整合到您的應用程式、 網站、 工具或其他解決方案，以提供多語言使用者體驗。 您可以使用它於任何硬體平台與任何作業系統來執行語言翻譯和其他語言相關工作，例如文字語言偵測和文字轉換語音，根據產業標準。 如需詳細資訊，請參閱 < [Translator Text API](../translator-info-overview.md)。

## <a name="getting-started"></a>使用者入門
若要存取 Translator Text API，您需要[註冊 Microsoft Azure](../translator-text-how-to-signup.md)。

## <a name="authentication"></a>驗證 
Translator Text API 的所有呼叫都需要訂用帳戶金鑰進行驗證。 此 API 支援三種驗證方法：

- 存取權杖。 使用步驟 9 中所參考的訂用帳戶金鑰來建立存取權杖進行，POST 要求來驗證服務。 如需詳細資料，請參閱權杖服務文件。 使用傳遞至轉譯器服務的存取權杖`Authorization`標頭或`access_token`查詢參數。 存取權杖的有效時間為 10 分鐘。 取得新存取權杖每隔 10 分鐘，並使用相同的存取權杖以供重複要求期間保持在 10 分鐘。
- 直接使用訂用帳戶金鑰。 將您的訂用帳戶金鑰傳遞中的值為`Ocp-Apim-Subscription-Key`包含您到 Translator Text API 的要求標頭。 當您直接使用訂用帳戶金鑰時，您不需要呼叫的權杖驗證服務，以建立存取權杖。
- [Azure 認知服務多服務訂用帳戶](https://azure.microsoft.com/pricing/details/cognitive-services/)。 這個方法可讓您使用單一的祕密金鑰來驗證多個服務的要求。
當您使用多服務的祕密金鑰時，您需要包含兩個驗證標頭，與您的要求。 第一個標頭會傳遞的祕密金鑰。 第二個標頭會指定與您的訂用帳戶相關聯的區域：
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

需要多服務的文字 API 訂用帳戶的區域。 選取的區域是當您使用多服務的訂用帳戶金鑰，您可以使用文字翻譯的唯一地區。 它必須是您選取您在 Azure 入口網站上多服務訂用帳戶註冊時的相同區域。

可用的區域都`australiaeast`， `brazilsouth`， `canadacentral`， `centralindia`， `centraluseuap`， `eastasia`， `eastus`， `eastus2`， `japaneast`， `northeurope`， `southcentralus`， `southeastasia`，`uksouth`， `westcentralus`， `westeurope`， `westus`，和`westus2`。

您的訂用帳戶金鑰和存取權杖是應該從檢視隱藏的祕密。

## <a name="profanity-handling"></a>粗話處理
一般來說，轉譯器服務將會保留存在於來源中的不雅內容。 不雅內容和內容，讓文字的不雅的字眼的程度是根據文化特性而有所不同。 因此無法增加或減少的程度不雅內容的目標語言。

如果您想要防止在轉譯的不雅內容，即使它是在原始程式文字中，您可以使用的不雅內容篩選可支援它之方法的選項。 此選項可讓您選擇是否要查看不雅內容刪除，或加上適當的標記，或在目標中允許的不雅內容。 可接受的值`ProfanityAction`都`NoAction`（預設值）， `Marked`，和`Deleted`。


|ProfanityAction    |動作 |範例來源 （日文）  |範例翻譯 （英文）  |
|:--|:--|:--|:--|
|NoAction   |預設值。 與未設定此選項時相同。 粗話會從來源傳遞到目標。        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |會以 XML 標記括住不雅的字眼\<不雅內容 > 和\</profanity >。       |彼はジャッカスです。 |他\<不雅內容 > jackass\</profanity >。  |
|Deleted    |從輸出中移除不雅字眼，且不予取代。     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>從翻譯中排除內容
當您將轉譯具有標記，例如 HTML 的內容 (`contentType=text/html`)，可能會很有用翻譯中排除特定的內容。 您可以使用 `class=notranslate` 屬性，指定應該保留其原始語言的內容。 在下列範例中，在第一個內容`div`項目不會轉譯，但在第二個內容`div`將轉譯項目。

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>實作附註
將文字字串從某種語言翻譯成另一種語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Translate`。

**傳回值：** 翻譯的文字表示的字串。

如果您先前使用`AddTranslation`或是`AddTranslationArray`輸入評分為 5 或更高的同一個來源的句子，翻譯`Translate`傳回只是提供給系統的最上層的選擇。 「 同一個來源句子"表示完全相同 （100%相符），但大小寫、 泛空白字元、 標記值和標點符號的句子結尾除外。 如果未分級儲存評分為 5 或以上，則傳回的結果會由 Microsoft Translator 自動翻譯。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）

string

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|描述    |參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid  |(空白)    |必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|text|(空白)   |必要。 字串，表示要翻譯的文字。 文字不能包含超過 10,000 個字元。|query|string|
|from|(空白)   |選擇性。 字串，表示要翻譯的文字的語言代碼。 例如，en 代表「英文」。|query|string|
|to|(空白) |必要。 字串，表示要轉換成文字的語言的程式碼。|query|string|
|contentType|(空白)    |選擇性。 要翻譯文字的格式。 支援的格式為`text/plain`（預設值） 和`text/html`。 任何 HTML 項目必須是語式正確且完整的項目。|query|string|
|category|(空白)   |選擇性。 字串，包含翻譯的類別 （網域）。 預設為 `general`。|query|string|
|Authorization|(空白)  |如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)  |如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|


### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>實作附註
擷取多個來源文字翻譯。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`。

要求主體格式如下：

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

這些項目是在`TranslateArrayRequest`:


* `AppId`:必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`AppId`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。
* `From`:選擇性。 字串，表示要翻譯的文字的語言代碼。 如果此欄位保留空白，回應會包含自動語言偵測的結果。
* `Options`:選擇性。 `Options`物件，其中包含下列值。 這些是選用的預設為最常見的設定。 指定的項目必須以字母順序列出。
    - `Category`:字串，包含翻譯的類別 （網域）。 預設為 `general`。
    - `ContentType`:要翻譯文字的格式。 支援的格式為`text/plain`（預設值）， `text/xml`，和`text/html`。 任何 HTML 項目必須是語式正確且完整的項目。
    - `ProfanityAction`:指定如何處理 profanities，如先前所述。 接受的值為`NoAction`（預設值）， `Marked`，和`Deleted`。
    - `State`:使用者狀態，以協助將要求和回應相互關聯。 會在回應中傳回相同的內容。
    - `Uri`:依此 URI 篩選結果。 預設：`all`。
    - `User`:依此使用者篩選結果。 預設：`all`。
* `Texts`:必要。 陣列，包含翻譯的文字。 所有的字串必須以相同的語言。 若要轉譯的所有文字的總計不得超過 10,000 個字元。 陣列元素數目上限為 2,000。
* `To`:必要。 字串，表示要轉換成文字的語言的程式碼。

您可以省略選擇性的項目。 項目直接子系`TranslateArrayRequest`必須依字母順序列出。

`TranslateArray`方法會接受`application/xml`或是`text/xml`如`Content-Type`。

**傳回值：** `TranslateArrayResponse` 陣列。 每個`TranslateArrayResponse`都具有下列元素：

* `Error`:如果發生任何狀況，則表示發生錯誤。 否則設定為 null。
* `OriginalSentenceLengths`:整數的陣列，指出每個句子中的原始程式文字的長度。 陣列長度，指出句子數目。
* `TranslatedText`:翻譯的文字。
* `TranslatedSentenceLengths`:整數的陣列，表示每個句子中的已翻譯的文字長度。 陣列長度，指出句子數目。
* `State`:使用者狀態，以協助將要求和回應相互關聯。 傳回與要求相同的內容。

以下是回應主體的格式：

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>回應類別 （狀態 200）
成功的回應包含一系列的`TranslateArrayResponse`中稍早所述的格式陣列。

string

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|Authorization|(空白)  |如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)|如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼   |`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。 常見錯誤包括： <ul><li>陣列元素不可為空白。</li><li>無效的分類。</li><li>從語言無效。</li><li>語言無效。</li><li>此要求包含太多的項目。</li><li>不支援 From 語言。</li><li>不支援的轉換語言。</li><li>轉譯要求有太多資料。</li><li>HTML 不是正確的格式。</li><li>轉譯要求中傳遞太多的字串。</li></ul>|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>實作附註
擷取之語言的易記名稱做為參數傳遞`languageCodes`、 當地語系化至傳遞`locale`語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`。

要求主體包含字串陣列，表示要擷取的好記名稱的 ISO 639-1 語言代碼。 以下為範例：

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**傳回值：** 包含轉譯程式服務，當地語系化成要求的語言支援的語言名稱的字串陣列。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）
字串陣列，其中包含轉譯程式服務，當地語系化成要求的語言支援的語言名稱。

string

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|地區設定|(空白) |必要。 用來當地語系化的語言名稱的字串，表示下列項目，其中： <ul><li>與語言相關聯的 ISO 639 兩個字母小寫文化特性代碼和 ISO 3166 兩個字母大寫子文化特性代碼的組合。 <li>ISO 639 小寫文化特性代碼本身。|query|string|
|Authorization|(空白)  |如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)  |如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>實作附註
取得代表語言的翻譯服務所支援的語言代碼清單。  `Translate` 和 `TranslateArray` 可以翻譯這些語言的任兩種。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`。

**傳回值：** 字串陣列，其中包含轉譯程式服務所支援的語言代碼。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）
字串陣列，其中包含轉譯程式服務所支援的語言代碼。

string

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|Authorization|(空白)  |如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)|如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>實作附註
擷取提供語音合成功能的語言種類。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`。

**傳回值：** 字串陣列，其中包含轉譯程式服務所支援的語音合成的語言代碼。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）
字串陣列，其中包含轉譯程式服務所支援的語音合成的語言代碼。

string

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|Authorization|(空白)|如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)|如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|
 
### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401|無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>實作附註
傳回傳入，說出的文字所要的語言 WAV 或 MP3 資料流。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Speak`。

**傳回值：** WAV 或 MP3 文字資料流傳入，說出所要的語言。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）

binary

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|text|(空白)   |必要。 字串，包含要讀出的資料流，以指定的語言的一或多個句子。 文字不得超過 2,000 個字元。|query|string|
|language|(空白)   |必要。 字串，表示要說出的文字語言的支援的語言代碼。 程式碼必須是其中一個方法所傳回代碼`GetLanguagesForSpeak`。|query|string|
|format|(空白)|選擇性。 字串，指定內容類型識別碼。 目前可以使用 `audio/wav` 和 `audio/mp3`。 預設值為 `audio/wav`。|query|string|
|options|(空白)    |選擇性。 字串，指定的合成語音的屬性：<ul><li>`MaxQuality` 和`MinSize`指定品質音訊訊號。 `MaxQuality` 提供的最高的品質。 `MinSize` 提供的最小的檔案大小。 預設值是`MinSize`。</li><li>`female` 和`male`指定所需之語音的性別。 預設為 `female`。 使用分隔號 (<code>\|</code>) 包含多個選項。 例如，`MaxQuality|Male`。</li></li></ul>  |query|string|
|Authorization|(空白)|如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)  |如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>實作附註
識別一段文字的語言。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/Detect`。

**傳回值：** 字串，包含兩個字元語言代碼的文字。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）

string

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)  |必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|text|(空白)|必要。 字串，包含被識別為其語言的文字。 文字不得超過 10,000 個字元。|query|  string|
|Authorization|(空白)|如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key  |(空白)    |如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>實作附註

識別字串陣列中的語言。 獨立偵測到的每個個別陣列元素的語言，並傳回陣列的每個資料列的結果。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`。

要求主體格式如下：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

文字不能超過 10,000 個字元。

**傳回值：** 字串陣列，其中包含輸入陣列中每個資料列的兩個字元語言代碼。

以下是回應主體的格式：

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>回應類別 （狀態 200）
`DetectArray` 已成功。 傳回字串陣列，其中包含輸入陣列的每個資料列的兩個字元語言代碼。

string

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|Authorization|(空白)|如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。  授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)|如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>實作附註

> [!IMPORTANT]
> **已被取代的附註：** 在 2018 年 1 月 31 日之後, 這個方法不接受新的句子提交。 您會收到錯誤訊息。 請變更到 Collaborative Translation Framework (CTF) 的相關公告，參閱。

將翻譯新增至翻譯記憶體。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）

string

回應內容類型： 應用程式： xml
 
### <a name="parameters"></a>參數

|參數|值|說明|參數類型|資料類型   |
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|originalText|(空白)|必要。 字串，包含要轉譯的文字。 字串的長度上限是 1,000 個字元。|query|string|
|translatedText|(空白) |必要。 字串，包含文字轉譯成目標語言。 字串的長度上限為 2,000 個字元。|query|string|
|from|(空白)   |必要。 表示文字的原始語言的語言代碼的字串。 比方說，en 代表英文，以 de 代表德文。|query|string|
|to|(空白)|必要。 字串，表示要轉換成文字的語言的語言代碼。|query|string|
|rating|(空白) |選擇性。 整數，表示字串的品質評比。 這個值是介於-10 到 10 之間。 預設值為 1。|query|integer|
|contentType|(空白)    |選擇性。 要翻譯文字的格式。 支援的格式為`text/plain`和`text/html`。 任何 HTML 項目必須是語式正確且完整的項目。    |query|string|
|category|(空白)|選擇性。 字串，包含翻譯的類別 （網域）。 預設為 `general`。|query|string|
|user|(空白)|必要。 字串，用來追蹤提交的建立者。|query|string|
|uri|(空白)|選擇性。 字串，包含翻譯的內容位置。|query|string|
|Authorization|(空白)|如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。  授權權杖：`"Bearer" + " " + "access_token"`。  |頁首|string|
|Ocp-Apim-Subscription-Key|(空白)|如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|410|`AddTranslation` 已不再支援。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>實作附註

> [!IMPORTANT]
> **已被取代的附註：** 在 2018 年 1 月 31 日之後, 這個方法不接受新的句子提交。 您會收到錯誤訊息。 請變更到 Collaborative Translation Framework (CTF) 的相關公告，參閱。

將翻譯的記憶體中的翻譯的陣列。 這個方法會是陣列的舊版`AddTranslation`。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`。

要求主體格式如下：

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

這些項目是在`AddtranslationsRequest`:

* `AppId`:必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`AppId`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。
* `From`:必要。 字串，包含原始碼語言的語言代碼。 必須是 `GetLanguagesForTranslate` 方法所傳回的其中一種語言。
* `To`:必要。 字串，包含目標語言的語言代碼。 必須是 `GetLanguagesForTranslate` 方法所傳回的其中一種語言。
* `Translations`:必要。 要新增至翻譯記憶體的翻譯陣列。 必須包含每一個翻譯`OriginalText`， `TranslatedText`，和`Rating`。 每個大小上限`OriginalText`和`TranslatedText`是 1,000 個字元。 所有總計`OriginalText`和`TranslatedText`項目不能超過 10,000 個字元。 最大陣列項目數是 100。
* `Options`:必要。 一組選項，包括`Category`， `ContentType`， `Uri`，和`User`。 `User` 是必要的。 `Category``ContentType`，和`Uri`是選擇性的。 指定的項目必須以字母順序列出。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）
`AddTranslationArray` 成功的方法。 

在 2018 年 1 月 31 日之後, 將不會接受句子提交。 服務將會回應錯誤碼 410。

string

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|Authorization|(空白)|如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。  授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)|如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|410    |`AddTranslation` 已不再支援。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>實作附註
分成句子中的一段文字，並傳回陣列，其中包含每個句子的長度。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`。

**傳回值：** 整數的陣列，表示句子的長度。 陣列的長度表示句子的數目。 值代表每個句子的長度。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）
整數的陣列，表示句子的長度。 陣列的長度表示句子的數目。 值代表每個句子的長度。

integer

回應內容類型： application/xml

### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)  |必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query| string|
|text|(空白)   |必要。 表示將句子分成文字的字串。 文字的大小上限是 10,000 個字元。|query|string|
|language   |(空白)    |必要。 表示輸入文字的語言代碼的字串。|query|string|
|Authorization|(空白)|如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。   |頁首|string|
|Ocp-Apim-Subscription-Key|(空白)|如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400|不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401|無效的認證。|
|500|伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>實作附註
從存放區和 MT 引擎中擷取指定語言組的翻譯陣列。 `GetTranslations` 不同於`Translate`，會傳回所有可用的翻譯。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`。

在要求主體包含選用`TranslationOptions`，該物件具有下列格式：

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions`物件包含下列清單中的值。 這些是選用的預設為最常見的設定。 指定的項目必須以字母順序列出。

* `Category`:字串，包含翻譯的類別 （網域）。 預設為 `general`。
* `ContentType`:唯一支援的選項和預設值，是`text/plain`。
* `IncludeMultipleMTAlternatives`:若要指定是否應該從 MT 引擎傳回一個以上的替代方案的布林值旗標。 有效值`true`和`false`（區分大小寫）。 預設值是`false`，它會傳回僅有一個替代方法。 旗標設定為`true`可讓您建立假造的替代方案，完全整合與 Collaborative Translation Framework (CTF)。 功能可讓傳回的替代項目中 CTF 有沒有翻譯，加上從假造的替代方案的句子*n*-解碼器的最佳的清單。
    - 評等。 分級會套用像這樣： 
         - 最佳自動翻譯的評分為 5。
       - 從 CTF 替代項目會反映檢閱者的授權單位。 它們的範圍從-10 到 + 10。
       - 自動產生 (*n*-最佳) 轉譯替代項目有 0 到 100 相符程度的評等。
    - 替代項目數目。 傳回的替代項目數目最多可在指定的值為`maxTranslations`，但它可以是較低。
    - 語言配對。 這項功能不適用於簡體中文和繁體中文任一方向中的翻譯。 它位於所有其他 Microsoft Translator 支援的語言組。
* `State`:使用者狀態，以協助將要求和回應相互關聯。 會在回應中傳回相同的內容。
* `Uri`:依此 URI 篩選結果。 如果未不設定任何值，預設值是`all`。
* `User`:依此使用者篩選結果。 如果未不設定任何值，預設值是`all`。

要求 `Content-Type` 應該是 `text/xml`。

**傳回值：** 以下是回應的格式：

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

此回應包含`GetTranslationsResponse`元素，其中包含下列值：

* `Translations`:找到的相符項目陣列，儲存在`TranslationMatch`（下一節中所述） 的物件。 翻譯可能包含原始的文字 （模糊比對） 稍微變體。 會將翻譯排序為：100%符合第一，模糊的相符項目旁。
* `From`:如果未指定方法`From`語言中，這個值會來自自動語言偵測。 否則，它會指定`From`語言。
* `State`:使用者狀態，以協助將要求和回應相互關聯。 包含在所提供的值`TranslateOptions`參數。

`TranslationMatch`物件是由這些值所組成：

* `Error`:對於特定的輸入字串就會發生錯誤時的錯誤代碼。 否則，這個欄位是空的。
* `MatchDegree`:表示輸入的文字符合原始文字存放區中找到的程度。 系統會比對輸入句子與存放區 (包括未完全相符的項目)。 傳回值的範圍從 0 到 100 之間，其中 0 是沒有相似度，而 100 是確切的區分大小寫相符項目。
* `MatchedOriginalText`:此結果相符的原始文字。 只有當相符的原始文字已不同於輸入的文字，則會傳回這個值。 它用來傳回模糊相符的來源文字。 Microsoft Translator 結果未傳回此值。
* `Rating`:指出制定品質決策之人員的授權單位。 機器翻譯結果有 5 星級。 以匿名方式所提供的翻譯通常會有介於 1 到 4 的評等。 以系統授權方式提供的翻譯一般會有從 6 到第 10 評等。
* `Count`:已選取具有此評分之這個翻譯的次數。 值為 0，自動翻譯的回應。
* `TranslatedText`:翻譯的文字。

### <a name="response-class-status-200"></a>回應類別 （狀態 200）
A`GetTranslationsResponse`中先前所述的格式物件。

string

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|appid|(空白)|必要。 如果`Authorization`或是`Ocp-Apim-Subscription-Key`標頭，請將保留`appid`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。|query|string|
|text|(空白)|必要。 字串，表示要翻譯的文字。 文字的大小上限是 10,000 個字元。|query|string|
|from|(空白)|必要。 字串，表示要翻譯的文字的語言代碼。|query|string|
|to |(空白)    |必要。 字串，表示要轉換成文字的語言的語言代碼。|query|string|
|maxTranslations|(空白)|必要。 整數，表示要傳回翻譯的最大數目。|query|integer|
|Authorization| (空白)|如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。 授權權杖：`"Bearer" + " " + "access_token"`。|string|  頁首|
|Ocp-Apim-Subscription-Key|(空白)  |如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供給我們，大約的日期和時間的要求與回應標頭中包含的要求識別碼`X-MS-Trans-Info`。|
|503|服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>實作附註
擷取多個來源文字的多個翻譯候選項的目。

要求 URI 為 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`。

要求主體格式如下：

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` 包含下列元素：

* `AppId`:必要。 如果`Authorization`標頭，請將保留`AppId`欄位都是空白。 否則，包括字串，包含`"Bearer" + " " + "access_token"`。
* `From`:必要。 字串，表示要翻譯的文字的語言代碼。
* `MaxTranslations`:必要。 整數，表示要傳回翻譯的最大數目。
* `Options`:選擇性。 `Options`物件，其中包含下列值。 這些是選用的預設為最常見的設定。 指定的項目必須以字母順序列出。
    - `Category`:字串，包含翻譯的類別 （網域）。 預設為 `general`。
    - `ContentType`:唯一支援的選項和預設值，是`text/plain`。
    - `IncludeMultipleMTAlternatives`:若要指定是否應該從 MT 引擎傳回一個以上的替代方案的布林值旗標。 有效值`true`和`false`（區分大小寫）。 預設值是`false`，它會傳回僅有一個替代方法。 旗標設定為`true`可讓產生的人工翻譯，完全整合與共同作業翻譯 Framework (CTF) 中的替代方案。 功能可讓傳回的替代項目在 CTF 中的任何替代項目，藉由新增人工的替代項目從句子*n*-解碼器的最佳的清單。
        - 評等分級會套用像這樣：
          - 最佳自動翻譯的評分為 5。
          - 從 CTF 替代項目會反映檢閱者的授權單位。 它們的範圍從-10 到 + 10。
          - 自動產生 (*n*-最佳) 轉譯替代項目有 0 到 100 相符程度的評等。
        - 替代項目數目。 傳回的替代項目數目最多可在指定的值為`maxTranslations`，但它可以是較低。
        - 語言配對。 這項功能不適用於簡體中文和繁體中文任一方向中的翻譯。 它位於所有其他 Microsoft Translator 支援的語言組。
* `State`:使用者狀態，以協助將要求和回應相互關聯。 會在回應中傳回相同的內容。
* `Uri`:依此 URI 篩選結果。 如果未不設定任何值，預設值是`all`。
* `User`:依此使用者篩選結果。 如果未不設定任何值，預設值是`all`。
* `Texts`:必要。 陣列，包含翻譯的文字。 所有的字串必須以相同的語言。 若要轉譯的所有文字的總計不得超過 10,000 個字元。 最大陣列項目數是 10。
* `To`:必要。 字串，表示要轉換成文字的語言的語言代碼。

您可以省略選擇性的項目。 項目直接子系`GetTranslationsArrayRequest`必須依字母順序列出。

要求 `Content-Type` 應該是 `text/xml`。

**傳回值：** 以下是回應的格式：

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

每個`GetTranslationsResponse`項目包含下列值：

* `Translations`:找到的相符項目陣列，儲存在`TranslationMatch`（下一節中所述） 的物件。 翻譯可能包含原始的文字 （模糊比對） 稍微變體。 會將翻譯排序為：100%符合第一，模糊的相符項目旁。
* `From`:如果未指定方法`From`語言中，這個值會來自自動語言偵測。 否則，它會指定`From`語言。
* `State`:使用者狀態，以協助將要求和回應相互關聯。 包含在所提供的值`TranslateOptions`參數。

`TranslationMatch`物件包含下列值：
* `Error`:對於特定的輸入字串就會發生錯誤時的錯誤代碼。 否則，這個欄位是空的。
* `MatchDegree`:表示輸入的文字符合原始文字存放區中找到的程度。 系統會比對輸入句子與存放區 (包括未完全相符的項目)。 傳回值的範圍從 0 到 100 之間，其中 0 是沒有相似度，而 100 是確切的區分大小寫相符項目。
* `MatchedOriginalText`:此結果相符的原始文字。 只有當相符的原始文字已不同於輸入的文字，則會傳回這個值。 它用來傳回模糊相符的來源文字。 Microsoft Translator 結果未傳回此值。
* `Rating`:指出制定品質決策之人員的授權單位。 機器翻譯結果有 5 星級。 以匿名方式所提供的翻譯通常會有介於 1 到 4 的評等。 以系統授權方式提供的翻譯通常會有從 6 到第 10 評等。
* `Count`:已選取具有此評分之這個翻譯的次數。 值為 0，自動翻譯的回應。
* `TranslatedText`:翻譯的文字。


### <a name="response-class-status-200"></a>回應類別 （狀態 200）

string

回應內容類型： application/xml
 
### <a name="parameters"></a>參數

|參數|值|描述|參數類型|資料類型|
|:--|:--|:--|:--|:--|
|Authorization  |(空白)    |如果這兩個，則需要`appid`欄位和`Ocp-Apim-Subscription-Key`標頭會保留空白。  授權權杖：`"Bearer" + " " + "access_token"`。|頁首|string|
|Ocp-Apim-Subscription-Key|(空白)  |如果這兩個，則需要`appid`欄位和`Authorization`標頭會保留空白。|頁首|string|

### <a name="response-messages"></a>回應訊息

|HTTP 狀態碼|`Reason`|
|:--|:--|
|400    |不正確的要求。 檢查輸入參數和詳細錯誤回應。|
|401    |無效的認證。|
|500    |伺服器錯誤。 若錯誤持續發生，請讓我們知道。 請提供要求的大約日期和時間，並將要求識別碼包括在回應標頭 `X-MS-Trans-Info` 中。|
|503    |服務暫時無法使用。 請重試，並讓我們知道錯誤是否持續發生。|

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移轉至 Translator Text API v3](../migrate-to-v3.md)


