---
title: 安裝、執行容器
titlesuffix: Face - Azure Cognitive Services
description: 本逐步解說教學課程的內容包含如何下載、安裝及執行適用於臉部的容器。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: 3e6b220e7193c5e683fc8a6c06a6e9e3dd3e3f6e
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521613"
---
# <a name="install-and-run-face-containers"></a>安裝並執行臉部的容器

臉部會針對 Docker 提供名為「臉部」的標準化 Linux 容器，其能偵測影像中的人臉並識別其特性，包括臉部特徵點 (例如鼻子和眼睛)、性別、年齡及其他機器預測的臉部容貌。 除了偵測以外，臉部也可以使用信賴分數檢查相同或不同影像中的兩張臉是否相同，或將臉部向資料庫進行比對，看看是否有樣貌相似或相同的臉部。 它也能夠使用共同視覺特徵，將相似臉部分組。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

您必須符合下列必要條件，才能使用臉部 API 容器：

|必要項|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|臉部 API 資源 |若要使用此容器，您必須具備：<br><br>_臉部 API_ Azure 資源，用來取得相關聯的計費金鑰和計費端點 URI。 這兩個值可在 Azure 入口網站的 [臉部 API 概觀] 和 [金鑰] 頁面上取得，需要這些值才能啟動容器。<br><br>**{BILLING_KEY}**：資源金鑰<br><br>**{BILLING_ENDPOINT_URI}**：端點 URI 範例為：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明每個臉部 API 容器的最低和建議的 CPU 核心與記憶體配置。

| 容器 | 最小值 | 建議 | TPS<br>（最小值、 最大值）|
|-----------|---------|-------------|--|
|臉部 | 1 核心，2 GB 記憶體 | 1 核心，4 GB 記憶體 |10, 20|

* 每個核心必須至少 2.6 GHz 或更快。
* TP-每秒交易數

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

臉部 API 有容器映像可供使用。 

| 容器 | 存放庫 |
|-----------|------------|
| 臉部 | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>臉部容器的 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 此命令會使用下列參數：

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 此金鑰可用來啟動容器，您可以在 Azure 入口網站的 [臉部 API 金鑰] 頁面上取得。  |
|{BILLING_ENDPOINT_URI} | 計費端點 URI 值可在 Azure 入口網站的 [臉部 API 概觀] 頁面上取得。|

請以您自己的值取代下列範例 `docker run` 命令中的參數。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 從容器映像執行臉部容器
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

`docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

針對容器 API 請使用主機 `https://localhost:5000`。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](./face-resource-container-config.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。 


## <a name="billing"></a>計費

臉部 API 容器會使用您 Azure 帳戶上的_臉部 API_ 資源傳送計費資訊至 Azure。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./face-resource-container-config.md)。

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行臉部 API 容器的概念和工作流程。 摘要說明：

* 臉部 API 提供三個適用於 Docker 的 Linux 容器，其中包含關鍵片語擷取、語言偵測和情感分析。
* 容器映像可從 Azure 中的 Microsoft Container Registry (MCR) 下載取得。
* 容器映像是在 Docker 中執行。
* 您可以指定容器的主機 URI，以使用 REST API 或 SDK 呼叫臉部 API 容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](face-resource-container-config.md)以了解組態設定
* 檢閱[臉部概觀](Overview.md)以深入了解偵測與識別臉部  
* 參閱[臉部 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) \(英文\) 以取得容器所支援之方法的詳細資訊。
* 參閱[常見問題集 (FAQ)](FAQ.md) 來解決與臉部功能相關的問題。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
