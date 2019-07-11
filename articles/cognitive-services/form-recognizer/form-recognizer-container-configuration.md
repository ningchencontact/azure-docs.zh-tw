---
title: 如何設定表單的辨識器的容器
titleSuffix: Azure Cognitive Services
description: 了解如何設定表單辨識器容器來剖析表單和資料表資料。
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7752b09dd1bf20d796b19d03e62426b098486c39
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718447"
---
# <a name="configure-form-recognizer-containers"></a>設定表單辨識器容器

藉由使用 Azure 形式辨識器的容器，您可以建置已最佳化，可善用強大的雲端功能及邊緣位置的應用程式架構。

您可以使用設定表單的辨識器的容器執行階段環境`docker run`命令引數。 此容器有數個必要設定和一些選擇性的設定。 幾個範例，請參閱["範例 docker run 命令"](#example-docker-run-commands)一節。 容器專屬設定包括計費設定。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting)， [ `Billing` ](#billing-configuration-setting)，以及[ `Eula` ](#eula-setting)設定一起使用。 您必須提供有效的值，所有的三個設定;否則，不會啟動您的容器。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](form-recognizer-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey`設定會指定用來追蹤容器的帳單資訊的 Azure 資源金鑰。 ApiKey 的值必須是有效的金鑰，如_形式辨識器_指定的資源， `Billing` 「 帳務組態設定 」 一節。

您可以找到此設定在 Azure 入口網站中，在**形式辨識器資源管理**下方**金鑰**。

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing`設定會指定端點 URI 的_形式辨識器_上用來測量之容器的帳單資訊的 Azure 資源。 此組態設定的值必須是有效的端點 URI 的_形式辨識器_在 Azure 上的資源。 容器會每隔 10 到 15 分鐘回報使用量。

您可以找到此設定在 Azure 入口網站中，在**形式辨識器概觀**下方**端點**。

|必要項| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費端點 URI<br><br>範例：<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy 的認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以藉由指定指定的輸入的掛接或輸出掛接`--mount`選項[`docker run`命令](https://docs.docker.com/engine/reference/commandline/run/)。

表單辨識器容器需要的輸入的掛接，以及輸出掛接。 輸入的裝載可以是唯讀的並使用於訓練和評分的資料存取的。 輸出掛接可寫入，而且您會使用它來儲存暫存資料與模型。

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，掛接的位置[主機電腦](form-recognizer-container-howto.md#the-host-computer)可能因為而無法存取 Docker 服務帳戶權限與主應用程式掛接位置的權限之間的衝突。

|選擇性| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|必要項| `Input` | String | 輸入裝載的目標。 預設值為 `/input`。    <br><br>範例：<br>`--mount type=bind,src=c:\input,target=/input`|
|必要| `Output` | String | 輸出裝載的目標。 預設值為 `/output`。  <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。 當它執行時，容器會繼續執行，直到您[將它停止](form-recognizer-container-howto.md#stop-the-container)。

* **行接續字元**：下列各節中的，Docker 命令會使用反斜線 (\\) 做為行接續字元。 取代或移除此字元，根據主機作業系統的需求。
* **引數順序**：請勿變更引數的順序，除非您已熟悉 Docker 容器。

取代 {_數名稱_} 下表以您自己的值：

| Placeholder | 值 |
|-------------|-------|
|{BILLING_KEY} | 用來啟動容器的索引鍵。 使用 Azure 入口網站表單辨識器金鑰 頁面上。  |
|{BILLING_ENDPOINT_URI} | 使用 Azure 入口網站表單辨識器概觀頁面上的計費的端點 URI 值。|
|{COMPUTER_VISION_API_KEY}| 使用 Azure 入口網站的電腦視覺 API 金鑰 頁面上的索引鍵。|
|{COMPUTER_VISION_ENDPOINT_URI}|計費端點。 如果您使用雲端架構的電腦視覺資源，就有一個 URI 值可在 Azure 入口網站的電腦視覺 API 概觀 頁面。 如果您使用*認知服務-辨識-檢索*容器，使用計費會傳遞至容器中的端點 URL`docker run`命令。|

> [!IMPORTANT]
> 若要執行的容器，請指定`Eula`， `Billing`，和`ApiKey`選項; 容器不啟動，否則為。 如需詳細資訊，請參閱[帳單](#billing-configuration-setting)。

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
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
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
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>後續步驟

* 檢閱[安裝和執行的容器](form-recognizer-container-howto.md)。
