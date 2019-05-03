---
title: 具名實體辨識認知搜尋技能 - Azure 搜尋服務
description: 從 Azure 搜尋服務認知搜尋管道的文字中，擷取人員、位置和組織的具名實體。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7c462971f7ac02e35ab8df5b651da7d0a3f39e1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65022251"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>具名實體辨識認知技能

**具名實體辨識**技能會從文字擷取具名實體。 可用實體包括下列類型：`person`、`location` 以及 `organization`。

> [!IMPORTANT]
> 被取代的技能現已停用的具名的實體辨識[Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)。 支援停止於 2019 年 2 月 15 日，並從產品移除 API，已將它於 2019 5 月 2 日。 請遵循[已淘汰的認知搜尋技能](cognitive-search-skill-deprecated.md)中的建議，以移轉至支援的技能。

> [!NOTE]
> 當您展開範圍增加處理的頻率加入更多的文件，或新增更多的 AI 演算法，您必須[附加的可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 搜尋服務的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 執行內建的技能收費現有[認知服務付移價格](https://azure.microsoft.com/pricing/details/cognitive-services/)。 映像擷取定價會說明[Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>資料限制
記錄的大小上限應該是 50,000 個字元 (以 `String.Length` 為測量單位)。 如果您需要先分割資料，然後再將該資料傳送至關鍵片語擷取器，請考慮使用 [文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 參數名稱     | 描述 |
|--------------------|-------------|
| 類別    | 應擷取的類別陣列。  可能的類別類型：`"Person"`、`"Location"`、`"Organization"`。 如果未提供任何類別，則會傳回所有類型。|
|defaultLanguageCode |  輸入文字的語言代碼。 支援下列語言：`de, en, es, fr, it`|
| minimumPrecision  | 介於 0 和 1 之間的數字。 如果精確度低於此值，則不會傳回實體。 預設值為 0。|

## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 描述                   |
|---------------|-------------------------------|
| languageCode  | 選用。 預設值為 `"en"`。  |
| text          | 要分析的文字。          |

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱     | 描述                   |
|---------------|-------------------------------|
| 人員      | 字串陣列，其中每個字串代表人員名稱。 |
| 位置  | 字串陣列，其中每個字串代表位置。 |
| 組織  | 字串陣列，其中每個字串代表組織。 |
| 實體 | 複雜類型的陣列。 每個複雜類型包括下列欄位： <ul><li>類別 (`"person"`、`"organization"` 或 `"location"`)</li> <li>值 (實際實體名稱)</li><li>位移 (在文字中找到的位置)</li><li>信賴度 (介於 0 和 1 之間的值，表示該值是實際實體的信賴度)</li></ul> |

##  <a name="sample-definition"></a>範例定義

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
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
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>錯誤案例
如果文件的語言程式碼不受支援，則會傳回錯誤，且不會擷取任何實體。

## <a name="see-also"></a>請參閱

+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [實體辨識技能](cognitive-search-skill-entity-recognition.md)
