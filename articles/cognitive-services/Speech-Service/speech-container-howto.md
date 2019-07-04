---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 安裝並執行語音容器。 語音轉文字會即時地將音訊串流轉譯成文字，以便您的應用程式、工具或裝置使用或顯示。 文字轉語音會將輸入文字轉換為仿真人的合成語音。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 8f395788d4dd3c845155a52bd6b4666998838fcd
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490225"
---
# <a name="install-and-run-speech-service-containers"></a>安裝並執行語音服務的容器

語音容器可讓客戶能夠建置一個已最佳化，可善用強大的雲端功能及邊緣位置的語音應用程式架構。 

兩個語音容器**語音轉換文字**並**文字轉換語音**。 

|函式|功能|最新|
|-|-|--|
|語音轉文字| <li>Transcribes 連續即時語音或批次音訊錄製成中繼結果的文字。|1.1.3|
|文字轉語音| <li>將文字轉換成自然發音語音。 輸入純文字或語音合成標記語言 (SSML)。 |1.1.0|

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

使用語音容器之前，您必須符合下列必要條件：

|必要項|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|語音資源 |若要使用這些容器，您必須具備：<br><br>A_語音_Azure 資源，以取得相關聯的計費金鑰和計費的端點 URI。 這兩個值都位於 Azure 入口網站**語音**概觀] 和 [索引鍵頁面且需要啟動容器。<br><br>**{BILLING_KEY}** ：資源金鑰<br><br>**{BILLING_ENDPOINT_URI}** ：端點 URI 範例為：`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成與提交[認知服務語音容器要求表單](https://aka.ms/speechcontainerspreview/)要求存取至容器。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>進階的向量擴充功能支援

**主機**是執行 Docker 容器的電腦。 主機必須支援[Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2)。 您可以檢查這項支援在 Linux 主機使用下列命令： 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明最低和建議 CPU 核心和記憶體配置給每個語音容器。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 核心<br>2 GB 記憶體  | 4 核心<br>4 GB 記憶體  |
|cognitive-services-text-to-speech | 1 個核心，0.5 GB 記憶體| 2 核心、 1 GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

**請注意**;最低和建議，根據 Docker 限制*不*主機機器的資源。 例如，語音轉換文字容器記憶體對應部分大型的語言模型，而且它是_建議_整個檔案，以符合在記憶體中，這是額外的 4-6 GB。 此外，任一容器第一次執行可能會更久，因為模型需到記憶體分頁處理。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

使用語音的容器映像。

| 容器 | 存放庫 |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>語言地區設定是在容器標記

`latest`標記提取`en-us`地區設定和`jessarus`語音。

#### <a name="speech-to-text-locales"></a>語音轉換文字的地區設定

所有標記，除了`latest`都是以下列格式，其中`<culture>`指出的地區設定容器：

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

下列標記是格式的範例：

```
1.1.3-amd64-en-us-preview
```

下表列出支援的地區設定，如**語音轉換文字**1.1.3 在容器的版本：

|語言地區設定|標記|
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

所有標記，除了`latest`都是以下列格式，其中`<culture>`表示地區設定和`<voice>`指出容器的語音：

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

下列標記是格式的範例：

```
1.1.0-amd64-en-us-jessarus-preview
```

下表列出支援的地區設定，如**文字轉換語音**1.1.0 中容器的版本：

|語言地區設定|標記|支援的語音|
|--|--|--|
|中文|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao apollo|
|英文 |`en-au`|catherine<br>hayleyrus|
|英文 |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|英文 |`en-in`|heera apollo<br>priyarus<br>ravi-apollo<br>|
|英文 |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|法文|`fr-ca`|caroline<br>harmonierus|
|法文|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|德文|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|義大利文|`it-it`|cosimo-apollo<br>luciarus|
|日文|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro apollo|
|韓文|`ko-kr`|heamirus|
|葡萄牙文|`pt-br`|daniel-apollo<br>heloisarus|
|西班牙文|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|西班牙文|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>適用於語音容器的 docker 提取

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

1. 使用必要但未使用的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](speech-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 此命令會使用下列參數：

**在預覽期間**，計費的設定必須是有效啟動容器，但您不需支付使用方式。

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 此金鑰用來啟動容器時，並可在 Azure 入口網站的 [語音金鑰] 頁面上取得。  |
|{BILLING_ENDPOINT_URI} | 使用 Azure 入口網站的 [語音概觀] 頁面上的帳單寄送的端點 URI 值。|

請以您自己的值取代下列範例 `docker run` 命令中的參數。

### <a name="text-to-speech"></a>文字轉換語音

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

### <a name="speech-to-text"></a>語音轉文字

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 從容器映像執行語音容器
* 2 個 CPU 核心和 2 gb 的記憶體配置
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

容器提供 websocket 為基礎的查詢端點的 Api，可透過存取[語音 SDK](index.yml)。

根據預設，語音 SDK 會使用線上語音服務。 若要使用容器，您必須變更初始化方法。 請參閱以下範例。

#### <a name="for-c"></a>針對 C#

從使用此 Azure 雲端初始化呼叫變更：

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

為使用容器端點的這項呼叫：

```C#
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

容器提供 REST 端點 Api，可以找到[此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)和您可以找到範例[這裡](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

當您執行容器時，容器會使用 **stdout** 和 **stderr** 來輸出資訊，該資訊有助於排解在啟動或執行容器時所發生的問題。

## <a name="billing"></a>計費

帳單寄送至 Azure 的資訊，請使用 語音容器傳送_語音_上您的 Azure 帳戶的資源。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](speech-container-configuration.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已了解概念和下載、 安裝及執行語音容器的工作流程。 摘要說明：

* 語音提供兩個 Linux 容器的 Docker，將語音轉換文字和文字轉換語音的封裝。
* 容器映像是從 Azure 中的私人容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以使用 REST API 或 SDK，藉由指定主應用程式的容器 URI 呼叫語音容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
>  認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](speech-container-configuration.md)以了解組態設定
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
