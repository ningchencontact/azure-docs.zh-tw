---
title: 使用 Azure CLI 設定 Azure ExpressRoute Global Reach | Microsoft Docs
description: 此文章可協助您將 ExpressRoute 線路連結在一起，在內部部署網路之間產生私人網路，並使觸角擴及全球。
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: cherylmc
ms.openlocfilehash: 9d41ab26876d464187466f566bbfafc4861c799d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333244"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>使用 Azure CLI 設定 ExpressRoute Global Reach (預覽)
此文章可協助您使用 Azure CLI 設定 ExpressRoute Global Reach。 如需詳細資訊，請參閱 [ExpressRouteRoute Global Reach](expressroute-global-reach.md)。
 
## <a name="before-you-begin"></a>開始之前
> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 


您必須先檢查下列需求，才能開始設定。

* 安裝最新版的 Azure CLI。 請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli) 和[開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。
* 了解 ExpressRoute 線路佈建[工作流程](expressroute-workflows.md)。
* 請確定 ExpressRoute 線路處於已佈建狀態。
* 請確定 ExpressRoute 線路上已設定 Azure 私用對等互連。  

### <a name="log-into-your-azure-account"></a>登入 Azure 帳戶
若要開始此設定，您必須登入 Azure 帳戶。 此命令將開啟您的預設瀏覽器，並提示您輸入 Azure 帳戶的登入認證。  

```azurecli
az login
```

如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶。

```azurecli
az account list
```

指定您要使用的訂用帳戶。

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>識別要設定的 ExpressRoute 線路
您可以在任兩個 ExpressRoute 線路之間啟用 ExpressRoute 觸角擴及全球，前提是它們位於支援的國家/地區，且建立在不同的對等互連位置。 如果兩個線路皆為訂用帳戶所擁有，則可以選擇使用任一線路來執行下列各節中的設定。 如果兩個線路位於不同的 Azure 訂用帳戶中，則需要其中一個 Azure 訂用帳戶的授權，並在您於另一個 Azure 訂用帳戶中執行設定命令時傳入授權金鑰。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>在內部部署網路之間啟用連線

執行下列 CLI 以連接兩條 ExpressRoute 線路。

> [!NOTE]
> *peer-circuit* 應該是完整的資源識別碼，例如
> ```
> */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}*
> ```
> 

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

> [!IMPORTANT]
> -AddressPrefix 必須是 /29 IPv4 子網路，例如"10.0.0.0/29"。 我們將使用此子網路中的 IP 位址在兩個 ExpressRoute 線路之間建立連線。 您不可在 Azure VNet 或內部部署網路中使用此子網路中的位址。
> 

CLI 輸出看起來如下。

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

上述作業完成後，您應該就可在內部部署網路的兩端，透過兩個 ExpressRoute 線路連線到另一端。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>在不同 Azure 訂用帳戶中的 ExpressRoute 線路

如果兩個線路不在相同的 Azure 訂用帳戶中，您將需要授權。 在下列設定中，線路 2 的訂用帳戶中會產生授權，且授權金鑰會傳遞至線路 1。

產生授權金鑰。 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

CLI 輸出看起來如下。

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

請記下線路 2 的資源識別碼以及授權金鑰。

針對線路 1 執行下列命令。 傳入線路 2 的資源識別碼和授權金鑰 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

上述作業完成後，您應該就可在內部部署網路的兩端，透過兩個 ExpressRoute 線路連線到另一端。

## <a name="get-and-verify-the-configuration"></a>取得及驗證設定

使用下列命令來驗證所設定線路上的設定 (即上述範例中的線路 1)。

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

在 CLI 輸出中，您將會看到 *CircuitConnectionStatus*。 它將會告訴您這兩個線路之間的連線已建立 (「已連線」) 還是未建立 (「已中斷連線」)。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>在內部部署網路之間停用連線

若要停用，請針對所設定的線路執行命令 (即上述範例中的線路 1)。

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

您可以執行顯示 CLI 來驗證狀態。 

在上述作業完成後，您就無法再透過 ExpressRoute 線路，於內部部署網路之間進行連線。 


## <a name="next-steps"></a>後續步驟
* [深入了解 ExpressRoute 觸角擴及全球](expressroute-global-reach.md)
* [驗證 ExpressRoute 連線](expressroute-troubleshooting-expressroute-overview.md)
* [將 ExpressRoute 線路連結到 Azure 虛擬網路](expressroute-howto-linkvnet-arm.md)


