---
title: 情感認知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中，從 AI 擴充管線中的文字解壓縮正負情感分數。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791905"
---
# <a name="sentiment-cognitive-skill"></a>情感認知技能

**情感**技能會依循正負面連續性評估非結構化文字，並且針對每個記錄傳回介於 0 與 1 之間的數值分數。 接近 1 的分數表示正面情感；接近 0 的分數表示負面情感。 這項技能會使用認知服務中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)所提供的機器學習模型。

> [!NOTE]
> 當您藉由增加處理次數、新增更多文件或新增更多 AI 演算法來擴展範圍時，您需要[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 Api 時，會產生費用，並在 Azure 認知搜尋中以檔破解階段的形式進行映射解壓縮。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)會說明影像提取定價。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應為5000個字元，如[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)所測量。 如果您需要先分割資料，再將該資料傳送至情感分析器，請使用[文字分割技能](cognitive-search-skill-textsplit.md)。


## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 參數名稱 |                      |
|----------------|----------------------|
| defaultLanguageCode | (選用) 要套用至未明確指定語言之文件的語言代碼。 <br/> 請參閱[支援語言的完整清單](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>技能輸入 

| 輸入名稱 | 描述 |
|--------------------|-------------|
| text | 要分析的文字。|
| languageCode  |  (選用) 此字串表示記錄的語言。 如果未指定此屬性，則預設值為 "en"。 <br/>請參閱[支援語言的完整清單](../cognitive-services/text-analytics/text-analytics-supported-languages.md)。|

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱 | 描述 |
|--------------------|-------------|
| 分數 | 介於 0 與 1 之間的值代表所分析文字的情感。 接近 0 的值具有負面情感、接近 0.5 的值具有中性情感，而接近 1 的值具有正面情感。|


##  <a name="sample-definition"></a>範例定義

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>範例輸出

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>注意
如果是空的，這些記錄不會傳回情感分數。

## <a name="error-cases"></a>錯誤案例
如果不支援語言，則會產生錯誤，並且不會傳回情感分數。

## <a name="see-also"></a>請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md)
