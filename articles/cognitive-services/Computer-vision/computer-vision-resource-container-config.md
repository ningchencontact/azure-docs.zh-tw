---
title: 設定容器 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 在電腦視覺中設定辨識文字容器的各種設定。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 1df8199abbbc195db873ab3da515cb1dd5fe9761
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484074"
---
# <a name="configure-computer-vision-docker-containers"></a>設定電腦視覺 Docker 容器

您可以使用 `docker run` 命令引數來設定電腦視覺容器的執行時間環境。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 及 [`Eula`](#eula-setting) 設定，因此您必須同時為這三個設定提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](computer-vision-how-to-install-containers.md)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure `Cognitive Services` 資源金鑰。 您必須指定 ApiKey 的值，且值必須是為[`Billing`](#billing-configuration-setting)設定所指定之_認知服務_資源的有效金鑰。

此設定可在下列位置找到：

* Azure 入口網站：**認知服務**的資源管理，位於 [**金鑰**] 下方

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing` 設定會指定 Azure 上用來計量容器帳單資訊的_認知服務_資源端點 URI。 您必須指定此設定的值，且該值必須是 Azure 上_認知服務_資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 入口網站：**認知服務**總覽，加上標籤 `Endpoint`

請務必將 `vision/v1.0` 路由新增至端點 URI，如下表所示。 

|必要| 名稱 | 資料類型 | 說明 |
|--|------|-----------|-------------|
|是| `Billing` | 字串 | 計費端點 URI<br><br>範例：<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy 認證設定

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 `--mount`docker run[ 命令中指定 ](https://docs.docker.com/engine/reference/commandline/run/) 選項，以指定輸入裝載或輸出裝載。

電腦視覺容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](computer-vision-how-to-install-containers.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選用| 名稱 | 資料類型 | 說明 |
|-------|------|-----------|-------------|
|不允許| `Input` | 字串 | 電腦視覺容器不會使用此項目。|
|選用| `Output` | 字串 | 輸出裝載的目標。 預設值為 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](computer-vision-how-to-install-containers.md#stop-the-container)。

* **行接續字元**：下列各節中的 Docker 命令會使用反斜線（`\`）做為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **引數順序**：除非您非常熟悉 Docker 容器，否則請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| Placeholder | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | [Azure `Computer Vision` 金鑰] 頁面上 `Computer Vision` 資源的端點金鑰。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | [計費端點] 值可在 Azure `Computer Vision` [總覽] 頁面取得。| 如需明確的範例，請參閱[收集必要的參數](computer-vision-how-to-install-containers.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](computer-vision-how-to-install-containers.md#billing)。
> ApiKey 值是 [Azure `Cognitive Services` 資源金鑰] 頁面中的**金鑰**。

## <a name="container-docker-examples"></a>容器 Docker 範例

下列是讀取容器的 Docker 範例。

### <a name="basic-example"></a>基本範例

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>記錄範例 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>後續步驟

* 請參閱[如何安裝及執行容器](computer-vision-how-to-install-containers.md)。
