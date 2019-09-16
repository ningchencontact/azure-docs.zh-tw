---
title: '停用 Azure 私人連結服務來源 IP 位址的網路原則 '
description: 瞭解如何停用 Azure 私人連結的網路原則
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: c7a0968d32418867d3e929786a42da9349806b2d
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018928"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>停用私人連結服務來源 IP 的網路原則

若要為您的私用連結服務選擇來源 IP 位址，子網需要明確`privateLinkServiceNetworkPolicies`的停用設定。 此設定僅適用于您選擇做為私人連結服務之來源 IP 的特定私人 IP 位址。 若為子網中的其他資源，則會根據網路安全性群組（NSG）安全性規則定義來控制存取。 
 
使用任何 Azure 用戶端（PowerShell、CLI 或範本）時，需要額外的步驟來變更此屬性。 您可以從 Azure 入口網站使用 cloud shell，或 Azure PowerShell、Azure CLI 的本機安裝，或使用 Azure Resource Manager 範本來停用原則。  
 
請遵循下列步驟，針對名為*myVirtualNetwork*的虛擬網路停用私人連結服務網路原則，並在名為*myResourceGroup*的資源群組中託管*預設*子網。 

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
本節說明如何使用 Azure PowerShell 停用子網私人端點原則。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>使用 Azure CLI
本節說明如何使用 Azure CLI 停用子網私人端點原則。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>使用範本
本節說明如何使用 Azure Resource Manager 範本停用子網私人端點原則。
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>後續步驟
- 深入瞭解[Azure 私用端點](private-endpoint-overview.md)
 
