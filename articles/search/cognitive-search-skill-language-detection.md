---
title: 語言偵測認知搜尋技能 - Azure 搜尋服務
description: 評估非結構化文字，並針對每筆記錄傳回語言識別項，其分數表示 Azure 搜尋服務擴充管線中分析的強度。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 605f4c639cfc8c0f9732f7347532e1bd7edc055f
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404649"
---
#   <a name="language-detection-cognitive-skill"></a>語言偵測認知技能

**語言偵測**技能偵測到的語言輸入文字，並報告在要求上送出每個文件的單一語言程式碼。 語言代碼各配有一個分數，表示分析的強度。 這項技能會使用認知服務中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)所提供的機器學習模型。

當您需要提供文字的語言作為其他技能的輸入時 (例如[情感分析技能](cognitive-search-skill-sentiment.md)或[文字分割技能](cognitive-search-skill-textsplit.md))，這項功能就特別有用。

語言偵測利用 Bing 的自然語言處理程式庫，這超過的數字的[支援的語言和區域](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support)列出文字分析。 確切的語言清單尚未發行，但包含廣泛地說出的所有語言，再加上變體，方言，以及某些地區和文化特性的語言。 如果您有以較不常用的語言表示的內容，您可以[試用語言偵測 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)來查看它是否會傳回程式碼。 無法偵測到的語言的回應是`unknown`。

> [!NOTE]
> 從 2018 年 12 月 21 日開始，您可以[連結認知服務資源](cognitive-search-attach-cognitive-services.md)與 Azure 搜尋服務技能集。 據此，我們將開始收取執行技術集的費用。 自這個日期起，我們也會開始收取文件萃取階段中影像擷取的費用。 從文件中擷取文字的功能則繼續免費提供。
>
> [內建認知技能](cognitive-search-predefined-skills.md)執行會以[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services)收費，費率與直接執行工作時一樣。 影像擷取是 Azure 搜尋服務費用，目前是以預覽版定價提供。 如需詳細資訊，請參閱 [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)或[計費方式](search-sku-tier.md#how-billing-works)。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應該是 50,000 個字元 (以 `String.Length` 為測量單位)。 如果您需要先分割資料，再將該資料傳送至情感分析器，您可以使用[文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-inputs"></a>技能輸入

這些參數會區分大小寫。

| 輸入     | 描述 |
|--------------------|-------------|
| text | 要分析的文字。|

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | 描述 |
|--------------------|-------------|
| languageCode | 所識別語言的 ISO 6391 語言代碼。 例如，"en"。 |
| languageName | 語言的名稱。 例如，"English"。 |
| 分數 | 0 到 1 之間的值。 正確識別語言的可能性。 如果句子有混合的語言，分數可能會小於 1。  |

##  <a name="sample-definition"></a>範例定義

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>範例輸入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>範例輸出

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>錯誤案例
如果文字以不支援的語言表示，則會產生錯誤，而且不會傳回語言識別項。

## <a name="see-also"></a>請參閱

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
