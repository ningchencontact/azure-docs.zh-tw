---
title: OCR 認知搜尋技能 - Azure 搜尋服務
description: 在 Azure 搜尋服務擴充管線中，使用光學字元辨識 (OCR) 擷取影像檔案中的文字。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: dbbff7644d0c9375a4d2a145769d09a786b01c25
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412320"
---
# <a name="ocr-cognitive-skill"></a>OCR 認知技能

光學字元辨識 (OCR) 技能可辨識影像檔案中的列印和手寫文字。 這項技能會使用認知服務中[電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)所提供的機器學習模型。 **OCR** 技能對應至下列功能：

+ 當 textExtractionAlgorithm 設定為 "handwritten" 時，會使用 ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) 功能。
+ 當 textExtractionAlgorithm 設定為 "printed" 時，英文以外的語言會使用 ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) 功能。 若為英文，則會對列印的文字使用新的 ["Recognize Text"](../cognitive-services/computer-vision/concept-recognizing-text.md) 功能。

**OCR** 技能可從影像檔擷取文字。 支援的檔案格式包括：

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF

> [!NOTE]
> 從 2018 年 12 月 21 日開始，您可以[連結認知服務資源](cognitive-search-attach-cognitive-services.md)與 Azure 搜尋服務技能集。 這可讓我們開始收取執行技能集的費用。 自這個日期起，我們也會開始收取文件萃取階段中影像擷取的費用。 從文件中擷取文字的功能則繼續免費提供。
>
> [內建認知技能](cognitive-search-predefined-skills.md)執行會以[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services)收費，費率與直接執行工作時一樣。 影像擷取是 Azure 搜尋服務費用，目前是以預覽版定價提供。 如需詳細資訊，請參閱 [Azure 搜尋服務定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)或[計費方式](search-sku-tier.md#how-billing-works)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 參數名稱     | 說明 |
|--------------------|-------------|
| detectOrientation | 啟用自動偵測影像方向。 <br/> 有效值：true/false。|
|defaultLanguageCode | <p>  輸入文字的語言代碼。 支援的語言包括： <br/> zh-Hans (簡體中文) <br/> zh-Hant (繁體中文) <br/>cs (捷克文) <br/>da (丹麥文) <br/>nl (荷蘭文) <br/>en (英文) <br/>fi (芬蘭文)  <br/>fr (法文) <br/>  de (德文) <br/>el (希臘文) <br/> hu (匈牙利文) <br/> it (義大利文) <br/>  ja (日文) <br/> ko (韓文) <br/> nb (挪威文) <br/>   pl (波蘭文) <br/> pt (葡萄牙文) <br/>  ru (俄文) <br/>  es (西班牙文) <br/>  sv (瑞典文) <br/>  tr (土耳其文) <br/> ar (阿拉伯文) <br/> ro (羅馬尼亞文) <br/> sr-Cyrl (塞爾維亞文 - 斯拉夫) <br/> sr-Latn (塞爾維亞文 - 拉丁) <br/>  sk (斯洛伐克文) <br/>  unk (未知) <br/><br/> 如果未指定語言代碼或為 Null，則語言會設為英文。 如果語言明確設為 "unk"，則會自動偵測語言。 </p> |
| textExtractionAlgorithm | 「列印」或「手寫」。 「手寫」文字辨識 OCR 演算法目前為預覽狀態，並且僅支援英文。 |

## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 說明                                          |
|---------------|------------------------------------------------------|
| 映像         | 複雜類型。 目前僅適用於當 ```imageAction``` 設為 ```generateNormalizedImages``` 時，由 Azure Blob 索引子產生的「/document/normalized_images」欄位。 如需詳細資訊，請參閱[範例](#sample-output)。|


## <a name="skill-outputs"></a>技能輸出
| 輸出名稱     | 說明                   |
|---------------|-------------------------------|
| text          | 從影像擷取的純文字。   |
| layoutText    | 說明擷取之文字以及文字發現位置的複雜類型。|


## <a name="sample-definition"></a>範例定義

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>範例文字和 layoutText 輸出

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>範例：將從內嵌影像擷取的文字，與文件內容合併。

文字合併常見的使用案例，是將影像的文字表示法 (來自 OCR 技能的文字，或影像標題) 合併至文件的內容欄位。 

下列的範例技能集會建立 *merged_text* 欄位。 此欄位包含文件的文字內容，以及該文件每個內嵌影像中經過 OCR 處理的文字。 

#### <a name="request-body-syntax"></a>要求本文的語法
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
上述技能集範例假設正規化映像欄位存在。 若要產生此欄位，請將索引子定義中的 *imageAction* 組態設定為 *generateNormalizedImages*，如下所示：

```json
{  
   //...rest of your indexer definition goes here ... 
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>另請參閱
+ [預先定義的技能](cognitive-search-predefined-skills.md)
+ [TextMerger 技能](cognitive-search-skill-textmerger.md) (英文)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立索引子 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
