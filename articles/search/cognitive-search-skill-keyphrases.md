---
title: 關鍵片語擷取認知搜尋技能 - Azure 搜尋服務
description: 評估非結構化的文字，並針對每一筆記錄，透過 Azure 搜尋服務擴充管線傳回關鍵片語清單。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 422b97414142c36669ed449a21c6045fd774581a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341959"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>關鍵片語擷取認知技能

**關鍵片語擷取**技能會評估非結構化的文字，並針對每一筆記錄，傳回關鍵片語的清單。 這項技能會使用認知服務中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)所提供的機器學習模型。

如果您需要快速識別記錄中的主要討論要點，此功能相當有用。 例如，假設輸入文字為「The food was delicious and there were wonderful staff」，服務會傳回「food」和「wonderful staff」。

> [!NOTE]
> 從 2018 年 12 月 21 日開始，您可以[連結認知服務資源](cognitive-search-attach-cognitive-services.md)與 Azure 搜尋服務技能集。 據此，我們將開始收取執行技術集的費用。 自這個日期起，我們也會開始收取文件萃取階段中影像擷取的費用。 從文件中擷取文字的功能則繼續免費提供。
>
> [內建認知技能](cognitive-search-predefined-skills.md)執行會以[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services)收費，費率與直接執行工作時一樣。 影像擷取是 Azure 搜尋服務費用，目前是以預覽版定價提供。 如需詳細資訊，請參閱 [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)或[計費方式](search-sku-tier.md#how-billing-works)。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>資料限制
記錄的大小上限應該是 50,000 個字元 (以 `String.Length` 為測量單位)。 如果您需要先分割資料，然後再將該資料傳送至關鍵片語擷取器，請考慮使用 [文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入                | 描述 |
|---------------------|-------------|
| defaultLanguageCode | (選用) 要套用至未明確指定語言之文件的語言代碼。  如果未指定預設語言代碼，則會使用英文 (en) 做為預設語言代碼。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)。 |
| maxKeyPhraseCount   | (選用) 產生的關鍵片語數量上限。 |

## <a name="skill-inputs"></a>技能輸入

| 輸入     | 描述 |
|--------------------|-------------|
| text | 要分析的文字。|
| languageCode  |  此字串表示記錄的語言。 如果未指定此參數，將使用預設語言代碼來分析記錄。 <br/>請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>範例定義

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>錯誤和警告
如果您提供不支援的語言代碼，則會產生錯誤，而且不會擷取關鍵片語。
如果您的文字是空白的，則會產生警告。
如果文字超過 50,000 個字元，則只會分析前 50,000 個字元，並且會發出警告。

## <a name="see-also"></a>請參閱

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
