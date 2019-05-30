---
title: 安裝及執行容器 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 了解如何使用表單辨識器容器來剖析表單和資料表資料。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/28/2019
ms.author: pafarley
ms.openlocfilehash: f65375bfd826660f8583068875a1fddc545a86d7
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306537"
---
# <a name="install-and-run-form-recognizer-containers"></a>安裝及執行表單辨識器容器
表單辨識器運用機器學習技術來識別和擷取表單中的索引鍵值組和資料表。 它會讓值和資料表項目關聯至表單，然後輸出在原始檔中包含關聯性的結構化資料。 您可以使用簡單的 REST API 呼叫您的自訂表單辨識器模型，以降低複雜度並輕鬆地在您的工作流程自動化程序或其他應用程式中整合它。 只需要五份文件 (或空的表單)，以便您快速且精確地取得針對特定內容量身打造的結果，而不需大量手動操作或廣泛的資料科學專業知識。 不需要資料標記或資料註解。

|函式|特性|
|-|-|
|表單辨識器| <li>處理 PDF、PNG 和 JPG 類型的檔案。<li>使用至少 5 份相同版面配置的表單來定型自訂模型。 <li>擷取索引鍵值組和資料表資訊。 <li>使用認知服務電腦視覺 API RecognizeText，偵測及擷取表單內部影像中的列印文字。<li>不需要註釋或標記。|

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

使用表單辨識器容器之前，您必須符合下列必要條件：

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。|
|Azure CLI| 您可能需要在主機上安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。|
|電腦視覺 API 資源| 若要處理經過掃描的文件和影像，則需要**電腦視覺資源**。 您可以將**辨識文字**功能當作 Azure 資源 (REST API 或 SDK) 或 `cognitive-services-recognize-text` [容器](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)存取。 適用一般計費費用。 <br><br>您必須針對特定的電腦視覺資源 (Azure 雲端或認知服務容器) 傳入金鑰和計費端點。 使用此金鑰和計費端點作為 {COMPUTER_VISION_API_KEY} 和 {COMPUTER_VISION_BILLING_ENDPOINT_URI}。<br><br> 如果您使用 **`cognitive-services-recognize-text` 容器**，請確定：<br><br>* 表單辨識器容器的電腦視覺金鑰是在電腦視覺 `docker run` 命令中針對 `cognitive-services-recognize-text` 容器指定的金鑰。<br>* 您的計費端點是容器的端點，例如 `https://localhost:5000`。 如果您在相同的主機上一起使用電腦視覺和表單辨識器容器，則兩者無法同時透過預設通訊埠 `5000` 啟動。  |  
|表單辨識器資源 |若要使用這些容器，您必須具備：<br><br>「表單辨識器」  Azure 資源，用來取得相關聯的計費金鑰和計費端點 URI。 這兩個值可在 Azure 入口網站的 [表單辨識器概觀]  和 [表單辨識器金鑰] 頁面上取得，需要這些值才能啟動容器。<br><br>**{BILLING_KEY}** ：資源金鑰<br><br>**{BILLING_ENDPOINT_URI}** ：端點 URI 範例為：`https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成並提交[認知服務表單辨識器容器要求表單](https://aka.ms/FormRecognizerRequestAccess)，才能要求存取容器。 這也會讓您註冊電腦視覺。 您不需要個別註冊電腦視覺要求表單。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明每個表單辨識器容器的最低和建議 CPU 核心與記憶體配置。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 核心，4 GB 記憶體 | 4 核心，8 GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。
* TPS - 每秒的交易數

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

> [!Note]
> 最低和建議值是以 Docker 限制 (而「非」  主機機器資源) 為基礎。

## <a name="get-the-container-image-with-docker-pull-command"></a>使用 docker pull 命令取得容器映像

可以使用表單辨識器的容器映像。

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

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用必要但未使用的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](form-recognizer-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 此命令會使用下列參數：

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 此金鑰用來啟動容器，您可以在 Azure 入口網站的 [表單辨識器金鑰] 頁面上取得。  |
|{BILLING_ENDPOINT_URI} | 在 Azure 入口網站的 [表單辨識器概觀] 頁面上可以取得計費端點 URI 值。|
|{COMPUTER_VISION_API_KEY}| 在 Azure 入口網站的 [電腦視覺 API 金鑰] 頁面上可取得此金鑰。|
|{COMPUTER_VISION_ENDPOINT_URI}|計費端點。 如果您使用雲端式電腦視覺資源，則可在 Azure 入口網站的 [電腦視覺 API 概觀] 頁面上取得 URI 值。 如果您使用 `cognitive-services-recognize-text` 容器，請在 `docker run` 命令中使用已傳遞至容器的計費端點 URL。|

請以您自己的值取代下列範例 `docker run` 命令中的參數。

### <a name="form-recognizer"></a>表單辨識器

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

此命令：

* 從容器映像執行表單辨識器容器
* 配置 2 個 CPU 核心和 8 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。
* 將 /input 和 /output 磁碟區裝載至容器

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>以個別的 docker 執行命令執行個別的容器

若為裝載於相同主機本機上的表單辨識器和文字辨識器組合，後面會接著兩個範例 Docker CLI 命令。

在連接埠 5000 上執行第一個容器。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
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

若為裝載於相同主機本機上的表單辨識器和文字辨識器組合，後面會接著一個範例 Docker Compose YAML 檔案。 `formrecognizer` 和 `ocr` 容器的文字辨識器 `{COMPUTER_VISION_API_KEY}` 必須相同。 `{COMPUTER_VISION_ENDPOINT_URI}` 只會使用於 `ocr` 容器，因為 `formrecognizer` 容器會使用 `ocr` 名稱和連接埠。 

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
> 必須指定 `Eula`、`Billing` 和 `ApiKey` 以及 `FormRecognizer:ComputerVisionApiKey` 和 `FormRecognizer:ComputerVisionEndpointUri` 選項才能執行容器，否則不會啟動容器。  如需詳細資訊，請參閱[帳單](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

|容器|端點|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>表單辨識器

容器會提供 Websocket 型查詢端點 API，其可透過[表單辨識器服務 SDK 文件](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)存取。

根據預設，表單辨識器 SDK 會使用線上服務。 若要使用容器，您必須變更初始化方法。 請參閱以下範例。

#### <a name="for-c"></a>針對 C#

從使用此 Azure 雲端初始化呼叫變更：

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

為使用容器端點的這項呼叫：

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>針對 Python

從使用此 Azure 雲端初始化呼叫變更

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

為使用容器端點的這項呼叫：

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>表單辨識器

容器會提供 REST 端點 API，您可在[這裡](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)找到。


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

當您執行容器時，容器會使用 **stdout** 和 **stderr** 來輸出資訊，該資訊有助於排解在啟動或執行容器時所發生的問題。

## <a name="billing"></a>計費

表單辨識器容器會使用您 Azure 帳戶上的「表單辨識器」  資源，將計費資訊傳送至 Azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](form-recognizer-container-configuration.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行表單辨識器容器的概念和工作流程。 摘要說明：

* 表單辨識器會針對 Docker 提供一個 Linux 容器。
* 容器映像是從 Azure 中的私人容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以指定容器的主機 URI，以使用 REST API 或 SDK 呼叫表單辨識器容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
>  認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](form-recognizer-container-configuration.md)以了解組態設定
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
