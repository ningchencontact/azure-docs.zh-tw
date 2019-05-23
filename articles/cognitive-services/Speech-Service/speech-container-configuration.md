---
title: 設定語音容器
titleSuffix: Azure Cognitive Services
description: 語音容器
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: diberry
ms.openlocfilehash: e2ed29bb61f553f68b9f9802884169361d5d983f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797916"
---
# <a name="configure-speech-service-containers"></a>設定語音服務容器

語音容器可讓客戶能夠建置一個已最佳化，可善用強大的雲端功能及邊緣位置的語音應用程式架構。 我們現在支援兩個語音容器**語音轉換文字**並**文字轉換語音**。 

**語音**容器的執行階段環境設定使用`docker run`命令引數。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

# <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 及 [`Eula`](#eula-setting) 設定，因此您必須同時為這三個設定提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](speech-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 值和值必須是有效的金鑰，如_語音_指定的資源[ `Billing` ](#billing-configuration-setting)組態設定。

此設定可在下列位置找到：

* Azure 入口網站：**語音的**資源管理下**金鑰**

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing`設定會指定端點 URI 的_語音_来測量之容器的帳單資訊使用在 Azure 上的資源。 您必須指定此組態設定值，和值必須是有效的端點 URI 的_語音_在 Azure 上的資源。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 入口網站：**語音的**概觀，標示為 `Endpoint`

|必要項| 名稱 | 数据类型 | 說明 |
|--|------|-----------|-------------|
|有| `Billing` | String | 計費端點 URI<br><br>範例：<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP Proxy 認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>掛接設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。

語音容器不會使用輸入或輸出掛接至訓練或服務的資料存放區。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](speech-container-howto.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選用| 名稱 | 数据类型 | 說明 |
|-------|------|-----------|-------------|
|不允許| `Input` | String | 語音容器不要使用這個動作。|
|選用| `Output` | String | 輸出裝載的目標。 預設值為 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令 

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](speech-container-howto.md#stop-the-container)。

* **行接續字元**：以下幾節的 Docker 命令會使用反斜線 `\` 作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **引數順序**：若非十分熟悉 Docker 容器，請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | Value | 格式或範例 |
|-------------|-------|---|
|{BILLING_KEY} | 語音資源端點索引鍵。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | 包括區域的計費端點值。|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing-configuration-setting)。
> ApiKey 值是**金鑰**從 Azure 語音資源的 [金鑰] 頁面。 

## <a name="speech-container-docker-examples"></a>語音容器 Docker 範例

下列 Docker 範例適用於語音的容器。 

### <a name="basic-example-for-speech-to-text"></a>語音文字的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
```

### <a name="basic-example-for-text-to-speech"></a>文字轉換語音的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
```

### <a name="logging-example-for-speech-to-text"></a>文字記錄語音的範例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
  Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>文字轉換語音的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
  Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>後續步驟

* 檢閱[如何安裝及執行容器](speech-container-howto.md)