---
title: 適用於 Azure 認知服務、文字分析 API 的 Python 快速入門 | Microsoft Docs
description: 取得資訊和程式碼範例，以協助您在 Azure 上快速開始使用 Microsoft 認知服務中的文字分析 API。
services: cognitive-services
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: b4c02767320b71912050ad511811767e6b5decf4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368731"
---
# <a name="quickstart-for-text-analytics-api-with-python"></a>文字分析 API 與 Python 的快速入門 
<a name="HOLTop"></a>

本文示範如何使用[文字分析 API](//go.microsoft.com/fwlink/?LinkID=759711) 與 Python 來[偵測語言](#Detect)、[分析情感](#SentimentAnalysis)及[擷取關鍵片語](#KeyPhraseExtraction)。

您可以按一下 [launch Binder] (啟動 Binder) 徽章，在 [MyBinder](https://mybinder.org) \(英文\) 上以 Jupyter Notebook 執行此範例： 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

如需 API 的技術文件，請參閱 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>先決條件

您必須有具備**文字分析 API** 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 您可以使用 **5,000 次交易/月的免費層**來完成此逐步解說。

您也必須具備註冊時產生的[端點和存取金鑰](../How-tos/text-analytics-how-to-access-key.md)。 

若要繼續進行本逐步解說，請以您稍早取得的有效訂用帳戶金鑰取代 `subscription_key`。


```python
subscription_key = None
assert subscription_key
```

接下來，確認 `text_analytics_base_url` 中的區域會對應到您在設定服務時所使用的區域。 如果您使用免費試用金鑰，則不需變更任何項目。


```python
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
```

<a name="Detect"></a>

## <a name="detect-languages"></a>偵測語言

語言偵測 API 會使用[偵測語言方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) \(英文\) 來偵測文字文件的語言。 透過下列 URL 來取得適用於您所在區域的語言偵測 API 服務端點：


```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages


對 API 的酬載會由 `documents` 清單所組成，其中的每份文件依序會包含 `id` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字。 

以適用於語言偵測的任何其他文字取代 `documents` 字典。 


```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

後續幾行程式碼會使用 Python 中的 `requests` 程式庫來呼叫語言偵測 API，以判斷文件中的語言。


```python
import requests
from pprint import pprint
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}


下列程式碼行會將 JSON 資料轉譯為 HTML 表格。


```python
from IPython.display import HTML
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>分析人氣

情感分析 API 會使用[情感分析方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) \(英文\) 來偵測一組文字記錄的情感。 下列範例會為兩份文件進行評分，一份是英文，另一份則是西班牙文。

適用於情感分析的服務端點可透過下列 URL 供您的區域使用：


```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment


如同使用語言偵測範例，服務會利用具有 `documents` 索引鍵的字典來提供，而該索引鍵是由文件清單所組成的。 每份文件都是一個由 `id`、要分析的 `text`，以及文字的 `language` 所組成的 Tuple。 您可以使用上一節的語言偵測 API 來填入此欄位。 


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

情感 API 現在可用來分析文件以了解它們的情感。


```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

    {'documents': [{'id': '1', 'score': 0.7673527002334595},
                   {'id': '2', 'score': 0.18574094772338867},
                   {'id': '3', 'score': 0.5}],
     'errors': []}


文件的情感分數會介於 $0$ 和 $1$ 之間，分數越高表示情感越正面。

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>擷取關鍵片語

關鍵片語擷取 API 會使用[關鍵片語方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) \(英文\) 以從文字文件擷取關鍵片語。 本逐步解說的這個小節會擷取英文和西班牙文文件的關鍵片語。

透過下列 URL 來存取適用於關鍵片語擷取服務的服務端點：


```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases


文件的集合與用於情感分析的相同。


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```


    {'documents': [
        {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
        {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
        {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
        {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
     'errors': []
    }


JSON 物件可以使用下列程式碼，再次轉譯為 HTML 表格：


```python
from IPython.display import HTML
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

## <a name="identify-linked-entities"></a>識別已連結實體

實體連結 API 會使用[實體連結方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) \(英文\) 來識別文字文件中的已知實體。 下列範例會識別英文文件的實體。

透過下列 URL 來存取適用於實體連結服務的服務端點：


```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/entities


文件的集合如下：


```python
documents = {'documents' : [
  {'id': '1', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.'},
  {'id': '2', 'text': 'The Seattle Seahawks won the Super Bowl in 2014.'}
]}
```

現在，可以將文件傳送到文字分析 API 來接收回應。

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```
    {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "name": "Xbox One",
                        "matches": [
                            {
                                "text": "XBox One",
                                "offset": 23,
                                "length": 8
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Xbox One",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                        "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                    },
                    {
                        "name": "Ultra-high-definition television",
                        "matches": [
                            {
                                "text": "4K",
                                "offset": 63,
                                "length": 2
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Ultra-high-definition television",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                        "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                    }
                ]
            },
            {
                "id": "2",
                "entities": [
                    {
                        "name": "2013 Seattle Seahawks season",
                        "matches": [
                            {
                                "text": "Seattle Seahawks",
                                "offset": 4,
                                "length": 16
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "2013 Seattle Seahawks season",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                        "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                    }
                ]
            }
        ],
        "errors": []
    }

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另請參閱 

 [文字分析概觀](../overview.md)  
 [常見問題集 (FAQ)](../text-analytics-resource-faq.md)
