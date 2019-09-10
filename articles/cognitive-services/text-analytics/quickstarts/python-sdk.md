---
title: 快速入門：適用於 Python 的文字分析用戶端程式庫 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 透過此快速入門開始使用 Azure 認知服務中的文字分析 API。
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 0543bc639e60c65d9ab5a6cc810ddf6b7d10087a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142735"
---
# <a name="quickstart-text-analytics-client-library-for-python"></a>快速入門：適用於 Python 的文字分析用戶端程式庫
<a name="HOLTop"></a>

開始使用適用於 Python 的文字分析用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 

使用適用於 Python 的文字分析用戶端程式庫執行：

* 情感分析
* 語言偵測
* 實體辨識
* 關鍵片語擷取


[參考文件](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [套件 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [範例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>建立文字分析 Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](../../cognitive-services-apis-create-account.md)或 [Azure CLI](../../cognitive-services-apis-create-account-cli.md) 在本機電腦上建立文字分析的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services/#decision)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。  
* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源

從試用版訂用帳戶或資源取得金鑰後，請為名為 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 的金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

在您慣用的編輯器或 IDE 中，建立新的 Python 應用程式。 然後匯入下列程式庫。

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

為資源的 Azure 端點和訂用帳戶金鑰建立變數。 從環境變數 TEXT_ANALYTICS_SUBSCRIPTION_KEY 和 TEXT_ANALYTICS_ENDPOINT 取得這些值。 如果您在開始編輯應用程式後才建立這些環境變數，則必須先關閉您使用的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```python
import os

key_var_name = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TEXT_ANALYTICS_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]

credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="object-model"></a>物件模型

文字分析用戶端是一個 [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以單一字串或批次的形式) 的方法。 

文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 

回應物件是一份清單，包含每個文件的分析資訊。 

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的文字分析用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [情感分析](#sentiment-analysis)
* [語言偵測](#language-detection)
* [實體辨識](#entity-recognition)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端

以 `credentials` 和 `text_analytics_url` 作為參數來建立新的 [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) 物件。 針對您的文字分析訂用帳戶，使用正確的 Azure 區域 (例如 `westcentralus`)。

```python
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>情感分析

呼叫 [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 函式並取得結果。 接著逐一查看結果，並列印每個文件的識別碼和人氣分數。 接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    }
]
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>輸出

```console
Document Id:  1 , Sentiment Score:  0.87
```

## <a name="language-detection"></a>語言偵測

使用稍早建立的用戶端，呼叫 [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 函式並取得結果。 接著逐一查看結果，並列印每個文件的識別碼以及第一個傳回的語言。

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    }
]
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>輸出

```console
Document Id:  1 , Language:  English
```

## <a name="entity-recognition"></a>實體辨識

使用稍早建立的用戶端，呼叫 [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 函式並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼以及其中包含的實體。

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    }
]
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```

### <a name="output"></a>輸出

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
```

## <a name="key-phrase-extraction"></a>關鍵片語擷取

使用稍早建立的用戶端，呼叫 [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 函式並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼以及其中包含的關鍵片語。

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    }
]
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```


### <a name="output"></a>輸出

```console
Document Id:  3
    Phrases:
         cat
         veterinarian
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除與資源群組相關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [文字分析概觀](../overview.md)
* [情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [實體辨識](../how-tos/text-analytics-how-to-entity-linking.md)
* [偵測語言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [辨識語言](../how-tos/text-analytics-how-to-language-detection.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py) 上找到。
