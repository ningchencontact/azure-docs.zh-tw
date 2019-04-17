---
title: 快速入門：偵測異常行為以批次使用異常偵測器 REST API 與 Python |Microsoft Docs
description: 使用異常偵測器 API 來偵測異常狀況的資料序列視為一個批次，或對串流資料。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 6b4ddcadfe63f74d115c155354a276e45c6b53f9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544495"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>快速入門：偵測時間序列資料使用異常偵測器 REST API 和 Python 中的異常

使用本快速入門中，若要開始使用異常偵測器 API 的兩個偵測模式來偵測異常的時間序列資料。 此 Python 應用程式會傳送包含 JSON 格式的時間序列資料，兩個 API 要求，並取得回應。

| API 要求                                        | 應用程式輸出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批次偵測異常行為                        | JSON 回應，包含時間序列資料和任何偵測到的異常狀況的位置中的每個資料點異常狀態 （和其他資料）。 |
| 偵測異常的狀態最新的資料點 | JSON 回應，其中包含最新的資料點，時間序列資料中的異常狀態 （和其他資料）。                                                                                                                                         |

 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>必要條件

- [Python 2.x 或 3.x](https://www.python.org/downloads/)

- [要求程式庫](http://docs.python-requests.org)適用於 python

- JSON 檔案包含時間序列資料點。 本快速入門中的範例資料都位於[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>建立新的應用程式

1. 在您慣用的文字編輯器或 IDE 中，建立新的 python 檔案。 新增下列匯入。

    ```python
    import requests
    import json
    ```

2. 建立您的訂用帳戶金鑰和您的端點的變數。 以下是您可以用於異常偵測的 Uri。 這些將會附加至您的服務端點，稍後若要建立的 API 要求 Url。

    |偵測方法  |URI  |
    |---------|---------|
    |批次偵測    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |在最新的資料點上的偵測     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. 讀取 JSON 資料檔案中開啟它，並使用`json.load()`。

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立新的函式呼叫`send_request()`採用上面所建立的變數。 然後執行下列步驟。

2. 建立要求的標頭的字典。 設定`Content-Type`要`application/json`，並新增您的訂用帳戶金鑰，以`Ocp-Apim-Subscription-Key`標頭。

3. 傳送要求使用`requests.post()`。 結合您的端點，並針對完整的異常偵測 URL 要求 URL，並包含您的標頭和 json 要求資料。 然後傳回回應。

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>以批次偵測異常行為

1. 建立一個方法，叫做`detect_batch()`來偵測異常行為，整個批次的資料。 呼叫`send_request()`上方建立端點、 url、 訂用帳戶金鑰和 json 資料的方法。

2. 呼叫`json.dumps()`格式化，並列印到主控台的結果。

3. 如果回應包含`code`欄位中，列印的錯誤碼和錯誤訊息。

4. 否則資料集中尋找異常狀況的位置。 回應的`isAnomaly`欄位包含與指定的資料點是否為異常狀況相關的布林值。 逐一查看清單中，並列印的任何索引`True`值。 如果找不到任何，這些值會對應到的異常資料點索引。

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測異常的狀態最新的資料點

1. 建立一個方法，叫做`detect_latest()`判斷時間序列中最新的資料點是否為異常狀況。 呼叫`send_request()`上面的方法與您的端點、 url、 訂用帳戶金鑰和 json 資料。 

2. 呼叫`json.dumps()`格式化，並列印到主控台的結果。

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>載入時間序列資料，並傳送要求

1. 載入 JSON 時間序列資料開啟的檔案處理常式，並使用`json.load()`上面。 接著呼叫異常上面所建立的偵測方法。

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>範例回應

成功的回應是以 JSON 格式傳回。 按一下下列連結，可在 GitHub 上檢視 JSON 回應：
* [批次偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)