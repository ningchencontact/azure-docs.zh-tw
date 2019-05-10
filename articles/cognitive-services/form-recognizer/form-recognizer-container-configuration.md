---
title: 設定容器 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 了解如何設定表單辨識器容器來剖析表單和資料表資料。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 3f6cc32f6fb2a9fb11220ac1a3134fb3ae4d2f84
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147474"
---
# <a name="configure-form-recognizer-containers"></a>設定表單辨識器容器

表單辨識器容器可讓客戶建置最能同時利用健全雲端功能和邊緣位置的應用程式架構。

**表單辨識器**容器執行階段環境可使用 `docker run` 命令引數來設定。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 及 [`Eula`](#eula-setting) 設定，因此您必須同時為這三個設定提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](form-recognizer-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 的值，該值必須是有效金鑰，且適用於為 [`Billing`](#billing-configuration-setting) 組態設定而指定的「表單辨識器」資源。

此設定可在下列位置找到：

* Azure 入口網站：**表單辨識器**資源管理，位於 [金鑰] 下方

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing` 設定會指定 Azure 上用來計量容器帳單資訊的「表單辨識器」資源端點 URI。 您必須為此組態設定指定值，且該值必須是適用於 Azure 上「表單辨識器」資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 入口網站：**表單辨識器**的概觀，標示為 `Endpoint`

|必要| Name | 資料類型 | 說明 |
|--|------|-----------|-------------|
|yes| `Billing` | 字串 | 計費端點 URI<br><br>範例：<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>記錄設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。

表單辨識器容器需要輸入和輸出裝載。 輸入掛接可以是唯讀模式，且必須有此掛接才能存取將會用於定型和計分的資料。 輸出掛接必須可供寫入，且會用來儲存模型和暫存資料。

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](form-recognizer-container-howto.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。

|選用| Name | 資料類型 | 說明 |
|-------|------|-----------|-------------|
|必要| `Input` | 字串 | 輸入裝載的目標。 預設值為 `/input`。    <br><br>範例：<br>`--mount type=bind,src=c:\input,target=/input`|
|必要| `Output` | 字串 | 輸出裝載的目標。 預設值為 `/output`。  <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](form-recognizer-container-howto.md#stop-the-container)。

* **行接續字元**：以下幾節的 Docker 命令會使用反斜線 `\` 作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。
* **引數順序**：若非十分熟悉 Docker 容器，請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 此金鑰用來啟動容器，您可以在 Azure 入口網站的 [表單辨識器金鑰] 頁面上取得。  |
|{BILLING_ENDPOINT_URI} | 在 Azure 入口網站的 [表單辨識器概觀] 頁面上可以取得計費端點 URI 值。|
|{COMPUTER_VISION_API_KEY}| 在 Azure 入口網站的 [電腦視覺 API 金鑰] 頁面上可取得此金鑰。|
|{COMPUTER_VISION_ENDPOINT_URI}|計費端點。 如果您使用雲端式電腦視覺資源，則可在 Azure 入口網站的 [電腦視覺 API 概觀] 頁面上取得 URI 值。 如果您使用 `cognitive-services-recognize-text` 容器，請在 `docker run` 命令中使用已傳遞至容器的計費端點 URL。|

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing-configuration-setting)。
> ApiKey 值是 [Azure 表單辨識器資源金鑰] 頁面中的**金鑰**。

## <a name="form-recognizer-container-docker-examples"></a>表單辨識器容器 Docker 範例

以下是表單辨識器容器的 Docker 範例。

### <a name="basic-example-for-form-recognizer"></a>表單辨識器的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>表單辨識器的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>後續步驟

* 檢閱[如何安裝及執行容器](form-recognizer-container-howto.md)
