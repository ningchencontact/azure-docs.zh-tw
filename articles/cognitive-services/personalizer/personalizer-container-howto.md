---
title: 安裝及執行容器 - 個人化工具
titleSuffix: Azure Cognitive Services
description: 如何下載、安裝及執行個人化工具的容器。
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/09/2019
ms.author: edjez
ms.openlocfilehash: a197531a7c78823271c0a5fa5413b76746f63a9a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507021"
---
# <a name="install-and-run-personalizer-containers"></a>安裝和執行個人化工具容器

個人化工具服務有下列容器： 

|函式|特性|
|-|-|
|個人化工具|從內容和使用者的目前環境判斷最佳動作。|

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

使用個人化工具服務容器之前，您必須符合下列必要條件：

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|個人化工具服務資源 |若要使用這些容器，您必須具備：<br><br>「個人化工具服務」 Azure 資源，用來取得相關聯的計費金鑰和計費端點 URI。 這兩個值可在 Azure 入口網站的 [個人化工具服務概觀] 和 [個人化工具服務金鑰] 頁面上取得，需要這些值才能啟動容器。<br><br>**{BILLING_KEY}**：資源金鑰<br><br>**{BILLING_ENDPOINT_URI}**：端點 URI 範例為：`https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>主機電腦

**主機**是執行 Docker 容器的電腦。 這可以您內部部署的電腦，或是在 Azure 中裝載服務的 Docker，這些服務包括：

* [Azure Kubernetes Service](https://docs.microsoft.com/aks/index.yml)
* [Azure 容器執行個體](https://docs.microsoft.com/container-instances/index.yml)
* 部署至 [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml) 的 [Kubernetes](https://kubernetes.io/) 叢集。 如需詳細資訊，請參閱[將 Kubernetes 部署至 Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md)。

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明每個個人化工具服務容器的最低和建議的 CPU 核心與記憶體配置。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
|個人化工具 | 1 核心，4 GB 記憶體 | 2 核心、8 GB 記憶體 |

每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

容器必須要能夠連線到 Azure EventHub，才能將排名和回報呼叫中的資訊轉送到 Azure 中的個人化工具伺服器，而且必須能夠連線到個人化工具 API，才能載入所需的組態和最新機器學習模型。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

個人化工具服務有容器映像可供使用。 

| 容器 | 存放庫 |
|-----------|------------|
| 個人化工具 | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> 您可以使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) \(英文\) 命令來列出已下載的容器映像。 例如，下列命令會列出每個已下載之容器映像的識別碼、存放庫和標籤，並將它格式化為表格：
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>個人化工具服務容器的 docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>容器的運作方式

個人化工具容器代表個人化工具服務的用戶端部分。 容器會在執行時，從 Azure 雲端中的個人化工具服務取得模型和組態設定。 容器服務會使用此資訊來回應**排名**和**回報**要求。 容器也會將這些要求傳送至 Azure 雲端中的個人化工具資源。 此資訊接著會根據模型更新頻率的目前設定，用於定型個人化工具迴圈模型。 已更新的模型會送回容器。 

![在內部部署環境，個人化工具容器用戶端的本機架構](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](personalizer-container-configuration.md#example-docker-run-commands)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 此命令會使用下列參數：

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 此金鑰可用來啟動容器，您可以在 Azure 入口網站的 [個人化工具服務金鑰] 頁面上取得。  |
|{BILLING_ENDPOINT_URI} | 計費端點 URI 值可在 Azure 入口網站的 [個人化工具服務概觀] 頁面上取得。|

請以您自己的值取代下列範例 `docker run` 命令中的參數。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 從容器映像執行個人化工具服務容器
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

### <a name="run-multiple-containers-on-the-same-host"></a>在相同主機上執行多個容器

如果您打算使用公開的連接埠執行多個容器，請務必使用不同的連接埠執行每個容器。 例如，在連接埠 5000 上執行第一個容器，以及在連接埠 5001 上執行第二個容器。

以您使用的容器值，取代 `<container-registry>` 和 `<container-name>`。 這些容器值不必是相同的容器。 您可以讓個人化工具容器和 LUIS 容器在主機上一起執行，也可以執行多個個人化工具容器。 

在連接埠 5000 上執行第一個容器。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

在連接埠 5001 上執行第二個容器。


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

每個後續容器應該位於不同的連接埠。 

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

針對容器 API 請使用主機 https://localhost:5000。

## <a name="stop-the-container"></a>停止容器

若要關閉容器，請在正在執行容器的命令列環境中按 **Ctrl+C**。

## <a name="troubleshoot"></a>疑難排解

如果您在啟用輸出[掛接](personalizer-container-configuration.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。 

## <a name="container-api-documentation"></a>容器的 API 文件

容器提供了一組完整的端點文件集以及 `Try it now` 功能。 這項功能可讓您將自己的設定輸入以 Web 為基礎的 HTML 表單中，並直接進行查詢而無須撰寫任何程式碼。 在查詢傳回時，將會提供範例 CURL 命令，以示範所需的 HTTP 標頭和本文格式。 

> [!TIP]
> 請參閱 [OpenAPI 規格](https://swagger.io/docs/specification/about/)，其中說明 `/swagger` 相對 URI 中的容器所支援的 API 作業。 例如︰
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>計費

個人化工具服務容器會使用您 Azure 帳戶上的「個人化工具服務」資源，將計費資訊傳送至 Azure。 

認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。  

`docker run` 命令會使用下列引數進行計費：

| 選項 | 說明 |
|--------|-------------|
| `ApiKey` | 「個人化工具服務」資源的 API 金鑰，可用來追蹤帳單資訊。 |
| `Billing` | 「個人化工具服務」資源的端點，可用來追蹤帳單資訊。|
| `Eula` | 表示您已接受容器的授權。<br/>此選項的值必須設定為 `accept`。 |

> [!IMPORTANT]
> 這三個選項都必須指定為有效的值，否則將無法啟動容器。

如需這些選項的詳細資訊，請參閱[設定容器](personalizer-container-configuration.md)。

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行個人化工具服務容器的概念和工作流程。 摘要說明：

* 個人化工具服務提供 Docker 適用的 Linux 容器，以封裝個人化內容。
* 容器映像可從 Azure 中的 Microsoft Container Registry (MCR) 下載取得。
* 容器映像是在 Docker 中執行。
* 指定容器的主機 URI，即可使用 REST API 或 SDK 呼叫個人化工具服務容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 個人化工具容器也會將要求資料傳送到 Azure 中的對應的服務，以便進行線上訓練，並且會定期從 Azure 取得已更新的模型。

## <a name="next-steps"></a>後續步驟

[如何設定 `Docker Run` 命令的 Docker 容器](personalizer-container-configuration.md)