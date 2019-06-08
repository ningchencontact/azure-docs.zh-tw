---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: c4abc5fa89b48b6fc55637e9ff3b259387d0d410
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116687"
---
此容器具有下列組態設定：

|必要|設定|目的|
|--|--|--|
|yes|[ApiKey](#apikey-configuration-setting)|用來追蹤帳單資訊。|
|否|[ApplicationInsights](#applicationinsights-setting)|可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援新增至容器。|
|yes|[Billing](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|yes|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄 (和選擇性的計量資料) 寫入至 Fluentd 伺服器。|
|否|Http Proxy|設定 HTTP Proxy 以進行輸出要求。|
|否|[Logging](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[Mounts](#mount-settings)|從主機電腦將資料讀取和寫入至容器，以及從容器將資料讀取和寫回主機電腦。|
