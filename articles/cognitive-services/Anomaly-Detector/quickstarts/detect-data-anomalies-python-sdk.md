---
title: 快速入門：偵測資料異常使用異常偵測器程式庫和 Python |Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 使用異常偵測器 API 來偵測異常狀況的資料序列視為一個批次，或對串流資料。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 1d89ed8f40547142d41af9c587fc8fc000fa4dd9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503708"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>快速入門：適用於 Python 的異常偵測器用戶端程式庫

開始使用異常偵測器的用戶端程式庫適用於.NET。 請遵循下列步驟來安裝套件，並試試我們的範例程式碼的基本工作。 異常偵測器服務，可讓您在時間序列資料中尋找異常狀況，會自動使用最適合的模型，不論產業、 案例中或資料磁碟區。

## <a name="key-concepts"></a>重要概念

使用 Python 的異常偵測器的用戶端程式庫：

* 偵測異常狀況，在時間序列資料集，做為批次要求
* 偵測時間序列中的最新的資料點的異常狀態

[程式庫參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [封裝 (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [範例](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶-[免費建立一個](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* [Pandas 資料分析程式庫](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>設定

### <a name="create-an-anomaly-detector-resource"></a>建立異常偵測器資源

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後, 您可以安裝與用戶端程式庫：

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>物件模型

異常偵測器用戶端[AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python)向 Azure 中使用您的金鑰的物件。 用戶端提供異常偵測兩個的方法：使用整個資料集[entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)，以及端點的最新的資料上的 使用[Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)。 

時間序列資料傳送以一連串[點](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python)中[要求](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python)物件。 `Request`物件包含用來描述資料屬性 ([資料粒度](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)例如)，和異常偵測的參數。 

異常偵測器回應[LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)或是[EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)根據所使用的方法的物件。 

## <a name="getting-started"></a>開始使用

在您慣用的編輯器或 IDE 中建立新的 Python 應用程式。 然後將下列匯入宣告新增至您的檔案。 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> 本快速入門假設您已經[建立的環境變數](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)異常偵測器索引鍵，名為`ANOMALY_DETECTOR_KEY`。

建立您的金鑰做為環境變數、 時間序列資料檔案中和您的訂用帳戶的 azure 位置的路徑的變數。 例如： `westus2` 。 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>程式碼範例 

這些程式碼片段會示範如何執行異常偵測器的用戶端程式庫適用於.NET 的下列動作：

* [驗證用戶端](#authenticate-the-client)
* [從檔案載入時間序列資料集](#load-time-series-data-from-a-file)
* [偵測異常的整個資料集](#detect-anomalies-in-the-entire-data-set) 
* [偵測異常的狀態最新的資料點](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>驗證用戶端

將您的 azure 位置的變數新增至端點，並使用您的金鑰進行驗證用戶端。

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>從檔案載入時間序列資料

下載範例資料從本快速入門[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. 在瀏覽器中以滑鼠右鍵按一下**Raw**
2. 按一下 **儲存連結**
3. 將您的應用程式的目錄，檔案儲存為.csv 檔案。

此時間序列資料會格式化為.csv 檔案，並會傳送到的異常偵測器 API。

載入您的資料檔案使用 Pandas 程式庫的`read_csv()`方法，並請儲存您的資料數列的空白清單變數。 逐一查看檔案，並附加資料作為[點](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python)物件。 此物件會包含時間戳記和.csv 資料檔案的資料列的數值。 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

建立[要求](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python)物件與您的時間序列，而[資料粒度](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)（或週期性） 其資料點。 例如： `Granularity.daily` 。

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>偵測異常的整個資料集 

呼叫 API 來偵測異常行為，透過使用用戶端的整個時間序列資料[entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)方法。 儲存傳回[EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)物件。 逐一查看的回應`is_anomaly`清單，然後列印的任何索引`true`值。 如果找不到任何，這些值會對應到的異常資料點索引。

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測異常的狀態最新的資料點

呼叫異常偵測器 API，以判斷最新的資料點是否使用用戶端的異常[last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)方法，並傳回存放區[LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)物件。 回應的`is_anomaly`值是布林值，指定該時間點異常的狀態。  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>執行應用程式

執行應用程式，在 IDE 中，或在命令列使用`python`命令和您的檔案名稱。
 
## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除的認知服務訂用帳戶，您可以刪除資源或資源群組。 刪除資源群組時，也會刪除資源群組相關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

您也可以執行下列 cloud shell 命令來移除資源群組和其相關聯的資源。 這可能需要幾分鐘才能完成。 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[使用 Azure Databricks 的資料流異常偵測](../tutorials/anomaly-detection-streaming-databricks.md)

* 什麼是[異常偵測器 API？](../overview.md)
* [最佳做法](../concepts/anomaly-detection-best-practices.md)使用異常偵測器 API 時。
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs) 上找到。
