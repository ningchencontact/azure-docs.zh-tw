---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: 4f9300ab1d688e1f5043f5b919e70c5a36c7c0e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063996"
---
容器具有下列組態設定：

|必要項|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|帳單資訊的追蹤。|
|否|[ApplicationInsights](#applicationinsights-setting)|可讓您新增[Azure Application Insights](https://docs.microsoft.com/azure/application-insights)遙測的支援，以您的容器。|
|是|[Billing](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|寫入記錄檔，並選擇性地 Fluentd 伺服器的計量資料。|
|否|Http Proxy|設定 HTTP proxy 來進行輸出要求。|
|否|[Logging](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[Mounts](#mount-settings)|讀取和寫入資料從主機電腦，以容器和容器上一步要在主機電腦。|
