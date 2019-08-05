---
title: 使用 Azure 認知服務中的文字分析 REST API 進行情感分析
titleSuffix: Azure Cognitive Services
description: 了解如何使用文字分析 REST API 來偵測情感。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 19654a4902ae64e5de63ffc93a8d143cc518e254
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697738"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>範例：使用文字分析來偵測情感

[Azure 情感分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) \(英文\) 會評估文字輸入，並傳回每份文件的情感分數。 分數的範圍是從 0 (負面) 到 1 (正面)。

此功能在偵測社交媒體、客戶評價及討論論壇中的正面和負面情感上非常實用。 內容由您提供。 模型和定型資料則由服務提供。

情感分析 API 目前支援英文、德文、西班牙文及法文。 其他語言則為預覽狀態。 如需詳細資訊，請參閱[支援的語言](../text-analytics-supported-languages.md)。

> [!TIP]
> Azure 文字分析 API 也會提供可用來分析情感的 Linux 型 Docker 容器映像，好讓您可以在接近資料的位置[安裝和執行文字分析容器](text-analytics-how-to-install-containers.md)。

## <a name="concepts"></a>概念

文字分析會使用機器學習分類演算法來產生介於 0 和 1 之間的情感分數。 接近 1 的分數表示正面情感，而接近 0 的分數則表示負面情感。 此模型是使用大量具有情感關聯的文字主體進行預先定型。 目前，您並無法提供自己的定型資料。 模型會在文字分析期間使用多種技術。 這些技術包括文字處理、詞性分析、字組位置和字組關聯。 如需有關演算法的詳細資訊，請參閱[文字分析簡介](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/) \(英文\)。

情感分析會針對整份文件執行，而不會擷取對於文字中特定實體的情感。 在實務上，當文件包含一或兩個句子而非大型文字區塊時，會較容易提升評分的準確度。 在客觀評估階段，模型會判斷整份文件是客觀的，還是包含情感的。 主要為客觀的文件不會進展到情感偵測階段，因此分數為 0.50，而且不會進一步處理。 對於繼續在管線中處理的文件，下一個階段會產生高於或低於 0.50 的分數。 分數取決於在文件中偵測到的情感程度。

## <a name="preparation"></a>準備工作

情感分析會在您為它提供較小區塊的文字來處理時，產生較高品質的結果。 這與關鍵片語擷取相反，後者在較大型文字區塊上的表現會更好。 若要從這兩個作業中取得最佳結果，請考慮據以重建輸入。

您必須具有此格式的 JSON 文件：識別碼、文字和語言。

每份文件的大小必須低於 5,120 個字元。 每個集合最多可以有 1,000 個項目 (識別碼)。 集合會在要求本文中提交。 以下是您可能提交來進行情感分析的內容範例：

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>步驟 1：建立要求結構

如需有關要求定義的詳細資訊，請參閱[呼叫文字分析 API](text-analytics-how-to-call-api.md)。 為了方便起見，我們將重申下列各點：

+ 建立一個 POST 要求。 若要檢閱適用於此要求的 API 文件，請參閱[情感分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)\(英文)\。

