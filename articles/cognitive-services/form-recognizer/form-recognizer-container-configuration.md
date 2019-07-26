---
title: 如何設定表單辨識器的容器
titleSuffix: Azure Cognitive Services
description: 了解如何設定表單辨識器容器來剖析表單和資料表資料。
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 4a490e8a9f111985df9c9e8c9f73bc36d686cc2a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348702"
---
# <a name="configure-form-recognizer-containers"></a>設定表單辨識器容器

藉由使用 Azure 表單辨識器容器, 您可以建立已優化的應用程式架構, 以充分利用健全的雲端功能和邊緣位置。

您可以使用`docker run`命令引數來設定表單辨識器容器執行時間環境。 此容器有數個必要的設定和一些選擇性的設定。 如需一些範例, 請參閱「 [docker 執行命令範例](#example-docker-run-commands)」一節。 容器專屬設定包括計費設定。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[和設定`Eula`會一起使用。](#eula-setting) [`Billing`](#billing-configuration-setting) 您必須提供所有三個設定的有效值;否則, 您的容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](form-recognizer-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey`設定會指定用來追蹤容器帳單資訊的 Azure 資源金鑰。 ApiKey 的值必須是在 [帳單設定] 區段`Billing`中針對指定的_表單辨識器_資源的有效索引鍵。

您可以在 [Azure 入口網站] 中的 [**金鑰**] 下的 [**表單辨識器資源管理**] 中找到這項設定。

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

設定會指定 Azure 上用來計量容器帳單資訊的_表單辨識器_資源的端點 URI。 `Billing` 此設定的值必須是 Azure 上的_表單辨識器_資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

您可以在 [Azure 入口網站] 的 [**表單辨識器**] 中, 于 [**端點**] 底下找到這項設定。

|必要項| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費端點 URI<br><br>範例:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy 認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>掛接設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在`--mount` [ `docker run`命令](https://docs.docker.com/engine/reference/commandline/run/)中指定選項, 以指定輸入裝載或輸出裝載。

表單辨識器容器需要輸入裝載和輸出裝載。 輸入裝載可以是唯讀的, 而且需要存取用於定型和評分的資料。 輸出裝載必須是可寫入的, 而且您可以使用它來儲存模型和暫存資料。

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外,[主機電腦](form-recognizer-container-howto.md#the-host-computer)的裝載位置可能無法存取, 因為 Docker 服務帳戶許可權與主機裝載位置許可權之間發生衝突。

|選擇性| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|必要項| `Input` | String | 輸入裝載的目標。 預設值為 `/input`。    <br><br>範例:<br>`--mount type=bind,src=c:\input,target=/input`|
|必要項| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。  <br><br>範例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。 當它執行時, 容器會繼續執行, 直到您將[它停止](form-recognizer-container-howto.md#stop-the-container)為止。

* **行接續字元**：下列各節中的 Docker 命令會使用反斜線 (\\) 做為行接續字元。 視您的主機作業系統需求而定, 取代或移除此字元。
* **引數順序**：除非您熟悉 Docker 容器, 否則請勿變更引數的順序。

將下表中的 {_argument_name_} 取代為您自己的值:

| 預留位置 | 值 |
|-------------|-------|
|{FORM_RECOGNIZER_API_KEY} | 用來啟動容器的索引鍵。 可在 [Azure 入口網站表單辨識器金鑰] 頁面上取得。  |
|{FORM_RECOGNIZER_ENDPOINT_URI} | 計費端點 URI 值可在 Azure 入口網站表單辨識器總覽頁面上取得。|
|{COMPUTER_VISION_API_KEY}| 金鑰可在 Azure 入口網站電腦視覺 API 金鑰 頁面上取得。|
|{COMPUTER_VISION_ENDPOINT_URI}|計費端點。 如果您使用以雲端為基礎的電腦視覺資源, 則 URI 值可在 Azure 入口網站電腦視覺 API 總覽頁面上取得。 如果您使用*認知服務辨識文字*容器, 請使用在`docker run`命令中傳遞至容器的計費端點 URL。|

> [!IMPORTANT]
> 若要執行容器, 請指定`Eula`、 `Billing`和`ApiKey`選項, 否則容器將不會啟動。 如需詳細資訊，請參閱[帳單](#billing-configuration-setting)。

> [!NOTE] 
> ApiKey 值是 [Azure 表單辨識器資源金鑰] 頁面中的**金鑰**。

## <a name="form-recognizer-container-docker-examples"></a>表單辨識器容器 Docker 範例

以下是表單辨識器容器的 Docker 範例。

### <a name="basic-example-for-form-recognizer"></a>表單辨識器的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>表單辨識器的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>後續步驟

* 請參閱[安裝及執行容器](form-recognizer-container-howto.md)。
