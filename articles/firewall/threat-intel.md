---
title: Azure 防火牆威脅情報型篩選
description: 深入了解 Azure 防火牆威脅智慧篩選
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194026"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Azure 防火牆威脅情報為基礎篩選-公開預覽

您可為防火牆啟用威脅情報型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence)提供 Microsoft 威脅情報，並由多個服務，包括 Azure 資訊安全中心。

![防火牆威脅情報](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> 威脅情報型篩選目前處於公開預覽狀態，並提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。  如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果已啟用 威脅情報為基礎的篩選，相關聯的規則處理任何的 NAT 規則、 網路規則或應用程式規則之前。 在預覽期間，只有最高信賴度記錄則會包含項目。

您可以選擇只是在觸發規則時，或您可以選擇警示，並拒絕模式時記錄警示。

根據預設，在警示的模式下啟用威脅情報為基礎的篩選。 您無法關閉此功能，或變更的模式，在您的區域，入口網站介面可供使用。

![威脅情報基礎篩選的入口網站介面](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>記錄

下列記錄檔摘錄顯示觸發的規則：

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>測試

- **輸出測試**-輸出流量警示應該很罕見，，因為這表示您的環境已遭入侵。 為了測試輸出警示使用，會觸發警示的測試已建立 FQDN。 使用**testmaliciousdomain.eastus.cloudapp.azure.com**輸出測試。

- **輸入測試**-您可以預期看到警示上的連入流量，如果 DNAT 規則設定在防火牆上。 這是 true，即使 DNAT 規則允許特定的來源，否則拒絕流量。 對所有已知的連接埠掃描器; azure 防火牆不會發出警示只有在掃描器上，且已知也中從事惡意行為。

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 防火牆記錄檔分析範例](log-analytics-samples.md)
- 了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)
- 檢閱[Microsoft 安全性情報報告](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)