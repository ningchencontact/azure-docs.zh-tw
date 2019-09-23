---
title: 使用 Azure 媒體服務分析視訊和音訊檔案 | Microsoft Docs
description: 使用 Azure 媒體服務時，您可以使用 AudioAnalyzerPreset 和 VideoAnalyzerPreset 來分析音訊和影片內容。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: bc4be8eaafe805e5d9a985b005efe80bc4af1d21
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177991"
---
# <a name="analyzing-video-and-audio-files"></a>分析視訊和音訊檔案

Azure 媒體服務 v3 可讓您透過媒體服務 v3 分析器預設值（如本文所述），利用影片索引子從您的影片和音訊檔案中提取見解。 若您希望獲得更多詳細深入解析，請直接使用影片索引器。 若要了解使用影片索引器以及媒體服務分析器預設的時機，請參閱[比較文件](../video-indexer/compare-video-indexer-with-media-services-presets.md)。

若要使用媒體服務 v3 分析您的內容，請建立**轉換**，並提交使用下列其中一個預設值的**作業**：[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) 或 **AudioAnalyzerPreset**。 下列文章示範如何使用 **VideoAnalyzerPreset**：[教學課程：使用 Azure 媒體服務分析影片](analyze-videos-tutorial-with-api.md)。

> [!NOTE]
> 使用視訊或音訊分析程式的預設時，可使用 Azure 入口網站將帳戶設定為擁有 10 個 S3 編碼保留單元。 如需詳細資訊，請參閱[調整媒體處理](media-reserved-units-cli-how-to.md)。

## <a name="built-in-presets"></a>內建預設

媒體服務目前支援下列內建的分析器預設：  

|**預設名稱**|**案例**|**詳細資料**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|分析音訊|此預設會套用一組預先定義的 AI 型分析作業，包括語音轉譯。 此預設目前支援處理具有單一音訊播放軌 (包含單一語言的語音) 的內容。 您可以使用「語言標記-區域」的 BCP-47 格式，為輸入中的音訊承載指定語言。 支援的語言有英文 ('EN-US' 和 'EN-GB')、西班牙文 ('ES-ES' 和 'ES-MX')、法文 ('fr-FR')、義大利文 ('IT-IT')、日文 ('JA-JP')、葡萄牙文 ('PT-BR')、中文 ('ZH-CN')、德文 ('DE-DE')、阿拉伯文 ('AR-EG')、俄文 ('RU-RU')、印度文 ('HI-IN') 和 韓文 ('KO-KR')。<br/><br/> 如果未指定語言或將語言設為 Null，自動語言偵測會選擇偵測到的第一種語言，並使用選取的語言處理整個檔案。 自動語言偵測功能目前支援英文、簡體中文、法文、德文、義大利文、日文、西班牙文、俄文和葡萄牙文。 目前不支援在偵測到第一個語言後，動態地切換語言。 搭配語音清晰的錄音時，自動語言偵測功能的效果最好。 如果自動語言偵測無法找到該語言，轉譯會切換回英文。|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|分析音訊和視訊|從音訊和視訊擷取見解 (豐富的中繼資料)，並輸出 JSON 格式檔案。 您可以指定在處理視訊檔案時，是否只想擷取音訊見解。 如需詳細資訊，請參閱[分析視訊](analyze-videos-tutorial-with-api.md)。|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|偵測出現在影片中的所有臉部|說明分析影片以偵測所有表面的情況時，所要使用的設定。|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

