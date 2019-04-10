---
title: 監視和記錄的 azure web 應用程式防火牆
description: 了解透過前端監視和記錄 web 應用程式防火牆 (WAF)
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362974"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>監視和記錄的 azure web 應用程式防火牆 

Azure web 應用程式防火牆 (WAF) 監視和記錄是透過提供記錄並與 Azure 監視器 」 和 「 Azure 監視器整合記錄檔。

> [!IMPORTANT]
> WAF 監視和記錄功能的 Azure 大門目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="azure-monitor"></a>Azure 監視器

與整合，透過前端記錄檔的 WAF [Azure 監視器](../azure-monitor/overview.md)。 Azure 監視器可讓您追蹤包括 WAF 警示和記錄的診斷資訊。 您可以設定 WAF 監視下的入口網站中的大門資源內**診斷**索引標籤上，或直接透過 Azure 監視器服務。

從 Azure 入口網站中，移至大門資源類型。 從**監視**/**計量**索引標籤左側，您可以新增**WebApplicationFirewallRequestCount**追蹤符合 WAF 規則的要求數目。 自訂篩選器可以根據動作類型和規則的名稱建立。

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>記錄檔和診斷

前門使用 WAF 會提供詳細的報告，每個偵測到的威脅。 記錄會與 Azure 診斷記錄整合，而且警示會以 JSON 格式來記錄。 這些記錄可以與 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)整合。

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog 會記錄所有要求轉送至客戶後端。 FrontdoorWebApplicationFirewallLog 記錄符合 WAF 規則的任何要求。

下列範例查詢會取得已封鎖的要求上的 WAF 記錄檔：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

下列範例查詢會取得 AccessLogs 項目：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>後續步驟

- 深入了解[大門](front-door-overview.md)。

