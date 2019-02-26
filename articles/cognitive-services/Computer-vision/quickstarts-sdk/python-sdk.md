---
title: 快速入門：Python SDK
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您會了解如何使用 Python SDK 來進行常見工作。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: pafarley
ms.openlocfilehash: 3043067f326f782c51be38382070ae0db0e90f4d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314185"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>適用於 Python 的 Azure 認知服務電腦視覺 SDK

電腦視覺服務可供開發人員存取進階演算法，以處理影像及傳回資訊。 電腦視覺演算法可根據您感興趣的視覺化功能，以不同的方式分析影像的內容。 例如，電腦視覺可判斷影像中是否包含成人或猥褻內容、尋找影像中的所有臉部，取得手寫或列印的文字。 此服務可處理熱門的影像格式，例如 JPEG 和 PNG。 

您可以在應用程式中使用電腦視覺來：

- 深入分析影像
- 擷取影像中的文字
- 產生縮圖

在尋找更多文件嗎？

* [SDK 參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [認知服務電腦視覺文件](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶][azure_sub]
* Azure [電腦視覺資源][computervision_resource]
* [Python 3.6+][python]

如果您需要電腦視覺 API 帳戶，則可以使用這個 [Azure CLI][azure_cli] 命令來建立：

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind ComputerVision \
    --sku S1 \
    --yes
```

## <a name="installation"></a>安裝

您也可以選擇在[虛擬環境][venv]內，使用 [pip][pip] 安裝 Azure 認知服務電腦視覺 SDK。

### <a name="configure-a-virtual-environment-optional"></a>設定虛擬環境 (選擇性)

如果您使用[虛擬環境][virtualenv]，則可以讓基底系統與 Azure SDK 環境彼此隔離，但您不一定要這麼做。 執行下列命令，使用 [venv][venv] 先設定再輸入虛擬環境，例如 `cogsrv-vision-env`：

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```

### <a name="install-the-sdk"></a>安裝 SDK

使用 [pip][pip] 安裝適用於 Python 的 Azure 認知服務電腦視覺 SDK [套件][pypi_computervision]：

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

在建立電腦視覺資源後，您需要其**區域**及其中一個**帳戶金鑰**來具現化用戶端物件。

在建立 [ComputerVisionAPI][ref_computervisionclient] 用戶端物件的執行個體時，請使用這些值。 

### <a name="get-credentials"></a>取得認證

請使用下面的 [Azure CLI][cloud_shell] 程式碼片段，來為兩個環境變數填入電腦視覺帳戶**區域**及其中一個**金鑰** (您也可以在 [Azure 入口網站][azure_portal]找到這些值)。 此程式碼片段會針對 Bash Shell 加以格式化。

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```

### <a name="create-client"></a>建立用戶端

在填入 `ACCOUNT_REGION` 和 `ACCOUNT_KEY` 環境變數後，您可以建立 [ComputerVisionAPI][ref_computervisionclient] 用戶端物件。

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

## <a name="usage"></a>使用量

在初始化 [ComputerVisionAPI][ref_computervisionclient] 用戶端物件後，您可以：

* 分析影像：您可以分析影像來找出某些特徵，例如臉部、色彩、標記。   
* 產生縮圖：建立自訂 JPEG 影像來作為原始影像的縮圖。
* 取得影像的描述：根據影像的主題領域取得影像的描述。 

如需此服務的詳細資訊，請參閱[什麼是電腦視覺？][computervision_docs]。

## <a name="examples"></a>範例

下列各節提供數個程式碼片段，內容涵蓋一些最常見的電腦視覺工作，包括：

* [分析影像](#analyze-an-image)
* [取得主題領域清單](#get-subject-domain-list)
* [依領域分析影像](#analyze-an-image-by-domain)
* [取得影像的文字描述](#get-text-description-of-an-image)
* [取得影像中的手寫文字](#get-text-from-image)
* [產生縮圖](#generate-thumbnail)

### <a name="analyze-an-image"></a>分析影像

您可以使用 [`analyze_image`][ref_computervisionclient_analyze_image] 分析影像來找出某些特徵。 請使用 [`visual_features`][ref_computervision_model_visualfeatures] 屬性來設定要對影像執行的分析類型。 常見的值為 `VisualFeatureTypes.tags` 和 `VisualFeatureTypes.description`。

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>取得主題領域清單

請使用 [`list_models`][ref_computervisionclient_list_models] 來檢閱用於分析影像的主題領域。 在[依領域分析影像](#analyze-an-image-by-domain)時會用到這些領域名稱。 `landmarks` 便是領域的其中一個範例。

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>依領域分析影像

您可以使用 [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain] 來依主題領域分析影像。 請取得[所支援主題領域的清單](#get-subject-domain-list)，以便使用正確的領域名稱。  

```Python
domain = "landmarks"
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>取得影像的文字描述

您可以使用 [`describe_image`][ref_computervisionclient_describe_image] 來取得影像的語言式文字描述。 如果您要對影像的相關關鍵字執行文字分析，請使用 `max_description` 屬性要求幾個描述。 下圖的文字描述範例包括 `a train crossing a bridge over a body of water`、`a large bridge over a body of water` 和 `a train crossing a bridge over a large body of water`。

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>從影像中取得文字

您可以從影像中取得任何手寫或列印的文字。 這需要對 SDK 發出兩個呼叫：[`recognize_text`][ref_computervisionclient_recognize_text] 和 [`get_text_operation_result`][ref_computervisionclient_get_text_operation_result]。 對於 recognize_text 的呼叫是非同步的。 在 get_text_operation_result 呼叫的結果中，您必須先確認第一個呼叫是否已完成且有 [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes]，再擷取文字資料。 結果中會包含文字以及文字的週框座標。 

```Python
url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>產生縮圖

您可以使用 [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail] 來產生影像的縮圖 (JPG)。 縮圖的比例不必和原始影像相同。 

此範例會使用 [Pillow][pypi_pillow] 套件在本機儲存新的縮圖影像。

```Python
from PIL import Image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>疑難排解

### <a name="general"></a>一般

使用 Python SDK 與 [ComputerVisionAPI][ref_computervisionclient] 用戶端物件互動時，系統會用 [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] 類別來傳回錯誤。 服務所傳回的錯誤會對應至 REST API 要求所傳回的相同 HTTP 狀態碼。

例如，如果您嘗試使用無效的金鑰來分析影像，就會傳回 `401` 錯誤。 下列程式碼片段會藉由攔截例外狀況並顯示錯誤的其他相關資訊，來適當地處理[錯誤][ref_httpfailure]。

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>透過重試來處理暫時性錯誤

在使用 [ComputerVisionAPI][ref_computervisionclient] 用戶端時，您可能會遇到由於服務強制執行[速率限制][computervision_request_units]而造成的暫時性失敗，或遇到其他暫時性問題 (例如，網路中斷)。 如需如何處理這些失敗類型的相關資訊，請參閱《雲端設計模式》指南中的[重試模式][azure_pattern_retry]，以及相關的[斷路器模式][azure_pattern_circuit_breaker]。

## <a name="next-steps"></a>後續步驟

### <a name="more-sample-code"></a>更多的程式碼範例

SDK 的 GitHub 存放庫中有數個電腦視覺 Python SDK 範例可供您參考。 這些範例會提供在使用電腦視覺時所經常遇到其他案例的程式碼範例：

* [recognize_text][recognize-text]

### <a name="additional-documentation"></a>其他文件

如需更多的電腦視覺服務文件，請參閱 docs.microsoft.com 上的 [Azure 電腦視覺文件][computervision_docs]。

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

