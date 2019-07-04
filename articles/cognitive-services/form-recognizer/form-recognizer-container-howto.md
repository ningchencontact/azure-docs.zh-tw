---
title: 安裝及執行容器 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 了解如何使用表單辨識器容器來剖析表單和資料表資料。
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: f38752928832b7dee6a7e55f1d25374a64391bbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441888"
---
# <a name="install-and-run-form-recognizer-containers"></a>安裝及執行表單辨識器容器
Azure 的形式辨識器適用於機器學習技術來識別和擷取表單中的索引鍵 / 值組和資料表。 它關聯的索引鍵 / 值組的值與資料表項目，然後輸出包含在原始檔的關聯性的結構化的資料。 

若要為降低複雜性，並輕鬆地將自訂的表單辨識器模型整合到您的工作流程自動化程序或其他應用程式，您可以使用簡單的 REST API 呼叫模型。 只有五個表單的文件 （或一個空白表單和兩個的填滿表單） 需要用到，因此您可以取得結果，快速、 精確地說，和量身訂做您特定的內容。 需要不到任何大量手動操作或廣泛的資料科學專業知識。 而且它不需要資料標籤或資料註解。

|函式|功能|
|-|-|
|表單辨識器| <li>處理 PDF、 PNG 和 JPG 檔<li>定型的 5 種相同的配置的最少的自訂模型 <li>擷取索引鍵 / 值組和資料表資訊 <li>使用 Azure 認知服務電腦視覺 API 辨識文字功能來偵測及擷取映像，在表單內的列印的文字<li>不需要註解或標記|

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

使用表單辨識器容器之前，您必須符合下列必要條件：

