---
title: 快速入門：使用 Python 來呼叫文字分析 API
titleSuffix: Azure Cognitive Services
description: 取得資訊和程式碼範例，協助您在 Azure 認知服務中快速開始使用文字分析 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/09/2019
ms.author: aahi
ms.openlocfilehash: 9ae894bee803c60b56a1bfacd5667f355aa44d2b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799990"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>快速入門：使用 Python REST API 呼叫文字分析認知服務 
<a name="HOLTop"></a>

透過此快速入門，開始使用文字分析 REST API 和 Python 來分析語言。 本文示範如何[偵測語言](#Detect)、[分析情感](#SentimentAnalysis)、[擷取關鍵片語](#KeyPhraseExtraction)，以及[識別已連結的實體](#Entities)。

如需 API 的技術文件，請參閱 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>必要條件

* [Python 3.x](https://python.org)

* 在註冊時產生的[端點和存取金鑰](../How-tos/text-analytics-how-to-access-key.md)。

* Python Requests 程式庫
    
    您可以使用此命令來安裝程式庫：

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

在您最愛的編輯器或 IDE 中建立新的 Python 應用程式。 在您的檔案中新增下列匯入項目。

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
from IPython.display import HTML
```

建立訂用帳戶金鑰的變數，以及文字分析 REST API 的端點。 確認端點中的區域會對應到您註冊時所使用的區域 (例如 `westcentralus`)。 如果您使用免費試用金鑰，則不需變更任何項目。
    
```python
subscription_key = "<ADD YOUR KEY HERE>"
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
```

下列各節說明如何呼叫 API 的各項功能。

<a name="Detect"></a>

## <a name="detect-languages"></a>偵測語言

將 `languages` 附加至文字分析基底端點，以形成語言偵測 URL。 例如：`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
    
```python
language_api_url = text_analytics_base_url + "languages"
```

對 API 的酬載是由 `documents` 清單所組成，其為包含 `id` 和 `text` 屬性的 Tuple。 `text` 屬性會儲存要分析的文字，而 `id` 可以是任何值。 

```python
documents = { "documents": [
    { "id": "1", "text": "This is a document written in English." },
    { "id": "2", "text": "Este es un document escrito en Español." },
    { "id": "3", "text": "这是一个用中文写的文件" }
]}
```

使用 Requests 程式庫將文件傳送給 API。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後使用 `requests.post()` 傳送要求。 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>輸出

```json
{
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>分析人氣

若要偵測一組文件的情感 (範圍介於正或負之間)，請將 `sentiment` 附加至文字分析基底端點，以形成語言偵測 URL。 例如：`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = text_analytics_base_url + "sentiment"
```

如同語言偵測範例，建立具有 `documents` 索引鍵的字典，而該索引鍵是由文件清單所組成。 每份文件都是一個由 `id`、要分析的 `text`，以及文字的 `language` 所組成的 Tuple。 

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

使用 Requests 程式庫將文件傳送給 API。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後使用 `requests.post()` 傳送要求。 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>輸出

文件的情感分數會介於 0.0 和 1.0 之間，分數越高表示情感越正面。

```json
{
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[

  ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>擷取關鍵片語
 
若要從一組文件中擷取關鍵片語，請將 `keyPhrases` 附加至文字分析基底端點，以形成語言偵測 URL。 例如：`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = text_analytics_base_url + "keyPhrases"
```

此文件集合與用於情感分析範例的集合相同。

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

使用 Requests 程式庫將文件傳送給 API。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後使用 `requests.post()` 傳送要求。 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>輸出

```json
{
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[

  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>識別實體

若要識別文字文件中的知名實體 (人員、地點和事物)，請將 `entities` 附加至文字分析基底端點，以形成語言偵測 URL。 例如：`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`
    
```python
entities_url = text_analytics_base_url + "entities"
```

如先前的範例，建立文件的集合。 

```python
documents = {"documents" : [
  {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

使用 Requests 程式庫將文件傳送給 API。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後使用 `requests.post()` 傳送要求。

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
```

### <a name="output"></a>輸出

```json
{'documents': [{'id': '1',
   'entities': [{'name': 'Microsoft',
     'matches': [{'wikipediaScore': 0.502357972145024,
       'entityTypeScore': 1.0,
       'text': 'Microsoft',
       'offset': 0,
       'length': 9}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Microsoft',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Microsoft',
     'bingId': 'a093e9b9-90f5-a3d5-c4b8-5855e1b01f85',
     'type': 'Organization'},
    {'name': 'Bill Gates',
     'matches': [{'wikipediaScore': 0.5849375085784292,
       'entityTypeScore': 0.999847412109375,
       'text': 'Bill Gates',
       'offset': 25,
       'length': 10}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Bill Gates',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Bill_Gates',
     'bingId': '0d47c987-0042-5576-15e8-97af601614fa',
     'type': 'Person'},
    {'name': 'Paul Allen',
     'matches': [{'wikipediaScore': 0.5314163053043621,
       'entityTypeScore': 0.9988409876823425,
       'text': 'Paul Allen',
       'offset': 40,
       'length': 10}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Paul Allen',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Paul_Allen',
     'bingId': 'df2c4376-9923-6a54-893f-2ee5a5badbc7',
     'type': 'Person'},
    {'name': 'April 4',
     'matches': [{'wikipediaScore': 0.37312706493069636,
       'entityTypeScore': 0.8,
       'text': 'April 4',
       'offset': 54,
       'length': 7}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'April 4',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/April_4',
     'bingId': '52535f87-235e-b513-54fe-c03e4233ac6e',
     'type': 'Other'},
    {'name': 'April 4, 1975',
     'matches': [{'entityTypeScore': 0.8,
       'text': 'April 4, 1975',
       'offset': 54,
       'length': 13}],
     'type': 'DateTime',
     'subType': 'Date'},
    {'name': 'BASIC',
     'matches': [{'wikipediaScore': 0.35916049097766867,
       'entityTypeScore': 0.8,
       'text': 'BASIC',
       'offset': 89,
       'length': 5}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'BASIC',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/BASIC',
     'bingId': '5b16443d-501c-58f3-352e-611bbe75aa6e',
     'type': 'Other'},
    {'name': 'Altair 8800',
     'matches': [{'wikipediaScore': 0.8697256853652899,
       'entityTypeScore': 0.8,
       'text': 'Altair 8800',
       'offset': 116,
       'length': 11}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Altair 8800',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Altair_8800',
     'bingId': '7216c654-3779-68a2-c7b7-12ff3dad5606',
     'type': 'Other'}]}],
 'errors': []}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另請參閱 

 [文字分析概觀](../overview.md)  
 [常見問題集 (FAQ)](../text-analytics-resource-faq.md)
