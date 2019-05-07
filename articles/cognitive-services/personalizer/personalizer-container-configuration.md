---
title: 個人化工具的運作方式
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Docker 執行命令組態設定執行個人化工具容器。
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 85344d432c169ce8c4adffd059059660672df02e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156936"
---
# <a name="configure-personalizer-containers"></a>設定個人化工具容器

[個人化工具] 容器執行階段環境可使用 `docker run` 命令引數來設定。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

## <a name="configuration-settings"></a>組態設定

此容器具有下列組態設定：

|必要|設定|目的|
|--|--|--|
|yes|[ApiKey](#apikey-configuration-setting)|用來追蹤帳單資訊。|
|否|[ApplicationInsights](#applicationinsights-setting)|可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援新增至容器。|
|yes|[計費](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|yes|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄 (和選擇性的計量資料) 寫入至 Fluentd 伺服器。|
|否|[HTTP Proxy](#http-proxy-credentials-settings)|設定 HTTP Proxy 以進行輸出要求。|
|否|[記錄](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[裝載](#mount-settings)|從主機電腦將資料讀取和寫入至容器，以及從容器將資料讀取和寫回主機電腦。|

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 及 [`Eula`](#eula-setting) 設定，因此您必須同時為這三個設定提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](personalizer-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 的值，該值必須是有效金鑰，且適用於為 [`Billing`](#billing-configuration-setting) 組態設定而指定的「個人化工具」資源。

此設定可在下列位置找到：

* Azure 入口網站：**個人化工具**的資源管理，位於 [金鑰] 下方

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing` 設定會指定 Azure 上用來計量容器帳單資訊的「個人化工具」資源端點 URI。 您必須為此組態設定指定值，且該值必須是適用於 Azure 上「個人化工具」資源的有效端點 URI。

此設定可在下列位置找到：

* Azure 入口網站：**個人化工具**的概觀，標示為 `Endpoint`

|必要| Name | 資料類型 | 說明 |
|--|------|-----------|-------------|
|yes| `Billing` | 字串 | 計費端點 URI<br><br>範例：<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP Proxy 認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。

容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](personalizer-container-howto.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選用| Name | 資料類型 | 說明 |
|-------|------|-----------|-------------|
|不允許| `Input` | 字串 | 個人化工具容器不會使用此項目。|
|選用| `Output` | 字串 | 輸出裝載的目標。 預設值為 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令 

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](personalizer-container-howto.md#stop-the-container)。

* **行接續字元**：以下幾節的 Docker 命令會使用反斜線 `\` 作為 bash shell 的行接續字元。 請根據您主機作業系統的需求加以替換或移除。 例如，適用於 Windows 的行接續字元是插入號 `^`。 以插入號取代反斜線。 
* **引數順序**：若非十分熟悉 Docker 容器，請勿變更引數的順序。

使用您自己的值取代括弧 `{}` 中的值：

| Placeholder | 值 | 格式或範例 |
|-------------|-------|---|
|{BILLING_KEY} | 個人化工具資源的端點金鑰。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | 包括區域的計費端點值。|`https://westus2.api.cognitive.microsoft.com/`|

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](personalizer-container-howto.md#billing)。
> ApiKey 值是 [Azure 個人化工具資源金鑰] 頁面中的 [金鑰]。 

## <a name="personalizer-container-docker-examples"></a>個人化工具容器 Docker 範例

下列是個人化工具容器的 Docker 範例。 

### <a name="basic-example"></a>基本範例 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/[container-name] \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>使用命令列引數的記錄範例

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/[container-name] \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>後續步驟

[了解如何使用 Docker 容器](personalizer-container-howto.md)