+ 使用 Azure 文字分析資源或具現化的[文字分析容器](text-analytics-how-to-install-containers.md)，來設定可用來分析情感的 HTTP 端點。 它必須包括 `/sentiment` 資源：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`。

+ 設定要求標頭以包含文字分析作業的[存取金鑰](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在要求主體中，提供您準備用於此分析的 JSON 文件集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或開啟[文件](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) \(英文\) 中的 **API 測試主控台**來建立要求結構，並將它發佈到服務。

## <a name="step-2-post-the-request"></a>步驟 2：張貼要求

分析會在接收要求時執行。 如需您每分鐘和每秒鐘可以傳送的要求大小和數量資訊，請參閱概觀中的[資料限制](../overview.md#data-limits)一節。

請記得，服務是無狀態的。 您的帳戶中並不會儲存任何資料。 結果會在回應中立即傳回。


## <a name="step-3-view-the-results"></a>步驟 3：View the results

情感分析器會將文字明確地分類為正面或負面。 並指派範圍從 0 到 1 的分數。 接近 0.5 的值是中立或不確定的。 0\.5 的分數表示中立。 當無法針對情感分析字串，或字串沒有任何情感時，分數一律為 0.5。 例如，如果您使用英文語言代碼傳入西班牙字串，分數將會是 0.5。

輸出會立即傳回。 您可以將結果串流至接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案。 然後，將輸出匯入應用程式，以便用來排序、搜尋和操作資料。

下列範例顯示本文中文件集合的回應：

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="sentiment-analysis-v3-public-preview"></a>情感分析 V3 公開預覽

[下一版情感分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) 現在已有公開預覽版。 其大幅改善了 API 文字分類和評分的精確度和詳細資料。

> [!NOTE]
> * 情感分析 v3 的要求格式和[資料限制](../overview.md#data-limits)與前版相同。
> * 此外，情感分析 V3：
>    * 目前僅支援英文。
>    * 會在以下區域上市：`Central US`、`Central Canada` 和 `East Asia`。

|功能 |說明  |
|---------|---------|
|更高的精確度     | 在文字文件中偵測正面、中性、負面和混合情感的能力顯著優於舊版。           |
|文件和句子層級的情感分數     | 同時偵測文件及其個別句子的情感。 如果文件包含多個句子，則會為每個句子指派情感分數。         |
|情感類別和分數     | 現在，API 除了會傳回情感分數以外，還會傳回文字的情感分類。 這些類別包括 `positive`、`negative`、`neutral` 和 `mixed`。       |
| 改善的輸出 | 現在，情感分析會同時傳回整份文字文件及其個別句子的資訊。 |

### <a name="sentiment-labeling"></a>情感標籤

情感分析 V3 會傳回句子和文件層級的分數和標籤。 分數和標籤為 `positive`、`negative` 和 `neutral`。 在文件層級也可傳回 `mixed` 情感標籤 (不是分數)。 文件的情感會在彙總句子分數後決定。

| 句子情感                                                        | 傳回的文件標籤 |
|---------------------------------------------------------------------------|----------------|
| 至少有一個正面的句子，且其餘句子皆為中性。 | `positive`     |
| 至少有一個負面的句子，且其餘句子皆為中性。  | `negative`     |
| 至少有一個負面的句子和至少一個正面的句子。         | `mixed`        |
| 所有句子皆為中性。                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>情感分析 V3 的要求範例

下列 JSON 是對新版情感分析所提出的要求範例。 要求的格式與前版相同：

```json
    {
        "documents": [
        {
            "language": "en",
            "id": "1",
            "text": "Hello world. This is some input text that I love."
        },
        {
            "language": "en",
            "id": "2",
            "text": "It's incredibly sunny outside! I'm so happy."
        }
        ],
    }
```

### <a name="sentiment-analysis-v3-example-response"></a>情感分析 V3 的回應範例

雖然要求格式與前版相同，但回應格式則有所變更。 下列 JSON 是新版 API 的範例回應︰

```json
    {
        "documents": [
            {
                "id": "1",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.98570585250854492,
                    "neutral": 0.0001625834556762,
                    "negative": 0.0141316400840878
                },
                "sentences": [
                    {
                        "sentiment": "neutral",
                        "sentenceScores": {
                            "positive": 0.0785155147314072,
                            "neutral": 0.89702343940734863,
                            "negative": 0.0244610067456961
                        },
                        "offset": 0,
                        "length": 12
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.98570585250854492,
                            "neutral": 0.0001625834556762,
                            "negative": 0.0141316400840878
                        },
                        "offset": 13,
                        "length": 36
                    }
                ]
            },
            {
                "id": "2",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.89198976755142212,
                    "neutral": 0.103382371366024,
                    "negative": 0.0046278294175863
                },
                "sentences": [
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.78401315212249756,
                            "neutral": 0.2067587077617645,
                            "negative": 0.0092281140387058
                        },
                        "offset": 0,
                        "length": 30
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.99996638298034668,
                            "neutral": 0.0000060341349126,
                            "negative": 0.0000275444017461
                        },
                        "offset": 31,
                        "length": 13
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="example-c-code"></a>範例 C# 程式碼

您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs) 上找到呼叫此情感分析版本的範例 C# 應用程式。

## <a name="summary"></a>總結

在本文中，您已了解使用 Azure 認知服務中的文字分析進行情感分析的概念和工作流程。 摘要說明：

+ [情感分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) \(英文\) 僅針對特定語言提供。
+ 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
+ 使用對您訂用帳戶有效的個人化[存取金鑰和端點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)，將要求 POST 到 `/sentiment` 端點。
+ 回應輸出 (其包含針對每個文件識別碼的情感分數) 可以串流處理到任何可接受 JSON 的應用程式。 範例應用程式包括 Excel 和 Power BI 等等。

## <a name="see-also"></a>另請參閱

 [文字分析概觀](../overview.md)[常見問題集 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文字分析產品頁面](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [擷取關鍵片語](text-analytics-how-to-keyword-extraction.md)
