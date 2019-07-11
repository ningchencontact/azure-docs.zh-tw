---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 46376477aae9f94c1c8f6e1dd6bc718d213cc373
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712477"
---
容器具有下列組態設定：

|必要|設定|用途|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|帳單資訊的追蹤。|
|否|[ApplicationInsights](#applicationinsights-setting)|可讓您新增[Azure Application Insights](https://docs.microsoft.com/azure/application-insights)遙測的支援，以您的容器。|
|是|[Billing](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|寫入記錄檔，並選擇性地 Fluentd 伺服器的計量資料。|
|否|Http Proxy|設定 HTTP proxy 來進行輸出要求。|
|否|[Logging](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[Mounts](#mount-settings)|讀取和寫入資料從主機電腦，以容器和容器上一步要在主機電腦。|
