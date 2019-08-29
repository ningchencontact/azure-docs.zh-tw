---
title: 設定容器-文字分析
titleSuffix: Azure Cognitive Services
description: 文字分析為每個容器提供了常見的設定架構，因此您可以輕易地設定及管理您容器的儲存體、記錄和遙測，以及安全性設定。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: f1c42002343de1dd3b3ef6b9c9e35f458db925f4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051099"
---
# <a name="configure-text-analytics-docker-containers"></a>設定文字分析 Docker 容器

文字分析為每個容器提供了常見的設定架構，因此您可以輕易地設定及管理您容器的儲存體、記錄和遙測，以及安全性設定。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 及 [`Eula`](#eula-setting) 設定，因此您必須同時為這三個設定提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](how-tos/text-analytics-how-to-install-containers.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 的值，該值必須是有效金鑰，且適用於為 [`Billing`](#billing-configuration-setting) 組態設定而指定的_文字分析_資源。

此設定可在下列位置找到：

* Azure 入口網站：**文字分析**資源管理, 位於 [**金鑰**] 下方

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing` 設定會指定 Azure 上_文字分析_資源的端點 URI，用來對容器計算帳單資訊。 您必須為此組態設定指定值，且該值必須是適用於 Azure 上_文字分析_資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 入口網站：**文字分析**總覽, 加上標籤`Endpoint`

|必要項| Name | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 必要的計費端點 URI |

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

文字分析容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選擇性| Name | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|不允許| `Input` | String | 文字分析容器不會使用此項目。|
|選擇性| `Output` | String | 輸出裝載的目標。 預設值為 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令 

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)。

* **行接續字元**：以下幾節的 Docker 命令會使用反斜線 `\` 作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **引數順序**：若非十分熟悉 Docker 容器，請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | [Azure `Text Analytics` `Text Analytics`金鑰] 頁面上可用資源的端點金鑰。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 計費端點值可在 Azure `Text Analytics`總覽頁面上取得。| 如需明確的範例, 請參閱[收集必要的參數](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)。 |

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](how-tos/text-analytics-how-to-install-containers.md#billing)。
> ApiKey 值是 [Azure `Text Analytics`資源金鑰] 頁面中的金鑰。 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[關鍵片語擷取](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[語言偵測](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[情感分析](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>後續步驟

* 檢閱[如何安裝及執行容器](how-tos/text-analytics-how-to-install-containers.md)
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
