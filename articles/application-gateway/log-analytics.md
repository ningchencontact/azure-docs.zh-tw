---
title: 使用 Azure Log Analytics，以檢查應用程式閘道 Web 應用程式防火牆記錄檔
description: 本文說明如何使用 Azure Log Analytics 來檢查應用程式閘道 Web 應用程式防火牆記錄檔
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712172"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>使用 Log Analytics 來檢查應用程式閘道 Web 應用程式防火牆記錄檔

一旦您的應用程式閘道 WAF 可運作，您可以啟用記錄檔，以檢查每個要求發生什麼情況。 防火牆記錄檔提供深入解析，以何種 WAF 是評估、 比對和封鎖。 透過 Log Analytics，您可以檢查防火牆記錄檔，以提供更多深入資訊內的資料。 如需建立 Log Analytics 工作區的詳細資訊，請參閱 <<c0> [ 在 Azure 入口網站中建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。 如需有關記錄檔查詢的詳細資訊，請參閱[查詢 Azure 監視器中的記錄檔概觀](../azure-monitor/log-query/log-query-overview.md)。

## <a name="import-waf-logs"></a>匯入 WAF 記錄檔

若要在您的防火牆記錄檔匯入 Log Analytics，請參閱[後端健康情況、 診斷記錄和度量的應用程式閘道](application-gateway-diagnostics.md#diagnostic-logging)。 當您在您的 Log Analytics 工作區中有防火牆記錄檔時，您可以檢視資料、 撰寫查詢、 建立視覺效果，和將它們新增至入口網站儀表板。

## <a name="explore-data-with-examples"></a>探索範例資料

若要檢視防火牆記錄檔中的未經處理資料，您可以執行下列查詢：

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

這會看起來會類似下列的查詢：

![Log Analytics 查詢](media/log-analytics/log-query.png)

您可以向下鑽研資料，並繪製圖形或建立視覺效果，從這裡開始。 請參閱下列的查詢，以做為起點：

### <a name="matchedblocked-requests-by-ip"></a>比對/封鎖要求的 IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>比對/封鎖要求的 URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>最上層的相符的規則

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>前五個相符的規則群組

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>將新增至儀表板

一旦您建立查詢時，您可以將它加入您的儀表板。  選取 **釘選到儀表板**右上方的 log analytics 工作區。 與先前的四個查詢，釘選到儀表板範例，這是您可以一眼看到的資料：

![儀表板](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>後續步驟

[後端健康情況、 診斷記錄和應用程式閘道的度量資訊。](application-gateway-diagnostics.md)
