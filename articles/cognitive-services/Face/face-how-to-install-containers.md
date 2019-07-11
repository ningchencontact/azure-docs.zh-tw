---
title: 安裝及執行容器
titlesuffix: Face - Azure Cognitive Services
description: 下載、 安裝及執行容器的臉部在本逐步解說教學課程。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 84960e82e25f4b6cc59324f17ce46de7f9f7ac23
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704672"
---
# <a name="install-and-run-face-containers"></a>安裝並執行臉部的容器

Azure 認知服務臉部偵測人臉映像中的 Docker 提供標準化的 Linux 容器。 它也會識別屬性，其中包括臉部特徵，例如 noses 和眼睛、 性別、 年齡和其他機器預測的臉部特徵。 除了偵測，臉部可以檢查是否在同一個映像或不同的映像中的這兩張臉就會將相同皆使用信心分數。 臉部也可以比較臉部，對資料庫看起來類似或相同的臉部是否已經存在。 它也可以組織相似臉部分組，藉由使用共用的視覺特性。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>先決條件

您必須符合下列必要條件，才能使用人臉識別 API 容器。

|必要|用途|
|--|--|
|Docker 引擎| 必須安裝 Docker 引擎[主機電腦](#the-host-computer)。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> 在 Windows、 Docker 也必須設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您需要 Docker 的概念，例如登錄、 存放庫、 容器和容器映像的基本知識。 您也需要的基本知識`docker`命令。| 
|Azure`Cognitive Services`資源 |若要使用容器，您必須具備：<br><br>在 Azure 認知服務資源和相關聯的計費金鑰和計費的端點 URI。 這兩個值都位於**概觀**並**金鑰**資源的頁面。 需要他們以啟動容器。 新增`face/v1.0`BILLING_ENDPOINT_URI 下例所示，路由傳送至端點 URI: <br><br>**{BILLING_KEY}** ：資源金鑰<br><br>**{BILLING_ENDPOINT_URI}** ： 端點的 URI 範例 `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明每個臉部 API 容器的最低和建議的 CPU 核心與記憶體配置。

| 容器 | 最小值 | 建議 | 每秒交易<br>（最小值、 最大值）|
|-----------|---------|-------------|--|
|臉部 | 1 個核心，2 GB 記憶體 | 1 個核心，4 GB 記憶體 |10, 20|

* 每個核心必須至少 2.6 GHz 或更快。
* 每秒交易數 (TPS)。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>取得與 docker 提取容器映像

使用臉部 API 的容器映像。 

| 容器 | 存放庫 |
|-----------|------------|
| 臉部 | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>臉部容器的 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>使用容器

容器是在後[主機電腦](#the-host-computer)，才能使用容器中使用下列程序。

1. [執行容器](#run-the-container-with-docker-run)與所需計費的設定。 `docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>執行執行的 docker 容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 此命令會使用下列參數。

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 此金鑰用來啟動容器，並且可在 Azure 上`Cognitive Services`**金鑰**頁面。 |
|{BILLING_ENDPOINT_URI} | 計費的端點 URI 值是可在 Azure 上取得`Cognitive Services`**概觀**頁面。 例如 `https://westus.api.cognitive.microsoft.com/face/v1.0`。|

新增`face/v1.0`BILLING_ENDPOINT_URI 前例中所示，路由傳送至端點 URI。 

這些參數取代為您自己的值，在下列`docker run`命令範例：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 從容器映像會執行臉部容器。
* 會配置一個 CPU 核心和 4 GB 的記憶體。
* 會公開 TCP 連接埠 5000，並將虛擬 TTY 配置容器。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

`docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> `Eula`， `Billing`，和`ApiKey`必須指定選項來執行容器或容器將無法啟動。 如需詳細資訊，請參閱[帳單](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

針對容器 API 請使用主機 `https://localhost:5000`。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您執行容器的輸出[掛接](./face-resource-container-config.md#mount-settings)並啟用記錄時，容器會產生很有幫助，當您啟動或執行容器時所發生的問題進行疑難排解的記錄檔。 


## <a name="billing"></a>計費

人臉識別 API 容器將您的 Azure 帳戶使用人臉識別 API 資源傳送至 Azure 的帳單資訊。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已了解概念及如何下載、 安裝及執行人臉識別 API 容器的工作流程。 摘要說明：

* 人臉識別 API 會提供三個 Linux 容器 docker 提供關鍵片語擷取、 語言偵測和情感分析。
* 從 Azure Container Registry 下載容器映像。
* 容器映像是在 Docker 中執行。
* 您可以使用 REST API 或 SDK 來呼叫藉由指定主應用程式的容器 URI 的人臉識別 API 容器中的作業。
* 當您具現化的容器，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務的容器未獲授權執行而不必連接至 Azure 中的計量。 客戶必須啟用要通訊隨時都與計量的服務的帳單資訊的容器。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 如需組態設定，請參閱[設定容器](face-resource-container-config.md)。
* 若要深入了解如何偵測及識別人臉，請參閱[臉部概觀](Overview.md)。
* 如需容器所支援的方法資訊，請參閱[人臉識別 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 若要使用更多的認知服務容器，請參閱[認知服務容器](../cognitive-services-container-support.md)。
