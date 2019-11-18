---
title: 搭配文字分析 API 使用實體辨識
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文字分析 REST API 識別並區分文字中找到的實體識別。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/12/2019
ms.author: aahi
ms.openlocfilehash: 5933c7ec56ded971e4daf96ea6d4302c04921f2f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031432"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文字分析中使用已命名的實體識別

[已命名的實體辨識 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)會採用非結構化文字，並針對每個 JSON 檔傳回可辨識的實體清單，其中包含 Web （維琪百科和 Bing）上詳細資訊的連結。

## <a name="entity-linking-and-named-entity-recognition"></a>實體連結和具名實體辨識

文字分析 ' `entities` 端點支援命名實體識別（NER）和實體連結。

### <a name="entity-linking"></a>實體連結
連結實體可識別及區分文字中找到的實體身分識別 (例如，判斷 "Mars" 是用來指星體或指羅馬的戰神)。 此程序需具備可讓辨識項目與之連結的知識庫 - `entities` 端點文字分析即是使用維基百科作為知識庫。

### <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)
「命名實體辨識」（NER）是指在文字中識別不同的實體，並將其分類為預先定義的類別或類型的能力。 

## <a name="named-entity-recognition-v3-public-preview"></a>命名實體辨識 v3 公開預覽

[下一版的命名實體]( https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)辨識現已提供公開預覽。 它提供實體連結和命名實體辨識的更新。 

:::row:::
    :::column span="":::
        **功能**
    :::column-end:::
    ::: column span="":::
        **描述** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        擴充的實體類型和子類型
    :::column-end:::
    :::column span="":::
     已擴充的分類和偵測數個已命名的實體類型。
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        個別的要求端點 
    :::column-end:::
    :::column span="":::
        用於傳送實體連結和 NER 要求的個別端點。
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` 參數
    :::column-end:::
    :::column span="":::
        選擇文字分析模型版本的選擇性參數。 目前只有預設模型可供使用。
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>實體類型

命名實體辨識 v3 提供跨多個類型的展開偵測。 目前，NER v3 可以辨識下列類別的實體。 如需支援的實體和語言的詳細清單，請參閱[命名實體類型](../named-entity-types.md)一文。

* 一般
* 個人資訊 

### <a name="request-endpoints"></a>要求端點

命名實體辨識 v3 會針對 NER 和實體連結要求使用不同的端點。 根據您的要求使用下列 URL 格式：

NER
* 一般實體-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 個人資訊實體-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

實體連結
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>模型版本設定

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>命名實體辨識 v2 支援的類型

> [!NOTE]
> 命名實體識別（NER）第2版支援下列實體。 [NER v3](#named-entity-recognition-v3-public-preview)處於公開預覽狀態，可大幅擴充文字中辨識之實體的數目和深度。   

| 在系統提示您進行確認時，輸入  | SubType | 範例 |
|:-----------   |:------------- |:---------|
| Person        | N/A\*         | "Jeff"、"Bill Gates"     |
| 位置      | N/A\*         | "Redmond, Washington"、"Paris"  |
| 組織  | N/A\*         | "Microsoft"   |
| 數量      | 數字        | "6"、"six"     |
| 數量      | 百分比    | "50%"、"fifty percent"|
| 數量      | 序數       | "2nd"、"second"     |
| 數量      | 年齡           | "90 day old"、"30 years old"    |
| 數量      | 貨幣      | "$10.99"     |
| 數量      | 維度     | "10 miles"、"40 cm"     |
| 數量      | 溫度   | "32 degrees"    |
| DateTime      | N/A\*         | "6:30PM February 4, 2012"      |
| DateTime      | Date          | "May 2nd, 2017"、"05/02/2017"   |
| DateTime      | 時間          | "8am"、"8:00"  |
| DateTime      | 日期範圍     | "May 2nd to May 5th"    |
| DateTime      | 時間範圍     | "6pm to 7pm"     |
| DateTime      | 持續時間      | "1 minute and 45 seconds"   |
| DateTime      | Set           | "every Tuesday"     |
| URL           | N/A\*         | "HTTPs：\//www.bing.com"    |
| 電子郵件         | N/A\*         | "support@contoso.com" |

\* 依輸入和擷取的實體而定，某些實體可能會省略 `SubType`。  列出的所有支援實體類型僅適用于英文、簡體中文、法文、德文和西班牙文等語言。

### <a name="language-support"></a>語言支援

若以多種語言使用連結實體，則需使用每一種語言的對應知識庫。 對於文字分析中的實體連結，這表示 `entities` 端點支援的每一種語言會連結至該語言的對應維基百科語料庫。 由於語料庫的大小會因語言不同而有所差異，因此我們預期實體連結功能的召回也會有所不同。 如需詳細資訊，請參閱[語言支援](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)文章。

## <a name="preparation"></a>準備工作

您必須具有此格式的 JSON 檔：識別碼、文字、語言

如需目前支援的語言，請參閱[這份清單](../text-analytics-supported-languages.md)。

文件大小必須少於 5,120 個字元，而且您最多可以針對每個集合擁有 1,000 個項目 (識別碼)。 集合會在要求本文中提交。 下列範例是您可能會提交至實體連結末端的內容說明。

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>步驟 1：建立要求結構

關於要求定義的詳細資料可以在[如何呼叫文字分析 API](text-analytics-how-to-call-api.md) 中找到。 為了方便起見，我們將重申下列各點：

+ 建立一個 **POST** 要求。 請參閱此要求的 API 檔：[實體 api](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ 使用 Azure 上的文字分析資源或具現化的[文字分析容器](text-analytics-how-to-install-containers.md)，設定關鍵字組解壓縮的 HTTP 端點。 您必須包含 `/text/analytics/v2.1/entities`。 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`。

+ 設定要求標頭以包含文字分析作業[的存取金鑰](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在要求本文中，提供您準備用於此分析的 JSON 文件集合

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或開啟**文件** \(英文\) 中的 [API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)來建立要求結構，並將它 POST 到服務。

## <a name="step-2-post-the-request"></a>步驟 2：張貼要求

分析會在接收要求時執行。 請參閱概觀中的[資料限制](../overview.md#data-limits)一節，以取得您每分鐘和每秒鐘可以傳送的要求大小和數量資訊。

請記得，服務是無狀態的。 您的帳戶中並不會儲存任何資料。 結果會在回應中立即傳回。

## <a name="step-3-view-results"></a>步驟 3：檢視結果

所有的 POST 要求都會傳回 JSON 格式的回應，具有識別碼和偵測到的屬性。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。

實體連結輸出的範例會隨即顯示：

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Summary

在本文中，您已了解在認知服務中使用文字分析的實體連結概念和工作流程。 摘要說明：

+ [實體 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) \(英文\) 僅針對特定語言提供。
+ 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
+ 使用對您訂用帳戶有效的個人化`/entities`存取金鑰和端點[，將要求 POST 到 ](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 端點。
+ 由連結實體 (包含每個文件識別碼的信賴分數、位移和網頁連結) 組成的回應輸出可用於任何應用程式

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [文字分析概觀](../overview.md)
* [常見問題集 (FAQ)](../text-analytics-resource-faq.md)</br>
* [文字分析產品頁面](//go.microsoft.com/fwlink/?LinkID=759712)
