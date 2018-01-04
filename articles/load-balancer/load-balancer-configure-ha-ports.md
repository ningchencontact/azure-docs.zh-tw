---
title: "設定 Azure Load Balancer 的高可用性連接埠| Microsoft Docs"
description: "了解如何使用高可用性連接埠進行負載平衡所有連接埠上的內部流量"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 36bc3d7a35f41384706cbc7101457d00848639b2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>設定內部負載平衡器的高可用性連接埠

本文章會提供內部負載平衡器上的高可用性連接埠的範例部署。 如需有關特定網路虛擬裝置 (NVAs) 組態的詳細資訊，請參閱對應的提供者網站。

>[!NOTE]
> 高可用性連接埠功能目前為預覽狀態。 在預覽期間，此功能可能沒有相同的層級的可用性和可靠性的一般可用性的功能版本。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

圖例中顯示本文中所述的部署範例下列的設定：

- NVAs 部署高可用性連接埠組態背後內部負載平衡器的後端集區中。 
- 使用者定義的路由 (UDR) 套用在周邊網路子網路路由的所有流量至 NVAs 所進行的下一個躍點為內部負載平衡器虛擬 IP。 
- 內部負載平衡器會發佈到其中一個使用中的 NVAs 根據負載平衡器演算法的流量。
- NVA 處理流量，並將它轉送至後端的子網路中的原始目的端。
- 如果在後端子網路上設定對應 UDR 傳回路徑可能需要使用相同的路由。 

![部署高可用性連接埠範例](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>註冊預覽

若要加入的標準 Azure 負載平衡器的高可用性連接埠功能預覽，註冊您的訂用帳戶，才能使用 Azure CLI 2.0 或 PowerShell 的存取。 註冊您的訂閱[標準負載平衡器預覽](https://aka.ms/lbpreview#preview-sign-up)。

>[!NOTE]
>註冊標準的負載平衡器預覽最多可能需要一小時的時間。

## <a name="configure-high-availability-ports"></a>設定高可用性連接埠

若要設定高可用性連接埠，設定 NVAs 內部負載平衡器後端集區中。 設定對應的負載平衡器健全狀況探查組態來偵測 NVA 健全狀況和負載平衡器規則，具有高可用性連接埠。 一般的負載平衡器相關組態在講述[開始](load-balancer-get-started-ilb-arm-portal.md)。 本文章重點說明高可用性連接埠組態。

組態基本上牽涉到設定的前端連接埠和後端連接埠值**0**。 將通訊協定值設定為**所有**。 本文說明如何使用 Azure 入口網站、 PowerShell 和 Azure CLI 2.0 設定高可用性連接埠。

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>使用 Azure 入口網站設定高可用性連接埠負載平衡器規則

若要使用 Azure 入口網站設定高可用性連接埠，請選取**HA 連接埠**核取方塊。 選取此選項時，系統會自動填入相關的連接埠和通訊協定組態。 

![透過 Azure 入口網站的高可用性連接埠組態](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>設定高可用性連接埠負載平衡規則透過 Resource Manager 範本

您可以在負載平衡器資源使用 Microsoft.Network/loadBalancers 2017-08-01 API 版本設定高可用性連接埠。 下列 JSON 片段會示範透過 REST API 的高可用性連接埠的負載平衡器組態中的變更：

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>使用 PowerShell 設定高可用性連接埠負載平衡器規則

若要建立高可用性連接埠的負載平衡器規則時使用 PowerShell 建立內部負載平衡器使用下列命令：

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>使用 Azure CLI 2.0 設定高可用性連接埠負載平衡器規則

在步驟 4 的[建立內部負載平衡器集](load-balancer-get-started-ilb-arm-cli.md)，使用下列命令來建立高可用性連接埠的負載平衡器規則：

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>後續步驟

深入了解[高可用性連接埠](load-balancer-ha-ports-overview.md)。