|必要項|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> 在 Windows、 Docker 也必須設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應該有基本了解 Docker 的概念，例如登錄、 存放庫、 容器和容器映像，以及的基本知識`docker`命令。|
|Azure CLI| 安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)您主機上。|
|電腦視覺 API 資源| 若要處理掃描的文件和影像，您需要電腦視覺資源。 您可以存取的辨識文字功能做為其中一個 Azure 資源 （REST API 或 SDK） 或*認知服務-辨識-檢索*[容器](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)。 適用一般計費費用。 <br><br>傳入索引鍵和計費的端點，您的電腦視覺資源 （Azure 的雲端或認知服務容器）。 使用此金鑰和計費端點作為 {COMPUTER_VISION_API_KEY} 和 {COMPUTER_VISION_BILLING_ENDPOINT_URI}。<br><br> 如果您使用*認知服務-辨識-檢索*容器，請確定：<br><br>您電腦視覺金鑰形式辨識器容器是電腦視覺中指定的金鑰`docker run`命令取得對於*認知服務-辨識-檢索*容器。<br>您的帳單端點是容器的端點 (例如`https://localhost:5000`)。 如果您使用電腦視覺容器和表單的辨識器容器一起在相同主機上，它們無法同時啟動搭配預設通訊埠*5000*。  |  
|表單辨識器資源 |若要使用這些容器，您必須具備：<br><br>「表單辨識器」  Azure 資源，用來取得相關聯的計費金鑰和計費端點 URI。 這兩個值都位於 Azure 入口網站**形式辨識器概觀**並**形式辨識器概觀金鑰**頁面，以及這兩個值，才能啟動容器。<br><br>**{BILLING_KEY}** ：資源金鑰<br><br>**{BILLING_ENDPOINT_URI}** ： 端點的 URI 範例 `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成與提交[認知服務形式辨識器容器存取要求表單](https://aka.ms/FormRecognizerRequestAccess)要求存取至容器。 這樣也會將您註冊電腦視覺。 您不需要個別登入電腦視覺要求表單。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

最低和建議 CPU 核心和記憶體配置給每個表單辨識器容器下表所述：

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 核心，4 GB 記憶體 | 4 核心，8 GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。
* TPS - 每秒的交易數
* 核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

> [!Note]
> 最低和建議值會根據 Docker 限制並*不*主機機器的資源。

## <a name="get-the-container-image-with-the-docker-pull-command"></a>取得 docker pull 命令的容器映像

表單的辨識器的容器映像可在以下的存放庫：

| 容器 | 存放庫 |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

如果您想使用`cognitive-services-recognize-text` [容器](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)，而非表單辨識器服務，請務必使用 `docker pull` 命令搭配正確的容器名稱： 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>表單辨識器容器的 Docker Pull

#### <a name="form-recognizer"></a>表單辨識器

若要取得表單辨識器的容器，使用下列命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器是在後[主機電腦](#the-host-computer)，才能使用容器中使用下列程序。

1. 使用必要但未使用的計費設定[執行容器](#run-the-container-by-using-the-docker-run-command)。 `docker run` 命令有相關[範例](form-recognizer-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-by-using-the-docker-run-command"></a>使用 docker run 命令執行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 此命令會使用下列參數：

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 此金鑰可用來啟動容器。 在 Azure 入口網站上使用**形式辨識器金鑰**頁面。  |
|{BILLING_ENDPOINT_URI} | 計費的端點 URI 值是可在 Azure 入口網站上取得**形式辨識器概觀**頁面。|
|{COMPUTER_VISION_API_KEY}| 索引鍵是可在 Azure 入口網站上取得**電腦視覺 API 金鑰**頁面。|
|{COMPUTER_VISION_ENDPOINT_URI}|計費端點。 如果您使用雲端架構的電腦視覺資源，是可在 Azure 入口網站上取得的 URI 值**電腦視覺 API 概觀**頁面。 如果您使用`cognitive-services-recognize-text`容器中，使用計費會傳遞至容器中的端點 URL`docker run`命令。|

請以您自己的值取代下列範例 `docker run` 命令中的參數。

### <a name="form-recognizer"></a>表單辨識器

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

此命令：

* 執行容器映像形式辨識器的容器。
* 2 個 CPU 核心和 8 gb 的記憶體配置。
* 會公開 TCP 連接埠 5000，並將虛擬 TTY 配置容器。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。
* 掛接 /input 和容器 /output 磁碟區。

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>以個別的 docker 執行命令執行個別的容器

表單的辨識器和文字辨識器組合在本機裝載於相同的主機上，使用下列兩個範例 Docker CLI 命令：

在連接埠 5000 上執行第一個容器。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

在連接埠 5001 上執行第二個容器。


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
每個後續容器應該位於不同的連接埠。 

### <a name="run-separate-containers-with-docker-compose"></a>透過 Docker Compose 執行個別的容器

在本機裝載在相同主機的表單辨識器和文字辨識器組合，請參閱下列的範例 Docker Compose 的 YAML 檔案。 `formrecognizer` 和 `ocr` 容器的文字辨識器 `{COMPUTER_VISION_API_KEY}` 必須相同。 `{COMPUTER_VISION_ENDPOINT_URI}`只能用於`ocr`容器，因為`formrecognizer`容器使用`ocr`名稱和連接埠。 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> `Eula`， `Billing`，並`ApiKey`，以及`FormRecognizer:ComputerVisionApiKey`和`FormRecognizer:ComputerVisionEndpointUri`必須指定選項，來執行容器; 否則不會啟動容器。 如需詳細資訊，請參閱[帳單](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

|容器|端點|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>表單辨識器

容器提供 websocket 為基礎的查詢端點的 Api，您可以透過存取[形式辨識器服務 SDK 文件](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)。

根據預設，表單辨識器 SDK 會使用線上服務。 若要使用容器，您必須變更初始化方法。 請參閱以下範例。

#### <a name="for-c"></a>針對 C#

從使用此 Azure 雲端初始化呼叫變更：

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

此呼叫，它會使用容器端點：

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>針對 Python

從使用此 Azure 雲端初始化呼叫變更：

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

此呼叫，它會使用容器端點：

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>表單辨識器

容器提供 REST 端點 Api，您可以找到關於[Form 辨識器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)頁面。


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

當您執行的容器時，容器會使用**stdout**並**stderr**有助於疑難排解問題發生時啟動，或執行容器的輸出資訊。

## <a name="billing"></a>計費

形式辨識器容器，利用將帳單資訊傳送至 Azure_形式辨識器_上您的 Azure 帳戶的資源。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](form-recognizer-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行表單辨識器容器的概念和工作流程。 摘要說明：

* 表單辨識器會針對 Docker 提供一個 Linux 容器。
* 容器映像是從 Azure 中的私人容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以使用 REST API 或 REST SDK，藉由指定主應用程式的容器 URI 呼叫表單的辨識器的容器中的作業。
* 當您具現化的容器，您必須指定的計費資訊。

> [!IMPORTANT]
>  認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](form-recognizer-container-configuration.md)的組態設定。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)。
