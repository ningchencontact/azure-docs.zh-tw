---
title: 設定容器-翻譯工具文字
titleSuffix: Azure Cognitive Services
description: 翻譯工具文字提供具有通用設定架構的容器，讓您可以輕鬆地設定及管理容器的儲存體、記錄和遙測，以及安全性設定。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501004"
---
# <a name="configure-translator-text-docker-containers"></a>設定翻譯工具文字 docker 容器

翻譯工具文字提供具有通用設定架構的每個容器，讓您可以輕鬆地設定及管理容器的儲存體、記錄和遙測，以及安全性設定。

## <a name="configuration-settings"></a>組態設定

容器具有下列設定：

|必要|設定|目的|
|--|--|--|
|否|[ApplicationInsights](#applicationinsights-setting)|可將[Azure 應用程式 Insights](https://docs.microsoft.com/azure/application-insights)遙測支援新增至您的容器。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄和（選擇性）計量資料寫入至 Fluentd 伺服器。|
|否|HTTP Proxy|設定 HTTP proxy 以進行輸出要求。|
|否|[Logging](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[裝載](#mount-settings)|從主機電腦將資料讀取和寫入至容器，並將其從容器還原至主機電腦。|

> [!IMPORTANT]
> 必須提供 `accept`值的[`Eula`](#eula-setting)設定;否則，您的容器將不會啟動。

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy 認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 `--mount`docker run[ 命令中指定 ](https://docs.docker.com/engine/reference/commandline/run/) 選項，以指定輸入裝載或輸出裝載。

翻譯工具文字容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

視主機作業系統而定，主機裝載位置的確切語法會有所不同。 此外，[主機電腦](how-to-install-containers.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選用| 名稱 | 資料類型 | 說明 |
|-------|------|-----------|-------------|
|不允許| `Input` | 字串 | 翻譯工具文字容器不會使用此。|
|選用| `Output` | 字串 | 輸出裝載的目標。 預設值為 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令 

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](how-to-install-containers.md#stop-the-container)。

* **行接續字元**：下列各節中的 docker 命令會使用反斜線（`\`）做為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **引數順序**：除非您非常熟悉 docker 容器，否則請勿變更引數的順序。

## <a name="translator-text-container-docker-examples"></a>翻譯工具文字容器 docker 範例

下列 docker 範例適用于翻譯工具文字容器。

### <a name="basic-example"></a>基本範例

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>記錄範例

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>後續步驟

* 檢閱[如何安裝及執行容器](how-to-install-containers.md)
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
