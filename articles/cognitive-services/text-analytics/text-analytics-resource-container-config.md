---
title: 設定容器
titlesuffix: Text Analytics - Azure Cognitive Services
description: 文字分析為每個容器提供了常見的設定架構，因此您可以輕易地設定及管理您容器的儲存體、記錄和遙測，以及安全性設定。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 7e993b9ccc57359ac64186765b7b704535eb5a57
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086669"
---
# <a name="configure-containers"></a>設定容器

文字分析為每個容器提供了常見的設定架構，因此您可以輕易地設定及管理您容器的儲存體、記錄和遙測，以及安全性設定。

## <a name="configuration-settings"></a>組態設定

文字分析容器中的組態設定為階層式，且所有容器都會使用以下列最上層結構為基礎的共用階層：

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [驗證](#authentication-configuration-settings)
* [計費](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [記錄](#logging-configuration-settings)
* [裝載](#mounts-configuration-settings)

從文字分析容器具現化容器時，您可以使用[環境變數](#configuration-settings-as-environment-variables)或[命令列引數](#configuration-settings-as-command-line-arguments)來指定組態設定。

環境變數的值會覆寫命令列引數的值，並進一步覆寫容器映像的預設值。 換句話說，如果您針對相同的組態設定 (例如 `Logging:Disk:LogLevel`) 在環境變數和命令列引數中指定不同的值，然後再將容器具現化，則已具現化的容器將會使用環境變數中的值。

### <a name="configuration-settings-as-environment-variables"></a>以環境變數指定組態設定

您可以使用 [ASP.NET Core 環境變數語法](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment)來指定組態設定。

容器會在具現化時讀取使用者環境變數。 如果存在環境變數，則該環境變數的值將會覆寫指定組態設定的預設值。 使用環境變數的優點，在於可在具現化容器之前設定多個組態設定，且多個容器可以自動使用相同的組態設定集。

例如，下列命令會使用環境變數來將主控台記錄層級設定為 [LogLevel.Information](https://msdn.microsoft.com)，然後從情感分析容器映像具現化容器。 環境變數的值會覆寫預設的組態設定。

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>以命令列引數指定組態設定

您可以使用 [ASP.NET Core 命令列引數語法](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments)來指定組態設定。

您可以在用來從已下載容器映像將容器具現化之 [docker run](https://docs.docker.com/engine/reference/commandline/run/) \(英文\) 命令的選擇性 `ARGS` 參數中指定組態設定。 使用命令列引數的好處，在於每個容器都可以使用不同的自訂組態設定集。

例如，下列命令會從情感分析容器映像具現化容器，然後將主控台記錄層級設定為 LogLevel.Information 並覆寫預設的組態設定。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 組態設定會指定 Azure 上用來追蹤容器帳單資訊之文字分析資源的組態金鑰。 您必須為此組態設定指定值，且該值必須是適用於針對 [`Billing`](#billing-configuration-setting) 所指定之文字分析資源的有效組態金鑰。

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 及 [`Eula`](#eula-configuration-setting) 組態設定，因此您必須同時為它們三個提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](how-tos/text-analytics-how-to-install-containers.md#billing)。

## <a name="applicationinsights-configuration-settings"></a>ApplicationInsights 組態設定

`ApplicationInsights` 區段中的組態設定可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援加入至您的容器。 Application Insights 可提供深入至程式碼層級的深入容器監視。 您可輕鬆監視容器的可用性、效能和使用情形。 您也可以快速識別並診斷容器的錯誤，不必等使用者回報。

下表說明 `ApplicationInsights` 區段下所支援的組態設定。

| Name | 資料類型 | 說明 |
|------|-----------|-------------|
| `InstrumentationKey` | 字串 | Application Insights 執行個體的檢測金鑰，容器的遙測資料會傳送到這裡。 如需詳細資訊，請參閱 [ASP.NET Core 的 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)。 |

## <a name="authentication-configuration-settings"></a>Authentication 組態設定

`Authentication` 組態設定能為您的容器提供 Azure 安全性選項。 雖然此區段中的組態設定可供文字分析容器中的所有容器使用，但組態設定值的使用方式會隨著個別容器而有所不同，且容器可能完全不會使用此區段。

下表說明 `Authentication` 區段下所支援的組態設定。

| Name | 資料類型 | 說明 |
|------|-----------|-------------|
| `ApiKey` | 字串或陣列 | 容器用來存取其他 Azure 資源的 Azure 訂用帳戶金鑰 (如果容器有需要)。<br/> 如果容器使用多個訂用帳戶金鑰，則此值會指定為字串陣列；否則，會使用字串值來指定容器所使用的單一訂用帳戶金鑰。 |

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing` 組態設定會指定 Azure 上用來對容器計算帳單資訊的文字分析資源的端點 URI。 您必須為此組態設定指定值，且該值必須是適用於 Azure 上文字分析資源的有效端點 URI。

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 及 [`Eula`](#eula-configuration-setting) 組態設定，因此您必須同時為它們三個提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](how-tos/text-analytics-how-to-install-containers.md#billing)。

## <a name="eula-configuration-setting"></a>Eula 組態設定

`Eula` 組態設定會指出您已接受容器的授權。 您必須為此組態設定指定值，且該值必須設定為 `accept`。

> [!IMPORTANT]
> 系統會同時使用 [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 及 [`Eula`](#eula-configuration-setting) 組態設定，因此您必須同時為它們三個提供有效的值，否則容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](how-tos/text-analytics-how-to-install-containers.md#billing)。

認知服務容器是在[您的合約](https://go.microsoft.com/fwlink/?linkid=2018657) (控管您對於 Azure 的使用) 底下獲得授權。 如果您沒有可控管您使用 Azure 的現有合約，您同意控管使用 Azure 的合約是 [Microsoft 線上訂閱合約](https://go.microsoft.com/fwlink/?linkid=2018755) (其中包含[線上服務條款](https://go.microsoft.com/fwlink/?linkid=2018760))。 對於預覽版，您也同意 [Microsoft Azure 預覽專用的補充使用條款](https://go.microsoft.com/fwlink/?linkid=2018815)。 使用容器即表示您同意這些條款。

## <a name="fluentd-configuration-settings"></a>Fluentd 組態設定

`Fluentd` 區段會管理適用於 [Fluentd](https://www.fluentd.org) 的組態設定，這是適用於統一記錄的開放原始碼資料收集器。 文字分析容器會包含 Fluentd 記錄提供者，這可讓您的容器將記錄 (及選擇性的計量資料) 寫入至 Fluentd 伺服器。

下表說明 `Fluentd` 區段下所支援的組態設定。

| Name | 資料類型 | 說明 |
|------|-----------|-------------|
| `Host` | 字串 | Fluentd 伺服器的 IP 位址或 DNS 主機名稱。 |
| `Port` | 整數  | Fluentd 伺服器的連接埠。<br/> 預設值為 24224。 |
| `HeartbeatMs` | 整數  | 活動訊號間隔，以毫秒為單位。 如果在此間隔到期之前未傳送任何事件流量，系統便會將活動訊號傳送至 Fluentd 伺服器。 預設值為 60000 毫秒 (1 分鐘)。 |
| `SendBufferSize` | 整數  | 針對傳送作業所配置的網路緩衝空間，以位元組為單位。 預設值為 32768 個位元組 (32 KB)。 |
| `TlsConnectionEstablishmentTimeoutMs` | 整數  | 向 Fluentd 伺服器建立 SSL/TLS 連線的逾時，以毫秒為單位。 預設值是 10000 毫秒 (10 秒)。<br/> 如果將 `UseTLS` 設定為 false，便會忽略此值。 |
| `UseTLS` | BOOLEAN | 指出容器是否應使用 SSL/TLS 來與 Fluentd 伺服器通訊。 預設值為 False。 |

## <a name="logging-configuration-settings"></a>Logging 組態設定

`Logging` 組態設定能管理適用於您容器的 ASP.NET Core 記錄支援。 針對您的容器，您可以使用適用於 ASP.NET Core 應用程式的相同組態設定和值。 文字分析容器支援下列記錄提供者：

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  ASP.NET Core `Console` 記錄提供者。 支援此記錄提供者的所有 ASP.NET Core 組態設定和預設值。
* [偵錯](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  ASP.NET Core `Debug` 記錄提供者。 支援此記錄提供者的所有 ASP.NET Core 組態設定和預設值。
* 磁碟  
  JSON 記錄提供者。 此記錄提供者會將記錄資料寫入至輸出裝載。  
  `Disk` 記錄提供者支援下列組態設定：  

  | Name | 資料類型 | 說明 |
  |------|-----------|-------------|
  | `Format` | 字串 | 適用於記錄檔的輸出格式。<br/> **附註：** 此值必須設定為 `json` 以啟用記錄提供者。 如果在具現化期間指定此值，但沒有同時指定輸出裝載，便會發生錯誤。 |
  | `MaxFileSize` | 整數  | 記錄檔的大小上限，以 MB 為單位。 當目前記錄檔的大小符合或超過此值時，記錄提供者便會建立新的記錄檔。 如果指定 -1，記錄檔的大小便只會受限於輸出裝載的檔案大小上限 (若有的話)。 預設值為 1。 |

如需設定 ASP.NET Core 記錄支援的詳細資訊，請參閱[設定檔案組態](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)。

## <a name="mounts-configuration-settings"></a>裝載組態設定

由文字分析容器提供的 Docker 容器具有無狀態且不可變的設計。 換句話說，在容器內建立的檔案會被儲存在可寫入的容器層中，其只會在容器執行期間保存，且無法被輕易存取。 如果停止或移除該容器，系統便會終結在該容器中建立的檔案。

不過，由於它們是 Docker 容器，如果容器支援的話，您可以使用 Docker 儲存體選項 (例如磁碟區和繫結裝載) 來讀取及寫入容器外的保存資料。 如需如何指定及管理 Docker 儲存體選項的詳細資訊，請參閱[在 Docker 中管理資料](https://docs.docker.com/storage/)。

> [!NOTE]
> 您通常不需要變更這些組態設定的值。 相反地，在指定容器的輸入和輸出裝載時，您將會使用這些組態設定中所指定的值作為目的地。 如需指定輸入和輸出裝載的詳細資訊，請參閱[輸入和輸出裝載](#input-and-output-mounts)。

下表說明 `Mounts` 區段下所支援的組態設定。

| Name | 資料類型 | 說明 |
|------|-----------|-------------|
| `Input` | 字串 | 輸入裝載的目標。 預設值為 `/input`。 |
| `Output` | 字串 | 輸出裝載的目標。 預設值為 `/output`。 |

### <a name="input-and-output-mounts"></a>輸入和輸出裝載

根據預設，每個容器都可以支援一個「輸入裝載」 (容器可從其中讀取資料)，以及一個「輸出裝載」 (容器可對它寫入資料)。 容器不需要支援輸入或輸出裝載，但除了文字分析容器所支援的記錄選項以外，每個容器也可同時將輸入和輸出裝載用於容器的特定用途。 下表列出文字分析容器中各個容器的輸入和輸出裝載支援。

| 容器 | 輸入裝載 | 輸出裝載 |
|-----------|-------------|--------------|
|[關鍵片語擷取](#working-with-key-phrase-extraction) | 不支援 | 選用 |
|[語言偵測](#working-with-language-detection) | 不支援 | 選用 |
|[情感分析](#working-with-sentiment-analysis) | 不支援 | 選用 |

您可以在用來從已下載的容器映像將容器具現化的 [docker run](https://docs.docker.com/engine/reference/commandline/run/) \(英文\) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。 根據預設，輸入裝載會使用 `/input` 作為其目的地，而輸出裝載會使用 `/output` 作為其目的地。 在 `--mount` 選項中可以指定任何可在 Docker 容器主機中使用的 Docker 儲存體選項。

例如，下列命令會將 Docker 繫結裝載定義至主機電腦上的 `D:\Output` 資料夾作為輸出裝載，然後從情感分析容器映像具現化容器，並以 JSON 格式將記錄檔儲存至輸出裝載。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
