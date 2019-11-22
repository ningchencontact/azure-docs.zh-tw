---
title: 虛擬網路服務端點 - Azure 事件中樞 | Microsoft Docs
description: 本文提供有關如何將 Microsoft EventHub 服務端點新增至虛擬網路的資訊。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 5b02b79980ebe5ea91a1cf16d3ea453ebef3bf08
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279786"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>將虛擬網路服務端點搭配 Azure 事件中樞使用

事件中樞與[虛擬網路（VNet）服務端點][vnet-sep]的整合，可讓您安全地從工作負載（例如系結至虛擬網路的虛擬機器）存取訊息功能，同時保護兩者的網路流量路徑結束.

一旦設定為系結到至少一個虛擬網路子網服務端點，個別的事件中樞命名空間就不會再接受來自虛擬網路中任何來自于授權子網的流量。 從虛擬網路的角度來看，將事件中樞命名空間繫結至服務端點，會設定從虛擬網路子網路到傳訊服務的隔離式網路通道。 

最終這會在繫結至子網路的工作負載與各自的事件中樞命名空間之間，建立私人且隔離的關係，儘管傳訊服務端點顯示的網路位址位於公用 IP 範圍中。 這種行為有例外。 啟用服務端點時，根據預設，會啟用與虛擬網路相關聯之 IP 防火牆中的 denyall 規則。 您可以在 IP 防火牆中新增特定的 IP 位址，以啟用事件中樞公用端點的存取權。 


>[!WARNING]
> 實作「虛擬網路」整合可防止其他 Azure 服務與「事件中樞」進行互動。
>
> 實作「虛擬網路」時，不支援受信任的 Microsoft 服務。
>
> 無法與「虛擬網路」搭配運作的常見 Azure 案例 (請注意，這**不是**完整的清單) -
> - Azure 串流分析
> - 與 Azure 事件方格的整合
> - Azure IoT 中樞路由
> - Azure IoT Device Explorer
>
> 虛擬網路上必須有下列 Microsoft 服務
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> 事件中樞的**標準**和**專用**層級支援虛擬網路。 基本層中不支援虛擬網路。

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 整合所實現的進階安全性案例 

需要嚴格且劃分的安全性，以及虛擬網路子網在劃分服務之間提供分割的解決方案，仍然需要位於這些區間之服務之間的通訊路徑。

區間之間的任何立即 IP 路由，包括那些運輸 HTTPS over TCP/IP，會帶來上層網路的弱點危害風險。 傳訊服務提供完全隔離的通訊路徑，在該路徑中，若通訊雙方之間轉換訊息，甚至會將訊息寫入到磁碟。 兩個都繫結到相同事件中樞執行個體的不同虛擬網路，當中的工作負載可以透過訊息有效且可靠地通訊，並且保留各自的網路隔離界限完整性。
 
這表示您的安全性敏感雲端解決方案，不只獲得 Azure 可靠、可擴充的領先業界非同步傳訊功能的存取權，而且它們現在可以使用傳訊來建立安全解決方案區間之間的通訊路徑，這些區間本質上比任何對等通訊模式 (包括 HTTPS 和其他 TLS 保護的通訊端通訊協定) 更為安全。

## <a name="bind-event-hubs-to-virtual-networks"></a>將事件中樞繫結至虛擬網路

「虛擬網路規則」是防火牆安全性功能，可控制 Azure 事件中樞命名空間是否接受來自特定虛擬網路子網路的連線。

將事件中樞命名空間繫結至虛擬網路是一個雙步驟程序。 您必須先在虛擬網路子網上建立**虛擬網路服務端點**，並將其啟用為 "Microsoft EventHub"，如[服務端點總覽][vnet-sep]中所述。 一旦您新增服務端點，便會使用*虛擬網路規則*將事件中樞命名空間與其繫結。

虛擬網路規則是「事件中樞」命名空間與虛擬網路子網路的關聯。 此規則存在時，繫結至該子網路的所有工作負載都會獲得事件中樞命名空間的存取權。 事件中樞本身永遠不會建立輸出連線，不需要獲得存取權，因此永遠不會因為啟用這項規則而獲得您子網路的存取權。

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>利用 Azure Resource Manager 範本來建立虛擬網路規則

下列 Resource Manager 範本可讓您將虛擬網路規則新增至現有的事件中樞命名空間。

範本參數：

* **namespaceName**：事件中樞命名空間。
* **vnetRuleName**：要建立之虛擬網路規則的名稱。
* **virtualNetworkingSubnetId**：虛擬網路子網路的完整 Resource Manager 路徑，例如，`/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` 適用於虛擬網路的預設子網路。

> [!NOTE]
> 雖然無法使用任何拒絕規則，但 Azure Resource Manager 範本是將預設動作設定為不會限制連線的 **"Allow"** 。
> 在建立「虛擬網路」或「防火牆」規則時，我們必須將 ***"defaultAction"***
> 
> 從
> ```json
> "defaultAction": "Allow"
> ```
> 收件人
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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

若要部署範本，請遵循[Azure Resource Manager][lnk-deploy]的指示。

## <a name="next-steps"></a>後續步驟

如需虛擬網路的詳細資訊，請參閱下列連結：

- [Azure 虛擬網路服務端點][vnet-sep]
- [Azure 事件中樞 IP 篩選][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
