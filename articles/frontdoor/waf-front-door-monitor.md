---
title: Azure web 應用程式防火牆監視和記錄
description: 瞭解具有 FrontDoor 監視和記錄功能的 web 應用程式防火牆 (WAF)
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 675d06f3d2071022da3867a4c45137efb818980d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849132"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure web 應用程式防火牆監視和記錄 

Azure web 應用程式防火牆 (WAF) 監視和記錄是透過記錄和與 Azure 監視器和 Azure 監視器記錄檔的整合提供。

## <a name="azure-monitor"></a>Azure 監視器

具有 FrontDoor 記錄的 WAF 會與[Azure 監視器](../azure-monitor/overview.md)整合。 Azure 監視器可讓您追蹤診斷資訊, 包括 WAF 警示和記錄。 您可以在入口網站的 [**診斷**] 索引標籤底下, 或直接透過 Azure 監視器服務, 設定 WAF 監視。

從 Azure 入口網站, 移至 [Front 門板] [資源類型]。 從左側的 [**監視**/**計量**] 索引標籤中, 您可以新增**WebApplicationFirewallRequestCount**以追蹤符合 WAF 規則的要求數目。 您可以根據動作類型和規則名稱來建立自訂篩選器。

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>記錄和診斷

WAF 與 Front 門會針對它偵測到的每個威脅提供詳細的報告。 記錄會與 Azure 診斷記錄整合，而且警示會以 JSON 格式來記錄。 這些記錄可以與 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)整合。

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog 會記錄轉送至客戶後端的所有要求。 FrontdoorWebApplicationFirewallLog 會記錄符合 WAF 規則的任何要求。

下列範例查詢會取得已封鎖要求的 WAF 記錄:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

下列範例查詢會取得 AccessLogs 專案:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Front](front-door-overview.md)。

