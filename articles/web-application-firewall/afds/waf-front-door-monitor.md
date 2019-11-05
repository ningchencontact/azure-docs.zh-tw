---
title: Azure Web 應用程式防火牆監視和記錄
description: 瞭解具有 FrontDoor 監視和記錄功能的 Web 應用程式防火牆（WAF）
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512479"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web 應用程式防火牆監視和記錄 

Azure Web 應用程式防火牆（WAF）監視和記錄是透過記錄和與 Azure 監視器和 Azure 監視器記錄檔的整合提供。

## <a name="azure-monitor"></a>Azure 監視器

具有 FrontDoor 記錄的 WAF 會與[Azure 監視器](../../azure-monitor/overview.md)整合。 Azure 監視器可讓您追蹤診斷資訊，包括 WAF 警示和記錄。 您可以在入口網站的 [**診斷**] 索引標籤底下，或直接透過 Azure 監視器服務，設定 WAF 監視。

從 Azure 入口網站，移至 [Front 門板] [資源類型]。 從左側的 [**監視**]/[**計量**] 索引標籤中，您可以新增**WebApplicationFirewallRequestCount**來追蹤符合 WAF 規則的要求數目。 您可以根據動作類型和規則名稱來建立自訂篩選器。

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>記錄和診斷

WAF 與 Front 門會針對它偵測到的每個威脅提供詳細的報告。 記錄會與 Azure 診斷記錄整合，而且警示會以 JSON 格式來記錄。 這些記錄可以與 [Azure 監視器記錄](../../azure-monitor/insights/azure-networking-analytics.md)整合。

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog 會記錄轉送至客戶後端的所有要求。 FrontdoorWebApplicationFirewallLog 會記錄符合 WAF 規則的任何要求。

下列範例查詢會取得已封鎖要求的 WAF 記錄：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

下列範例查詢會取得 AccessLogs 專案：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Front](../../frontdoor/front-door-overview.md)。

