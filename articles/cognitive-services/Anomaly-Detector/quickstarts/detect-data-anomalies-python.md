---
title: 快速入門：使用異常偵測器 REST API 與 Python 偵測批次異常行為 | Microsoft Docs
description: 使用異常偵測器 API 來偵測資料數列中的異常狀況，以批次或串流資料為單位。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 0ab39a7bb86671efb6258e3171e3bf7847aa241f
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341732"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>快速入門：使用異常偵測器 REST API 與 Python 偵測時間序列資料中的異常行為

使用本快速入門以開始使用異常偵測器 API 的兩個偵測模式，來偵測時間序列資料中的異常行為。 此 Python 應用程式會傳送包含 JSON 格式時間序列資料的兩個 API 要求，並取得回應。

| API 要求                                        | 應用程式輸出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 偵測批次異常行為                        | JSON 回應包含時間序列資料中每個資料點的異常狀態 (和其他資料)，以及偵測到的任何異常行為的位置。 |
| 偵測最新資料點的異常狀態 | JSON 回應包含時間序列資料中最新資料點的異常狀態 (和其他資料)。                                                                                                                                         |

 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>必要條件

- [Python 2.x 或 3.x](https://www.python.org/downloads/)

- Python 的[要求程式庫](http://docs.python-requests.org)

- JSON 檔案包含時間序列資料點。 此快速入門的範例資料可以在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>建立新的應用程式

1. 在您最愛的文字編輯器或 IDE 中建立新的 Python 檔案。 新增下列匯入。

    ```python
    import requests
    import json
    ```

2. 針對您的訂用帳戶金鑰和端點建立變數。 以下是您可以用於異常偵測的 URI。 這些稍後將會附加至您的服務端點，以建立 API 要求 URL。

    |偵測方法  |URI  |
    |---------|---------|
    |批次偵測    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新資料點上的偵測     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. 開啟檔案並使用 `json.load()` 來讀取 JSON 資料檔案。

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `send_request()` 的新函式，取用上方建立的變數。 然後執行下列步驟。

2. 建立要求標頭的字典。 將 `Content-Type` 設為 `application/json`，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

3. 使用 `requests.post()` 傳送要求。 針對完整要求 URL 合併您的端點與異常偵測 URL，並包含標頭和 json 要求資料。 然後傳回回應。

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>偵測批次異常行為

1. 建立名為 `detect_batch()` 的方法，以在整個批次資料中偵測異常行為。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `send_request()` 方法。

2. 在結果上呼叫 `json.dumps()` 以將其格式化，並列印到主控台。

3. 如果回應包含 `code` 欄位，則列印錯誤碼和錯誤訊息。

4. 否則，在資料集中尋找異常情況的位置。 回應的 `isAnomaly` 欄位包含與指定的資料點是否為異常相關的布林值。 逐一查看清單，並列印任何 `True` 值的索引。 如果有找到，這些值會對應到異常資料點的索引。

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

1. 建立名為 `detect_latest()` 的方法，以判斷時間序列中最新的資料點是否為異常。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方的 `send_request()` 方法。 

2. 在結果上呼叫 `json.dumps()` 以將其格式化，並列印到主控台。

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>載入時間序列資料，並傳送要求

1. 載入 JSON 時間序列資料，開啟檔案處理常式，並在其上使用 `json.load()`。 接著呼叫上面所建立的異常偵測方法。

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>範例回應

成功的回應會以 JSON 格式傳回。 按一下以下連結，在 GitHub 上檢視 JSON 回應：
* [批次偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
