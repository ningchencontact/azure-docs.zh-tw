---
title: 使用 Power BI 進行文字分析 - Azure 認知服務 | Microsoft Docs
description: 了解如何使用文字分析，以從儲存在 Power BI 的文字中擷取關鍵片語。
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 705b7b0eb32777953b365b217550e4591b8c27aa
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2018
ms.locfileid: "39717281"
---
# <a name="text-analytics-with-power-bi"></a>文字分析與 Power BI

Microsoft Power BI 會將組織的資料去蕪存菁為美觀的報告，讓您可以分散到整個組織以更快地獲得更深入的見解。 屬於 Microsoft Azure 認知服務一部分的文字分析服務，可透過其關鍵片語 API 從文字中擷取最重要的片語。 搭配使用這些工具，您便可快速了解客戶在說什麼，以及他們對此有何看法。

在本教學課程中，您可以了解如何整合 Power BI Desktop 和關鍵片語 API，使用自訂 Power Query 函式從客戶的意見反應中擷取最重要的片語。 我們也會從這些片語建立文字雲。

## <a name="prerequisites"></a>必要條件

若要進行本教學課程，您需要：

> [!div class="checklist"]
> * Microsoft Power BI Desktop。 [免費下載](https://powerbi.microsoft.com/get-started/)。
> * Microsoft Azure 帳戶。 [開始免費試用](https://azure.microsoft.com/free/)或[登入](https://portal.azure.com/)。
> * 文字分析的存取金鑰。 [註冊](../../cognitive-services-apis-create-account.md)，然後[取得金鑰](../how-tos/text-analytics-how-to-access-key.md)。
> * 客戶評論。 [取得我們的資料範例](https://aka.ms/cogsvc/ta)或使用您自己的範例。

## <a name="loading-customer-data"></a>載入客戶資料

若要開始使用，請開啟 Power BI Desktop 並載入逗號分隔值 (CSV) 檔案 **FabrikamComments.csv**。 這個檔案代表一家小型虛構公司的支援論壇中，一天之內的假設性活動。

> [!NOTE]
> Power BI 可以使用各種來源的資料，例如 Facebook 或 SQL 資料庫。 請深入了解 [Facebook 與 Power BI 的整合](https://powerbi.microsoft.com/integrations/facebook/)和 [SQL Server 與 Power BI 的整合](https://powerbi.microsoft.com/integrations/sql-server/)。

在 Power BI Desktop 主要視窗中，尋找 [常用] 功能區的 [外部資料] 群組。 在此群組的 [取得資料] 下拉式功能表中，選擇 [文字/CSV]。

![[取得資料按鈕]](../media/tutorials/power-bi/get-data-button.png)

[開啟] 對話方塊隨即出現。 瀏覽至 [下載] 資料夾，或任何包含資料檔案範例的資料夾。 按一下 `FabrikamComments.csv`，然後按 [開啟] 按鈕。 [CSV 匯入] 對話方塊隨即出現。

![[CSV 匯入對話方塊]](../media/tutorials/power-bi/csv-import.png)

[CSV 匯入] 對話方塊可讓您確認 Power BI Desktop 是否已正確地偵測到字元集、分隔符號、標題資料列和資料行類型。 此資訊全都正確，所以我們按一下 [載入]。

為了查看所載入的資料，請使用 Power BI 工作區左側邊緣的 [資料檢視] 按鈕切換到 [資料] 檢視。 隨即會開啟資料表，其中包含我們的資料，就和在 Microsoft Excel 中一樣。

![[所匯入資料的初始檢視]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>準備資料

您可能需要先將 Power BI Desktop 中的資料進行轉換，才能讓資料準備好供關鍵片語服務進行處理。

例如，我們的資料同時包含 `subject` 欄位和 `comment` 欄位。 在擷取關鍵片語時，這兩個欄位中的文字都應該納入考慮，而不只是考慮 `comment`。 Power BI Desktop 中的「合併資料行」函式可讓您輕鬆完成這項工作。

在 [常用] 功能區的 [外部資料] 群組中按一下 [編輯查詢]，以從 Power BI Desktop 的主要視窗開啟「查詢編輯器」。 

![[常用功能區中的外部資料群組]](../media/tutorials/power-bi/edit-queries.png)

在視窗左側的 [查詢] 清單中選取 [FabrikamComments] (如果尚未選取)。

現在，選取資料表中的 `subject` 和 `comment` 資料行。 您可能需要水平捲動才能看到這些資料行。 請先按一下 `subject` 資料行標題，然後按住 Control 鍵並按一下 `comment` 資料行標題。

![[選取要合併的欄位]](../media/tutorials/power-bi/select-columns.png)

在 [轉換] 功能區的 [文字資料行] 群組中，按一下 [合併資料行]。 [合併資料行] 對話方塊隨即出現。

![[使用合併資料行對話方塊合併欄位]](../media/tutorials/power-bi/merge-columns.png)

在 [合併資料行] 對話方塊中，選擇 [定位字元] 作為分隔符號，然後按一下 [確定]。 完成！

您也可以考慮使用「移除空白」篩選條件來篩掉空白訊息，或是使用「全新轉換」移除不可列印的字元。 如果資料包含資料行 (例如，檔案範例中的 `spamscore` 資料行)，您可以使用「數字篩選條件」來略過「垃圾」評論。

## <a name="understanding-the-api"></a>了解 API

關鍵片語 API 可針對每個 HTTP 要求處理多達一千個文字文件。 不過，Power BI 偏好一次處理一個記錄，所以我們對 API 的呼叫只會包含單一文件。 [此 API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) 要求所處理的每個文件具有下列欄位。

| | |
| - | - |
| `id`  | 這個文件在要求中的唯一識別碼。 回應中也會包含此欄位。 如此一來，如果您處理多個文件，就可以輕鬆地將擷取到的關鍵片語關聯至其來源文件。 我們會在每個要求中處理一個文件，因此，我們已經知道回應所關聯的文件，而且 `id` 在每個要求中可以是相同的。|
| `text`  | 要處理的文字。 我們會從所建立的 `Merged` 資料行取得文字，該資料行內含有合併的主旨列和評論文字。 關鍵片語 API 要求這項資料的長度不得超過約 5,000 個字元。|
| `language` | 代表文件撰寫語言的代碼。 我們的訊息全都使用英文，因此，可以將語言 `en` 硬式編碼到查詢中。|

我們需要將這些欄位合併成 JSON (JavaScript 物件標記法) 文件，以便提交給關鍵片語 API。 此要求的回應也是 JSON 文件，所以必須對其進行剖析，並擷取其中的關鍵片語。

## <a name="creating-a-custom-function"></a>建立自訂函式

現在，我們已經準備好建立自訂函式，以整合 Power BI 與文字分析。 Power BI Desktop 會針對資料表的每個資料列叫用函式，並使用結果建立新的資料行。

函式會收到要處理為參數的文字。 它會將資料轉換成所需的 JavaScript 物件標記法 (JSON) 以及進行反向轉換，並對關鍵片語 API 端點提出 HTTP 要求。 對回應進行剖析之後，函式會傳回字串，其中包含所擷取關鍵片語的逗號分隔清單。

> [!NOTE]
> Power BI Desktop 自訂函式會以 [Power Query M 公式語言](https://msdn.microsoft.com/library/mt211003.aspx) (簡稱 "M") 來撰寫。 M 是以 [F#](http://www.tryfsharp.org/) 為基礎的功能性程式設計語言。 不過，不是程式設計師也能完成本教學課程；下面有所需的程式碼。

您應該還是在 [查詢編輯器] 視窗中。 從 [常用] 功能區中，按一下 [新增來源] (在 [新增查詢] 群組中)，然後從下拉式功能表選擇 [空白查詢]。 

[查詢] 清單中會出現新的查詢，一開始名為 Query1。 按兩下此項目，並將它命名為 `KeyPhrases`。

現在，對 [常用] 功能區的 [查詢] 群組按一下 [進階編輯器]，以開啟 [進階編輯器] 視窗。 刪除該視窗中已有的程式碼，然後貼上下列程式碼。 

> [!NOTE]
> 在下列範例中，我們假設端點位於 https://westus.api.cognitive.microsoft.com。  文字分析可支援在 13 個不同區域中建立訂用帳戶。 如果您已在不同區域註冊服務，請務必使用所選區域的端點。 當您選取「文字分析」訂用帳戶時，此資訊應該會顯示在 Azure 入口網站的 [概觀] 頁面中。

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

也請貼上文字分析 API 金鑰 (從 Microsoft Azure 儀表板中開頭為 `apikey` 的程式行中取得)。 (請務必保留金鑰前後的引號)。然後按一下 [完成]。

## <a name="using-the-function"></a>使用函式

現在，我們可以使用自訂函式，來取得每個客戶評論中所包含的關鍵片語，並將其儲存在資料表的新資料行中。 

仍在查詢編輯器中，切換回 FabrikamComments 查詢，變更至 [新增資料行] 功能區中，然後按一下 [一般] 群組中的 [叫用自訂函式] 按鈕。

![[叫用自訂函式按鈕]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

在 [叫用自訂函式] 對話方塊中，輸入 `keyphrases` 作為新資料行的名稱。 選擇我們的自訂函式 `KeyPhrases` 作為「函式查詢」。 

對話方塊中會出現新的欄位，詢問要將哪一個欄位傳遞給函式作為 `text` 參數。 從下拉式功能表選擇 `Merged` (我們稍早藉由合併主旨和訊息欄位所建立的資料行)。

![[叫用自訂函式]](../media/tutorials/power-bi/invoke-custom-function.png)

最後，按一下 [確定]。

一切準備就緒時，Power BI 會針對資料表中的每個資料列呼叫函式一次，以執行關鍵片語查詢並在資料表中新增資料行。 但在開始進行之前，您可能需要指定驗證和隱私權設定。

## <a name="authentication-and-privacy"></a>驗證和隱私權

關閉 [叫用自訂函式] 對話方塊之後，可能會出現橫幅要求您指定如何連線至關鍵片語端點。

![[認證橫幅]](../media/tutorials/power-bi/credentials-banner.png)

按一下 [編輯認證]，確定已在對話方塊中選取 [匿名]，然後按一下 [連線]。 

> [!NOTE]
> 為何要匿名？ 文字分析服務會驗證您使用的 API 金鑰，因此 Power BI 不需要為 HTTP 要求本身提供認證。

![[將驗證設定為匿名]](../media/tutorials/power-bi/access-web-content.png)

如果在選擇匿名存取後仍看到 [編輯認證] 橫幅，表示您可能忘了貼上 API 金鑰。 請檢查 [進階編輯器] 中的 `KeyPhrases` 自訂函式，以確定其中有金鑰。

接下來，可能會出現橫幅要求您提供關於資料來源隱私權的資訊。 

![[隱私權橫幅]](../media/tutorials/power-bi/privacy-banner.png)

按一下 [繼續]，然後針對對話方塊中的每個資料來源選擇 [公用]。 然後按一下 [儲存]。

![[設定資料來源隱私權]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>建立文字雲

處理好出現的各個橫幅後，按一下 [常用] 功能區中的 [關閉並套用] 以關閉查詢編輯器。

Power BI Desktop 需要一點時間來提出必要的 HTTP 要求。 在資料表的每個資料列中，新的 `keyphrases` 資料行會包含關鍵片語 API 在文字中所偵測到的關鍵片語。 

讓我們使用此資料行來產生文字雲。 若要開始，請按一下工作區左邊 Power BI Desktop 主要視窗中的 [報告] 按鈕。

> [!NOTE]
> 為何要使用所擷取的關鍵片語來產生文字雲，而不是使用每個評論的完整文字？ 關鍵片語可為我們提供客戶評論中的「重要」文字，而不只是「最常見的」文字。 此外，在產生的文字雲中，文字大小也不會因為相對少數的評論中頻繁使用某個文字而受到影響。

如果您尚未安裝文字雲自訂視覺效果，請加以安裝。 在工作區右邊的 [視覺效果] 窗格中，按一下三個點 (**...**)，然後選擇 [從存放區匯入]。 然後搜尋「雲」，並按一下文字雲視覺效果旁的 [新增] 按鈕。 Power BI 會安裝文字雲視覺效果，並讓您知道它已成功安裝。

![[新增自訂視覺效果]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

現在，讓我們建立自己的文字雲！

![[視覺效果面板中的文字雲圖示]](../media/tutorials/power-bi/visualizations-panel.png)

首先，按一下 [視覺效果] 面板中的 [文字雲] 圖示。 新的報告隨即出現在工作區中。 將 `keyphrases` 欄位從 [欄位] 面板拖曳至 [視覺效果] 面板的 [類別] 欄位。 文字雲會出現在報告中。

現在，切換到 [視覺效果] 面板的 [格式] 頁面。 在 [停止文字] 類別中，開啟 [預設停止文字] 以從雲端排除常見的簡短文字，例如 "of"。 

![[啟用預設停止文字]](../media/tutorials/power-bi/default-stop-words.png)

在這個面板中往下一點，關閉 [旋轉文字] 和 [標題]。

![[啟用焦點模式]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

按一下報告中的 [焦點模式] 工具，以便更加了解我們的文字雲。 此工具會展開文字雲以填滿整個工作區，如下所示。

![[文字雲]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>更多的文字分析服務

文字分析服務 (Microsoft Azure 所提供的其中一個認知服務) 也會提供情感分析和語言偵測。 如果客戶的意見反應並非全是英文，語言偵測便很實用。

這兩個另外的 API 非常類似關鍵片語 API。 因此，我們可以使用幾乎相同的自訂函式來將它們與 Power BI Desktop 進行整合。 只要建立空白查詢並在 [進階編輯器] 中貼入下列適當的程式碼即可，方法同前。 (別忘了存取金鑰)！然後，同樣地，使用函式在資料表中新增資料行。

下面的情感分析函式會傳回分數，以指出文字中所表達的情感有多正面。

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

以下是語言偵測函式的兩個版本。 第一個版本會傳回 ISO 語言代碼 (例如 `en` 代表英文)，第二個版本則會傳回「易記」名稱 (例如 `English`)。 您可能會注意到，兩個版本之間只有內文的最後一行不同。

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (e.g. English) of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

最後，以下是已提出的關鍵片語函式變化，其會以清單物件的形式傳回片語，而不是以逗號分隔片語單一字串的形式傳回。 

> [!NOTE]
> 傳回單一字串簡化了我們的文字雲範例。 另一方面，清單則是更有彈性的格式，可處理 Power BI 中所傳回的片語。 您可以使用查詢編輯器 [轉換] 功能區中的 [結構化資料行] 群組，以處理 Power BI Desktop 中的清單物件。

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>後續步驟

深入了解文字分析服務、Power Query M 公式語言或 Power BI。

> [!div class="nextstepaction"]
> [文字分析 API 參考](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Power Query M 參考](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Power BI 文件](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
