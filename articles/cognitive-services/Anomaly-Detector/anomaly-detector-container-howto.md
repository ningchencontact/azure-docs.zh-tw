---
title: 安裝及執行容器-異常偵測器
titleSuffix: Azure Cognitive Services
description: 您可以使用異常偵測器 API 的進階的演算法來識別時間序列資料中的異常狀況。
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: 5dcec0d5f313b1c746c0674d0f9bf4d30ed19e5c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026788"
---
# <a name="install-and-run-anomaly-detector-containers"></a>安裝並執行異常偵測器的容器

異常偵測器都有下列的容器： 

|函式|特性|
|-|-|
|異常偵測器| <li> 即時發生，請偵測異常狀況。 <li> 會在您的資料集的異常偵測以批次。 <li> 推斷資料的預期一般的範圍。 <li> 支援的異常偵測敏感度調整使其更符合您的資料。 |

如需 Api 的詳細資訊，請參閱：
* [深入了解異常偵測 API 服務](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

使用異常偵測器容器之前，您必須符合下列必要條件：

|必要項|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|異常偵測器資源 |若要使用這些容器，您必須具備：<br><br>_異常偵測器_Azure 資源，以取得相關聯的計費金鑰和計費的端點 URI。 這兩個值可在 Azure 入口網站的異常偵測器的概觀和 [金鑰] 頁面上，才能啟動容器。<br><br>**{BILLING_KEY}**：資源金鑰<br><br>**{BILLING_ENDPOINT_URI}**：端點 URI 範例為：`https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>要求存取的容器登錄

您必須先完成與提交[異常偵測器容器要求表單](https://aka.ms/adcontainer)要求存取至容器。

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主機電腦

**主機**是執行 Docker 容器的電腦。 這可以您內部部署的電腦，或是在 Azure 中裝載服務的 Docker，這些服務包括：

* [Azure Kubernetes Service](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)
* [Azure 容器執行個體](https://docs.microsoft.com/container-instances/index.yml)
* 部署至 [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml) 的 [Kubernetes](https://kubernetes.io/) 叢集。 如需詳細資訊，請參閱[將 Kubernetes 部署至 Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md)。
<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明最低和建議 CPU 核心和記憶體配置的異常偵測器的容器。

| QPS （每秒的查詢）。 | 最小值 | 建議 |
|-----------|---------|-------------|
| 10 個 QPS | 4 核心，1 GB 記憶體 | 8 核心 2 GB 記憶體 |
| 20 QPS | 8 核心，2 GB 記憶體 | 16 核心 4 GB 記憶體 |

每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下載容器映像。

| 容器 | 存放庫 |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker 提取容器的異常偵測器

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](anomaly-detector-container-configuration.md#example-docker-run-commands)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 此命令會使用下列參數：

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | 此金鑰用來啟動容器時，並可在 Azure 入口網站的 [異常偵測器金鑰] 頁面上取得。  |
|{BILLING_ENDPOINT_URI} | 在 Azure 入口網站的異常偵測器概觀頁面上，您可以使用計費的端點 URI 值。|

請以您自己的值取代下列範例 `docker run` 命令中的參數。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 執行異常偵測器容器的容器映像
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

### <a name="running-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

如果打算使用所公开的端口运行多个容器，请确保使用不同的端口运行每个容器。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

将 `<container-registry>` 和 `<container-name>` 替换为你使用的容器的值。 它们并非必须是同一容器。 「 異常偵測器容器和 LUIS 容器主機上執行的同時，您可以有或您可以有多個執行的異常偵測器的容器。 

在端口 5000 上运行第一个容器。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

在端口 5001 上运行第二个容器。


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

每个后续容器都应当位于不同的端口上。 

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

針對容器 API 請使用主機 https://localhost:5000。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](anomaly-detector-container-configuration.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。 

## <a name="billing"></a>計費

異常偵測器容器傳送帳單寄送至 Azure 的資訊，請使用_異常偵測器_上您的 Azure 帳戶的資源。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](anomaly-detector-container-configuration.md)。

## <a name="summary"></a>總結

在本文中，您已了解概念和下載、 安裝及執行異常偵測器容器的工作流程。 摘要說明：

* 異常偵測器提供一個 Linux 容器 Docker、 封裝與批次串流，預期的範圍推斷 」 和 「 敏感度微調的異常偵測。
* 從私人 Azure 容器登錄庫專用容器 preview 下載容器映像。
* 容器映像是在 Docker 中執行。
* 您可以使用 REST API 或 SDK，藉由指定主應用程式的容器 URI 呼叫異常偵測器的容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會傳送客戶資料 （例如，時間序列資料分析） 給 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](anomaly-detector-container-configuration.md)以了解組態設定
* [深入了解異常偵測 API 服務](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
