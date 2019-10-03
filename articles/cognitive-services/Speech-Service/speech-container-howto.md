---
title: 安裝語音容器-語音服務
titleSuffix: Azure Cognitive Services
description: 安裝並執行語音容器。 語音轉文字會即時地將音訊串流轉譯成文字，以便您的應用程式、工具或裝置使用或顯示。 文字轉語音會將輸入文字轉換為仿真人的合成語音。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: dapine
ms.openlocfilehash: dbd4f2cd4691f9ae6d8e37f38d2b600ec4897e45
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326706"
---
# <a name="install-and-run-speech-service-containers"></a>安裝和執行語音服務容器

語音容器可讓客戶建立一個已優化的語音應用程式架構，以充分利用強大的雲端功能和邊緣位置。 

這兩個語音容器是**語音轉換文字**和**文字轉換語音**。 

|函數|功能|最新|
|-|-|--|
|語音轉文字| <li>使用中繼結果，將連續即時語音或批次音訊錄音可將成文字。|1.2.0|
|文字轉語音| <li>將文字轉換成自然發音語音。 使用純文字輸入或語音合成標記語言（SSML）。 |1.2.0|

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

使用語音容器之前，您必須符合下列必要條件：

|必要項|用途|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|語音資源 |若要使用這些容器，您必須具備：<br><br>Azure_語音_資源，用來取得相關聯的 API 金鑰和端點 URI。 這兩個值都可在 Azure 入口網站的 [**語音**總覽] 和 [金鑰] 頁面上取得。 這兩者都是啟動容器的必要項。<br><br>**{API_KEY}** ：[**金鑰**] 頁面上有兩個可用的資源金鑰之一<br><br>**{ENDPOINT_URI}** ：[**總覽**] 頁面上所提供的端點|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成並提交[認知服務的語音容器要求表單](https://aka.ms/speechcontainerspreview/)，以要求容器的存取權。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension 支援

**主機**是執行 Docker 容器的電腦。 主機必須支援「[先進向量延伸](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)」（AVX2）。 您可以使用下列命令，在 Linux 主機上檢查這項支援： 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明每個語音容器的最低和建議 CPU 核心和記憶體配置。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2核心<br>2 GB 記憶體  | 4 核心<br>4 GB 記憶體  |
|cognitive-services-text-to-speech | 1核心，0.5-GB 記憶體| 2核心，1 GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

**注意**;最低和建議是根據 Docker 限制，而*不*是主機電腦資源。 例如，適用于大型語言模型的語音轉換文字容器記憶體對應部分，_建議您_將整個檔案納入記憶體中，這是額外的 4-6 GB。 此外，第一次執行任一容器可能需要較長的時間，因為模型會分頁到記憶體中。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

可使用適用于語音的容器映射。

| 容器 | 存放庫 |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>語言地區設定位於容器標記中

標記會提取地區設定和`jessarus`語音。 `en-us` `latest`

#### <a name="speech-to-text-locales"></a>語音轉換文字地區設定

除了以外`latest`的所有標記都會採用下列格式， `<culture>`其中會指出地區設定容器：

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

下列標記是格式的範例：

```
1.2.0-amd64-en-us-preview
```

下表列出容器的1.2.0 版本中，**語音轉換文字**支援的地區設定：

|語言地區設定|Tags|
|--|--|
|中文|`zh-cn`|
|英文 |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|法文 |`fr-ca`<br>`fr-fr`|
|德文|`de-de`|
|義大利文|`it-it`|
|日文|`ja-jp`|
|韓文|`ko-kr`|
|葡萄牙文|`pt-br`|
|西班牙文|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>文字轉換語音的地區設定

除了以外`latest`的所有標記都採用下列格式， `<culture>`其中表示地區設定，而則`<voice>`表示容器的語音：

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

下列標記是格式的範例：

```
1.2.0-amd64-en-us-jessarus-preview
```

下表列出容器的1.2.0 版本中，**文字轉換語音**支援的地區設定：

|語言地區設定|Tags|支援的語音|
|--|--|--|
|中文|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|英文 |`en-au`|catherine<br>hayleyrus|
|英文 |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|英文 |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|英文 |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|法文|`fr-ca`|caroline<br>harmonierus|
|法文|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|德文|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|義大利文|`it-it`|cosimo-apollo<br>luciarus|
|日文|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|韓文|`ko-kr`|heamirus|
|葡萄牙文|`pt-br`|daniel-apollo<br>heloisarus|
|西班牙文|`es-es`|elenarus<br>劉娜-apollo<br>pablo-apollo<br>|
|西班牙文|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>適用于語音容器的 Docker pull

#### <a name="speech-to-text"></a>語音轉文字

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>文字轉換語音

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](speech-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 如需如何取得`{ENDPOINT_URI}`和`{API_KEY}`值的詳細資訊，請參閱[收集必要的參數](#gathering-required-parameters)。

命令的[範例](speech-container-configuration.md#example-docker-run-commands)可供使用。`docker run`

> [!NOTE]
> 在**預覽期間**，計費設定必須是有效的，才能啟動容器，但您不需支付使用量的費用。

### <a name="text-to-speech"></a>文字轉換語音

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>語音轉文字

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行語音容器
* 配置2個 CPU 核心和 2 gb 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

|容器|端點|
|--|--|
|語音轉文字|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|文字轉換語音|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>語音轉文字

容器提供以 websocket 為基礎的查詢端點 Api，可透過[語音 SDK](index.yml)存取。

根據預設，語音 SDK 會使用線上語音服務。 若要使用容器，您必須變更初始化方法。 請參閱以下範例。

#### <a name="for-c"></a>針對 C#

從使用此 Azure 雲端初始化呼叫變更：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

為使用容器端點的這項呼叫：

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>針對 Python

從使用此 Azure 雲端初始化呼叫變更

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

為使用容器端點的這項呼叫：

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>文字轉換語音

容器提供 REST 端點 Api，您可以在[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)找到，也可以在[這裡](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)找到範例。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](speech-container-configuration.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>帳務

語音容器會使用您 Azure 帳戶上的_語音_資源，將帳單資訊傳送至 azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](speech-container-configuration.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已瞭解下載、安裝及執行語音容器的概念和工作流程。 摘要說明：

* 語音提供兩個適用于 Docker 的 Linux 容器，封裝語音轉換文字和文字轉換語音。
* 容器映像是從 Azure 中的私人容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以藉由指定容器的主機 URI，使用 REST API 或 SDK 來呼叫語音容器中的作業。
* 當具現化容器時，您必須提供帳單資訊。

> [!IMPORTANT]
>  認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](speech-container-configuration.md)以了解組態設定
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
