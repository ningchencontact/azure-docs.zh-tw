---
title: 安裝和執行容器-文字分析
titleSuffix: Azure Cognitive Services
description: 本逐步解說教學課程的內容包含如何下載、安裝及執行適用於文字分析的容器。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dapine
ms.openlocfilehash: ddbe586c03d9f722d844d06968aa25e4b4a5aac0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815305"
---
# <a name="install-and-run-text-analytics-containers"></a>安裝並執行文字分析容器

容器可讓您在自己的環境中執行文字分析 Api, 而且非常適合您的特定安全性和資料控管需求。 文字分析容器提供原始文字的先進自然語言處理, 並包含三個主要功能: 情感分析、關鍵字組解壓縮和語言偵測。 容器目前不支援實體連結。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

若要執行任何文字分析的容器, 您必須擁有主機電腦和容器環境。

## <a name="preparation"></a>準備工作

使用文字分析容器之前，您必須符合下列必要條件：

|必要項|用途|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|文字分析資源 |若要使用此容器，您必須具備：<br><br>Azure[文字分析資源](../../cognitive-services-apis-create-account.md), 用來取得相關聯的 API 金鑰和端點 URI。 這兩個值可在 Azure 入口網站的 [文字分析概觀和金鑰] 頁面上取得，需要這些值才能啟動容器。<br><br>**{API_KEY}** :[**金鑰**] 頁面上有兩個可用的資源金鑰之一<br><br>**{ENDPOINT_URI}** :[**總覽**] 頁面上所提供的端點|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明針對每個文字分析容器配置的最低和建議 CPU 核心 (至少 2.6 GHz 或更快的版本) 與記憶體 (以 GB 為單位)。

| 容器 | 最小值 | 建議 | TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
|關鍵片語擷取 | 1核心, 2 GB 記憶體 | 1核心, 4 GB 記憶體 |15、30|
|語言偵測 | 1核心, 2 GB 記憶體 | 1核心, 4 GB 記憶體 |15、30|
|情感分析 | 1核心, 2 GB 記憶體 | 1核心, 4 GB 記憶體 |15、30|

* 每個核心必須至少 2.6 GHz 或更快。
* TPS - 每秒的交易數

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

文字分析的容器映像可從 Microsoft Container Registry 取得。

| 容器 | 存放庫 |
|-----------|------------|
|關鍵片語擷取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|語言偵測 | `mcr.microsoft.com/azure-cognitive-services/language` |
|情感分析| `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)使用命令從 Microsoft container Registry 下載容器映射。

如需文字分析提供之標籤的完整說明，請參閱下列 Docker Hub 上的容器：

* [關鍵片語擷取](https://go.microsoft.com/fwlink/?linkid=2018757)
* [語言偵測](https://go.microsoft.com/fwlink/?linkid=2018759)
* [情感分析](https://go.microsoft.com/fwlink/?linkid=2018654)

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下載容器映像。

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>關鍵片語擷取容器的 docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>語言偵測容器的 docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>情感容器的 docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](../text-analytics-resource-container-config.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 如需如何取得`{Endpoint_URI}`和`{API_Key}`值的詳細資訊, 請參閱[收集必要的參數](#gathering-required-parameters)。

命令的[範例](../text-analytics-resource-container-config.md#example-docker-run-commands)可供使用。`docker run`

### <a name="run-container-example-of-docker-run-command"></a>Docker run 命令的執行容器範例

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映像執行關鍵片語容器
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。


> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。

針對容器 API 請使用主機 `https://localhost:5000`。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](../text-analytics-resource-container-config.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

## <a name="billing"></a>帳務

文字分析容器會使用您 Azure 帳戶上的_文字分析_資源傳送計費資訊至 Azure。 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](../text-analytics-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行文字分析容器的概念和工作流程。 摘要說明：

* 文字分析提供三個適用於 Docker 的 Linux 容器，封裝了關鍵片語擷取、語言偵測和情感分析。
* 容器映像可從 Azure 中的 Microsoft Container Registry (MCR) 下載取得。
* 容器映像是在 Docker 中執行。
* 您可以指定容器的主機 URI，來使用 REST API 或 SDK 呼叫文字分析容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](../text-analytics-resource-container-config.md)以了解組態設定
* 參閱[常見問題集 (FAQ)](../text-analytics-resource-faq.md) 來解決功能相關問題。
