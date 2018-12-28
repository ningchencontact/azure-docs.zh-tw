---
title: Docker 容器設定
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 容器執行階段環境可使用 `docker run` 命令引數來設定。 LUIS 有數個必要的設定，和一些選擇性的設定。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080260"
---
# <a name="configure-containers"></a>設定容器

Language Understanding (LUIS) 容器執行階段環境可使用 `docker run` 命令引數來設定。 LUIS 有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括輸入[裝載設定](#mount-settings)和計費設定。 

容器設定是[階層式](#hierarchical-settings)的，可使用[環境變數](#environment-variable-settings)或 Docker [命令列引數](#command-line-argument-settings)來設定。

## <a name="configuration-settings"></a>組態設定

此容器具有下列組態設定：

|必要|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-setting)|用來追蹤帳單資訊。|
|否|[ApplicationInsights](#applicationinsights-setting)|可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援新增至容器。|
|是|[計費](#billing-setting)|指定 Azure 上的 _Language Understanding_ 資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄 (和選擇性的計量資料) 寫入至 Fluentd 伺服器。|
|否|[記錄](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|是|[裝載](#mount-settings)|從[主機電腦](luis-container-howto.md#the-host-computer)將資料讀取和寫入至容器，以及從容器將資料讀取和寫回主機電腦。|

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-setting)、[`Billing`](#billing-setting) 及 [`Eula`](#eula-setting) 設定，因此您必須同時為這三個設定提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](luis-container-howto.md#billing)。

## <a name="apikey-setting"></a>ApiKey 設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 的值，且該值必須是為 [`Billing`](#billing-setting) 組態設定而指定的 _Language Understanding_ 資源所適用的有效金鑰。

此設定可在兩個位置找到：

* Azure 入口網站：**Language Understanding** 的 [資源管理]，位於 [金鑰] 下方
* LUIS 入口網站：[金鑰和端點設定] 頁面。 

請勿使用入門金鑰或撰寫金鑰。 

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

`ApplicationInsights` 設定可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援新增至容器。 Application Insights 可提供深入容器監視。 您可輕鬆監視容器的可用性、效能和使用情形。 您也可以快速識別並診斷容器中的錯誤。

下表說明 `ApplicationInsights` 區段下所支援的組態設定。

|必要| Name | 資料類型 | 說明 |
|--|------|-----------|-------------|
|否| `InstrumentationKey` | 字串 | Application Insights 執行個體的檢測金鑰，容器的遙測資料會傳送到這裡。 如需詳細資訊，請參閱 [ASP.NET Core 的 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)。 <br><br>範例：<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>帳單支援

`Billing` 設定會指定 Azure 上用來計量容器帳單資訊的 _Language Understanding_ 資源的端點 URI。 您必須為此組態設定指定值，且該值必須是 Azure 上的 _Language Understanding_ 資源所適用的有效端點 URI。

此設定可在兩個位置找到：

* Azure 入口網站：**Language Understanding** 的 [概觀]，標示為 `Endpoint`
* LUIS 入口網站：[金鑰和端點設定] 頁面，包含在端點 URI 中。

|必要| Name | 資料類型 | 說明 |
|--|------|-----------|-------------|
|是| `Billing` | 字串 | 計費端點 URI<br><br>範例：<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Eula 設定

`Eula` 設定可指出您已接受容器的授權。 您必須為此組態設定指定值，且該值必須設定為 `accept`。

|必要| Name | 資料類型 | 說明 |
|--|------|-----------|-------------|
|是| `Eula` | 字串 | 接受授權<br><br>範例：<br>`Eula=accept` |

認知服務容器是在[您的合約](https://go.microsoft.com/fwlink/?linkid=2018657) (控管您對於 Azure 的使用) 底下獲得授權。 如果您沒有可控管您使用 Azure 的現有合約，您同意控管使用 Azure 的合約是 [Microsoft 線上訂閱合約](https://go.microsoft.com/fwlink/?linkid=2018755) (其中包含[線上服務條款](https://go.microsoft.com/fwlink/?linkid=2018760))。 對於預覽版，您也同意 [Microsoft Azure 預覽專用的補充使用條款](https://go.microsoft.com/fwlink/?linkid=2018815)。 使用容器即表示您同意這些條款。

## <a name="fluentd-settings"></a>Fluentd 設定

Fluentd 是適用於統一記錄的開放原始碼資料收集器。 `Fluentd` 設定可管理容器對 [Fluentd](https://www.fluentd.org) 伺服器的連線。 LUIS 容器包含 Fluentd 記錄提供者，可讓您的容器將記錄 (及選擇性的計量資料) 寫入至 Fluentd 伺服器。

下表說明 `Fluentd` 區段下所支援的組態設定。

| Name | 資料類型 | 說明 |
|------|-----------|-------------|
| `Host` | 字串 | Fluentd 伺服器的 IP 位址或 DNS 主機名稱。 |
| `Port` | 整數  | Fluentd 伺服器的連接埠。<br/> 預設值為 24224。 |
| `HeartbeatMs` | 整數  | 活動訊號間隔，以毫秒為單位。 如果在此間隔到期之前未傳送任何事件流量，系統便會將活動訊號傳送至 Fluentd 伺服器。 預設值為 60000 毫秒 (1 分鐘)。 |
| `SendBufferSize` | 整數  | 針對傳送作業所配置的網路緩衝空間，以位元組為單位。 預設值為 32768 個位元組 (32 KB)。 |
| `TlsConnectionEstablishmentTimeoutMs` | 整數  | 向 Fluentd 伺服器建立 SSL/TLS 連線的逾時，以毫秒為單位。 預設值是 10000 毫秒 (10 秒)。<br/> 如果將 `UseTLS` 設定為 false，便會忽略此值。 |
| `UseTLS` | BOOLEAN | 指出容器是否應使用 SSL/TLS 來與 Fluentd 伺服器通訊。 預設值為 False。 |

## <a name="logging-settings"></a>記錄設定

`Logging` 設定可管理適用於容器的 ASP.NET Core 記錄支援。 對於您用於 ASP.NET Core 應用程式的容器，您可以使用相同的組態設定和值。 

LUIS 容器支援下列記錄提供者：

|提供者|目的|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` 記錄提供者。 支援此記錄提供者的所有 ASP.NET Core 組態設定和預設值。|
|[偵錯](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` 記錄提供者。 支援此記錄提供者的所有 ASP.NET Core 組態設定和預設值。|
|[磁碟](#disk-logging)|JSON 記錄提供者。 此記錄提供者會將記錄資料寫入至輸出裝載。|

### <a name="disk-logging"></a>磁碟記錄
  
`Disk` 記錄提供者支援下列組態設定：  

| Name | 資料類型 | 說明 |
|------|-----------|-------------|
| `Format` | 字串 | 適用於記錄檔的輸出格式。<br/> **附註：** 此值必須設定為 `json` 以啟用記錄提供者。 如果在具現化期間指定此值，但沒有同時指定輸出裝載，便會發生錯誤。 |
| `MaxFileSize` | 整數  | 記錄檔的大小上限，以 MB 為單位。 當目前記錄檔的大小符合或超過此值時，記錄提供者便會建立新的記錄檔。 如果指定 -1，記錄檔的大小便只會受限於輸出裝載的檔案大小上限 (若有的話)。 預設值為 1。 |

如需設定 ASP.NET Core 記錄支援的詳細資訊，請參閱[設定檔案組態](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)。

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。 

LUIS 容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](luis-container-howto.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

下表說明支援的設定。

|必要| Name | 資料類型 | 說明 |
|-------|------|-----------|-------------|
|是| `Input` | 字串 | 輸入裝載的目標。 預設值為 `/input`。 這是 LUIS 套件檔案的位置。 <br><br>範例：<br>`--mount type=bind,src=c:\input,target=/input`|
|否| `Output` | 字串 | 輸出裝載的目標。 預設值為 `/output`。 這是記錄的位置。 其中包括 LUIS 查詢記錄和容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>階層式設定

LUIS 容器的設定是階層式的，[主機電腦](luis-container-howto.md#the-host-computer)上的所有容器會使用共用的階層。

您可以使用下列其中一項來指定設定：

* [環境變數](#environment-variable-settings)
* [命令列引數](#command-line-argument-settings)

環境變數的值會覆寫命令列引數的值，並進一步覆寫容器映像的預設值。 如果您針對相同的組態設定，在環境變數和命令列引數中指定不同的值，則已具現化的容器將會使用環境變數中的值。

|優先順序|設定位置|
|--|--|
|1|環境變數| 
|2|命令列|
|3|容器映像預設值|

### <a name="environment-variable-settings"></a>環境變數設定

使用環境變數的好處包括：

* 可以設定多個設定。
* 多個容器可以使用相同的設定。

### <a name="command-line-argument-settings"></a>命令列引數設定

使用命令列引數的好處，在於每個容器可以使用不同的設定。

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](luis-container-howto.md#stop-the-container)。


* **行接續字元**：以下幾節的 Docker 命令會使用反斜線 `\` 作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **引數順序**：若非十分熟悉 Docker 容器，請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| Placeholder | 值 | 格式或範例 |
|-------------|-------|---|
|{ENDPOINT_KEY} | 已定型 LUIS 應用程式的端點金鑰。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | 計費端點值可在 Azure 入口網站的 [Language Understanding 概觀] 頁面上取得。|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](luis-container-howto.md#billing)。
> ApiKey 值是 LUIS 入口網站中的 [金鑰和端點] 頁面所包含的 [金鑰]，此值也可以在 Azure Language Understanding 資源的金鑰頁面上取得。 

### <a name="basic-example"></a>基本範例

下列範例會以最少的引數來執行容器：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> 上述命令使用 `c:` 磁碟機中的目錄，以避免在 Windows 上發生任何權限衝突。 如果您需要使用特定目錄作為輸入目錄，您可能需要授與 Docker 服務權限。 上述 Docker 命令使用反斜線 `\` 作為行接續字元。 請根據您[主機電腦](luis-container-howto.md#the-host-computer)作業系統的需求取代或移除此項目。 若非十分熟悉 Docker 容器，請勿變更引數的順序。


### <a name="applicationinsights-example"></a>ApplicationInsights 範例

下列範例會設定 ApplicationInsights 引數，以在容器執行時將遙測資料傳送至 Application Insights：

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>使用命令列引數的記錄範例

下列命令會設定記錄層級 `Logging:Console:LogLevel`，以將記錄層級設定為 [`Information`](https://msdn.microsoft.com)。 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>使用環境變數的記錄範例

下列命令會使用名為 `Logging:Console:LogLevel` 的環境變數，將記錄層級設定為 [`Information`](https://msdn.microsoft.com)。 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>後續步驟

* 檢閱[如何安裝及執行容器](luis-container-howto.md)
* 參閱[常見問題集 (FAQ)](luis-resources-faq.md) 來解決與 LUIS 功能相關的問題。