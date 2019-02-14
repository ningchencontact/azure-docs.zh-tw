---
title: Azure 事件中樞防火牆規則 | Microsoft Docs
description: 使用「防火牆規則」以允許從特定 IP 位址連線至「Azure 事件中樞」。
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: ccb2fa7b0805b332957513c52c0c1051d068d2cc
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507530"
---
# <a name="use-firewall-rules"></a>使用防火牆規則

針對應該只能從特定已知網站存取「Azure 事件中樞」的情況，防火牆規則可讓您設定規則來接受源自特定 IPv4 位址的流量。 例如，這些位址可能是企業 NAT 閘道的位址。

## <a name="when-to-use"></a>使用時機

如果想要設定「事件中樞」命名空間，讓其應該只接收來自某個指定 IP 位址範圍的流量，並拒絕所有其他流量，您可以利用「防火牆規則」封鎖來自其他其他 IP 位址的事件中樞端點。 例如，如果您搭配 [Azure Express Route][express-route] 使用事件中樞，您可以建立*防火牆規則*限制來自您內部部署基礎結構 IP 位址的流量。

## <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選器規則會套用在事件中樞命名空間層級。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。

任何來自某個 IP 位址的連線嘗試，只要不符合「事件中樞」命名空間上的允許 IP 規則，系統就會將其視為未經授權而予以拒絕。 回應則不涉及 IP 規則。

## <a name="default-setting"></a>預設設定

根據預設，事件中樞入口網站中的 **IP 篩選器**方格是空的。 這個預設設定表示您的事件中樞會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。

## <a name="ip-filter-rule-evaluation"></a>IP 篩選器規則評估

IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

>[!WARNING]
> 實作「防火牆」可防止其他 Azure 服務與「事件中樞」進行互動。
>
> 實作「IP 篩選」(防火牆) 時，不支援受信任的 Microsoft 服務，但很快就會提供這項支援。
>
> 無法與「IP 篩選」搭配運作的常見 Azure 案例 (請注意，這**不是**完整的清單) -
> - Azure 監視器
> - Azure 串流分析
> - 與 Azure 事件方格的整合
> - Azure IoT 中樞路由
> - Azure IoT Device Explorer
> - Azure 資料總管
>
> 虛擬網路上必須有下列 Microsoft 服務
> - Azure Web Apps 
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>利用 Azure Resource Manager 範本來建立防火牆規則

> [!IMPORTANT]
> 「事件中樞」的**標準**和**專用**層級可支援防火牆規則。 基本層中不支援虛擬網路。

下列 Resource Manager 範本可讓您將 IP 篩選器規則新增至現有的事件中樞命名空間。

範本參數：

- **ipMask** 是單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 標記法中，70.37.104.0/24 表示從 70.37.104.0 開始，到 70.37.104.255 為止，總共 256 個 IPv4 位址，而 24 則表示該範圍內的顯著前置詞位元。

> [!NOTE]
> 雖然無法使用任何拒絕規則，但 Azure Resource Manager 範本是將預設動作設定為不會限制連線的 **"Allow"**。
> 在建立「虛擬網路」或「防火牆」規則時，我們必須將 ***"defaultAction"***
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> to
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

若要部署範本，請依照 [Azure Resource Manager][lnk-deploy] 適用的指示執行。

## <a name="next-steps"></a>後續步驟

若要將對事件中樞的存取權限制為 Azure 虛擬網路，請參閱下列連結：

- [事件中樞的虛擬網路服務端點][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
