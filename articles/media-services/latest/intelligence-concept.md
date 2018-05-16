---
title: Azure 媒體智慧 | Microsoft 文件
description: 使用 Azure 媒體服務時，您可以使用 AudioAnalyzerPreset 和 VideoAnalyzerPreset 分析音訊和視訊內容。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: 804a418f6ee88974d6e74a2c18bc5d01b6adf838
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="media-intelligence"></a>媒體智慧

Azure 媒體服務 REST v3 API 可讓您分析音訊及視訊內容。 若要分析您的內容，請建立一個**轉換**並提交一個使用下列預設 **AudioAnalyzerPreset** 或 **VideoAnalyzerPreset** 其中之一的**工作**。 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** 可讓您從音訊檔案或視訊檔案擷取多個音訊深入資訊。 輸出會包含 JSON 檔案 (包含所有的深入資訊) 和音訊文字記錄的 VTT 檔案。 此預設接受的屬性會指定輸入檔案的語言 ([BCP47](https://tools.ietf.org/html/bcp47) 字串形式)。 音訊的深入資訊包括：

* 音訊轉譯 – 具有時間戳記的旁白文字記錄。 支援多種語言
* 演講者索引 - 演講者和相應旁白之間的對應
* 口語情緒分析 – 針對音訊轉譯執行的情緒分析輸出
* 關鍵字 – 從音訊轉譯擷取的關鍵字。

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** 可讓您從視訊檔案擷取多個音訊和視訊深入資訊。 輸出包含 JSON 檔案 (包含所有的深入資訊)、視訊文字記錄的 VTT 檔案，以及縮圖的集合。 此預設也接受 [BCP47](https://tools.ietf.org/html/bcp47) 字串 (表示視訊的語言) 當作屬性。 視訊深入資訊包含上述的音訊深入資訊和下列其他項目：

* 臉部追蹤 – 臉部在視訊中出現的時間。 每一張臉都有臉部識別碼和相對應的縮圖集合
* 視覺文字 – 透過光學字元辨識偵測到的文字。 文字會加上時間戳記，並且也用來擷取關鍵字 (除了音訊文字記錄以外)
* 主要畫面格 – 從視訊擷取的主要畫面格集合
* 視覺內容合適性 – 視訊中被標記為成人或猥亵性質的部分
* 註釋 - 根據預先定義的物件模型，為視訊加上註解的結果

##  <a name="insightsjson-elements"></a>insights.json 元素

輸出會包含一個 JSON 檔案 (insights.json) 並包含視訊或音訊中所有找到的深入資訊。 json 可以包含下列其中一個元素：

### <a name="transcript"></a>文字記錄

|Name|說明|
|---|---|
|id|行識別碼。|
|text|文字記錄本身。|
|語言|文字記錄語言。 用於支援文字記錄，其中每一行可以有不同的語言。|
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

|Name|說明|
|---|---|
|id|OCR 行識別碼。|
|text|OCR 文字。|
|信賴度|辨識信賴。|
|語言|OCR 語言。|
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

### <a name="keywords"></a>關鍵字

|Name|說明|
|---|---|
|id|關鍵字識別碼。|
|text|關鍵字。|
|信賴度|關鍵字的辨識信賴。|
|語言|關鍵字語言 (轉譯時)。|
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

### <a name="faces"></a>臉部

|Name|說明|
|---|---|
|id|臉部識別碼。|
|name|臉部名稱。 它可以是 Unknown #0、已識別的名人或客戶培訓人員。|
|信賴度|臉部識別信賴。|
|說明|如果是名人，則為其描述 (Satya Nadella 出生在...)。 |
|thumbnalId|該臉部的縮圖識別碼。|
|knownPersonId|如果是已知人物，則為其內部識別碼。|
|referenceId|若為 Bing 名人，則為其 Bing 識別碼。|
|referenceType|目前只是 Bing。|
|title|若為名人，則為其職稱 (例如 Microsoft CEO)。|
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

### <a name="labels"></a>標籤

|Name|說明|
|---|---|
|id|標籤識別碼。|
|name|標籤名稱 (例如，電腦、電視)。|
|語言|標籤名稱語言 (轉譯時)。 BCP-47|
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

### <a name="shots"></a>擷取畫面

|Name|說明|
|---|---|
|id|擷取畫面識別碼。|
|keyFrames|擷取畫面的主要畫面格清單 (每個主要畫面格都有一個識別碼和執行個體的時間範圍清單)。|
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
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
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
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="audioeffects"></a>audioEffects

|Name|說明|
|---|---|
|id|音訊效果識別碼。|
|type|音訊效果類型 (例如，拍手聲、說話、無聲)。|
|執行個體|此音訊效果曾出現的時間範圍清單。|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```


### <a name="sentiments"></a>人氣

人氣會依據其 sentimentType 欄位 (Positive/Neutral/Negative) 加以彙總。 例如：0-0.1、0.1-0.2。

|Name|說明|
|---|---|
|id|人氣識別碼。|
|averageScore |所有該人氣類型執行個體的總分平均值 - Positive/Neutral/Negative|
|執行個體|此人氣曾出現的時間範圍清單。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Azure 媒體服務分析視訊](analyze-videos-tutorial-with-api.md)