預設可讓您從音訊檔案或視訊檔案擷取多個音訊深入資訊。 輸出會包含 JSON 檔案 (包含所有的深入資訊) 和音訊文字記錄的 VTT 檔案。 此預設接受的屬性會指定輸入檔案的語言 ([BCP47](https://tools.ietf.org/html/bcp47) 字串形式)。 音訊的深入資訊包括：

* 音訊轉譯 – 具有時間戳記的旁白文字記錄。 支援多種語言
* 演講者索引 - 演講者和相應旁白之間的對應
* 口語情緒分析 – 針對音訊轉譯執行的情緒分析輸出
* 關鍵字 – 從音訊轉譯擷取的關鍵字。

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

預設可讓您從影片檔案擷取多個音訊和影片深入資訊。 輸出包含 JSON 檔案 (包含所有的深入資訊)、視訊文字記錄的 VTT 檔案，以及縮圖的集合。 此預設也接受 [BCP47](https://tools.ietf.org/html/bcp47) 字串 (表示視訊的語言) 當作屬性。 視訊深入資訊包含上述的音訊深入資訊和下列其他項目：

* 臉部追蹤 – 臉部在視訊中出現的時間。 每一張臉都有臉部識別碼和相對應的縮圖集合
* 視覺文字 – 透過光學字元辨識偵測到的文字。 文字會加上時間戳記，並且也用來擷取關鍵字 (除了音訊文字記錄以外)
* 主要畫面格 – 從視訊擷取的主要畫面格集合
* 視覺內容合適性 – 視訊中被標記為成人或猥亵性質的部分
* 註釋 - 根據預先定義的物件模型，為視訊加上註解的結果

##  <a name="insightsjson-elements"></a>insights.json 元素

輸出會包含一個 JSON 檔案 (insights.json) 並包含視訊或音訊中所有找到的深入資訊。 json 可以包含下列其中一個元素：

### <a name="transcript"></a>文字記錄

|Name|描述|
|---|---|
|id|行識別碼。|
|文字|文字記錄本身。|
|language|文字記錄語言。 用於支援文字記錄，其中每一行可以有不同的語言。|
|執行個體|這一行曾出現的時間範圍清單。 如果執行個體是文字記錄，它只能有 1 個執行個體。|

範例：

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|Name|描述|
|---|---|
|id|OCR 行識別碼。|
|文字|OCR 文字。|
|信賴度|辨識信賴。|
|language|OCR 語言。|
|執行個體|此 OCR 曾出現的時間範圍清單 (相同的 OCR 可以出現多次)。|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>臉部

|Name|描述|
|---|---|
|id|臉部識別碼。|
|name|臉部名稱。 它可以是 Unknown #0、已識別的名人或客戶培訓人員。|
|信賴度|臉部識別信賴。|
|description|名人的描述。 |
|thumbnailId|該臉部的縮圖識別碼。|
|knownPersonId|如果是已知人物，則為其內部識別碼。|
|referenceId|若為 Bing 名人，則為其 Bing 識別碼。|
|referenceType|目前只是 Bing。|
|標題|若為名人，則為其職稱 (例如 "Microsoft CEO")。|
|imageUrl|若為名人，則為其影像 URL。|
|執行個體|這些是臉部出現在指定時間範圍中的執行個體。 每個執行個體也會有 thumbnailsId。 |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>擷取畫面

|Name|描述|
|---|---|
|id|擷取畫面識別碼。|
|keyFrames|擷取畫面的主要畫面清單 (每個主要畫面都有一個識別碼和執行個體的時間範圍清單)。 主要畫面格執行個體中有縮圖識別碼欄位，其中包含主要畫面格的縮圖識別碼。|
|執行個體|此擷取畫面的時間範圍清單 (擷取畫面只能有 1 個執行個體)。|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>統計資料

|Name|描述|
|---|---|
|CorrespondenceCount|影片中的對應數目。|
|WordCount|每個說話者的字數。|
|SpeakerNumberOfFragments|說話者在影片中的片段數量。|
|SpeakerLongestMonolog|說話者最長的獨白。 若說話在獨白中有無聲的部分，也會包含在其中。 獨白開頭和結尾的無聲部分則會被移除。| 
|SpeakerTalkToListenRatio|將說話者獨白的時間 (不含無聲的部分) 除以影片的總時間長度。 時間會四捨五入至小數點第三位。|


### <a name="sentiments"></a>人氣

人氣會依據其 sentimentType 欄位 (Positive/Neutral/Negative) 加以彙總。 例如：0-0.1、0.1-0.2。

|Name|描述|
|---|---|
|id|人氣識別碼。|
|averageScore |所有該人氣類型執行個體的總分平均值 - Positive/Neutral/Negative|
|執行個體|此人氣曾出現的時間範圍清單。|
|sentimentType |類型可以是「正面」、「中性」或「負面」。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>標籤

|Name|描述|
|---|---|
|id|標籤識別碼。|
|name|標籤名稱 (例如，電腦、電視)。|
|language|標籤名稱語言 (轉譯時)。 BCP-47|
|執行個體|此標籤曾出現的時間範圍清單 (同一個標籤可以出現多次)。 每個執行個體都有一個信賴度欄位。 |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>關鍵字

|Name|描述|
|---|---|
|id|關鍵字識別碼。|
|文字|關鍵字。|
|信賴度|關鍵字的辨識信賴。|
|language|關鍵字語言 (轉譯時)。|
|執行個體|此關鍵字曾出現的時間範圍清單 (同一個關鍵字可以出現多次)。|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 區塊包含影片索引器偵測到可能含有成人內容的時間範圍。 若 visualContentModeration 是空的，表示未識別到任何成人內容。

經發現含有成人或猥褻內容的影片，只能供私人檢視。 使用者可以要求人工審核影片內容，在此情況下，IsAdult 屬性將包含人工審核的結果。

|Name|描述|
|---|---|
|id|視覺內容仲裁識別碼。|
|adultScore|成人分數 (由內容仲裁提供)。|
|racyScore|辛辣分數 (由內容仲裁提供)。|
|執行個體|視覺內容仲裁出現的時間範圍清單。|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>後續步驟

[教學課程：使用 Azure 媒體服務分析影片](analyze-videos-tutorial-with-api.md)
