---
title: 使用 Azure PowerShell 將 Azure 虛擬網路移至另一個 Azure 區域
description: 使用 Azure Resource Manager 範本，使用 Azure PowerShell 將 Azure 虛擬網路從一個 Azure 區域移至另一個區域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fe4c3fe131eb763ef8875cced91ab3ae22abca08
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077655"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>使用 Azure Powershell 將 Azure 虛擬網路移至另一個區域

在許多情況下，您會想要將現有的 Azure 虛擬網路（Vnet）從一個區域移至另一個區域。 例如，您可能會想要使用相同的設定來建立虛擬網路，以進行現有虛擬網路的測試和可用性。 您也可能想要將生產虛擬網路移至另一個區域，做為嚴重損壞修復計畫的一部分。

您可以使用 Azure Resource Manager 範本來完成將虛擬網路移至另一個區域的工作。 若要這麼做，您可以將虛擬網路匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域。  如需 Resource Manager 和範本的詳細資訊，請參閱[將資源群組匯出至範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>必要條件

- 請確定 Azure 虛擬網路位於您要移動的 Azure 區域中。

- 若要匯出虛擬網路並部署範本以在另一個區域中建立虛擬網路，您將需要網路參與者角色或更高版本。

- 虛擬網路對等互連將不會重新建立，如果它們仍然存在於範本中，將會失敗。  您必須先移除任何虛擬網路對等，然後再匯出範本，然後在移動虛擬網路之後重新建立對等。
    
- 識別來源網路配置，以及您目前使用的所有資源。 此配置包括但不限於負載平衡器、網路安全性群組（Nsg）和公用 Ip。

- 確認您的 Azure 訂用帳戶可讓您在使用的目的地區域中建立虛擬網路。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源，可支援在此程式中新增虛擬網路。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>準備和移動
下列步驟示範如何使用 Resource Manager 範本來準備用於移動的虛擬網路，以及使用 Azure PowerShell 命令將虛擬網路移至目的地區域。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>匯出虛擬網路並使用 PowerShell 部署目標虛擬網路

1. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入您的 Azure 訂用帳戶，並遵循畫面上的指示：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 取得您想要移至目的地區域之虛擬網路的資源識別碼，並使用[new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)將它放在變數中：

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將來源虛擬網路匯出至 json 檔案，以在您執行命令[匯出-remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下載的檔案將會以匯出資源的來源資源群組命名。  找出從名為 **\<[資源-群組-名稱 >** ] 的命令匯出的檔案，並在您選擇的編輯器中開啟它：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. 若要編輯虛擬網路名稱的參數，請將來源虛擬網路名稱的屬性**defaultValue**變更為目標虛擬網路的名稱，並確定名稱是以引號括住：
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  若要編輯將移動 VNET 的目的地區域，請變更 [資源] 底下的 [**位置**] 屬性：

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
7. 若要取得區域位置代碼，您可以藉由執行下列命令來使用 Azure PowerShell Cmdlet [get-azlocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  如果您選擇，您也可以變更 **\<資源群組名稱**中的其他參數 > json 檔案，而且是選擇性的，視您的需求而定：

    * **位址空間**-VNET 的位址空間可以藉由修改**resources**  >  **addressSpace**  **\<一節來變更，並變更中的 addressPrefixes 屬性。資源群組-名稱 > json**檔案：

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **子**網-您可以修改 **\<資源群組名稱 >. json**檔案的**子**網區段，來變更或新增子網名稱和子網位址空間。 您可以藉由變更**name**屬性來變更子網的名稱。 藉由改變 **\<資源群組名稱 >. json**檔案中的**addressPrefix**屬性，即可變更子網位址空間：

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         在**資源群組名稱 > json 檔案中，若要變更位址首碼，必須在兩個位置中進行編輯，如上所列的區段和下面所列的類型一節。 \<**  變更**addressPrefix**屬性，使其符合上述各項：

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

9.  將資源**群組名稱儲存>json檔案。\<**

10. 在目的地區域中建立資源群組，以使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目標 VNET
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 使用[new-azresourcegroupdeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)，將已編輯 **\<的資源群組名稱 >. json**檔案部署到在上一個步驟中建立的資源群組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要確認已在目的地區域中建立資源，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>捨棄 

部署之後，如果您想要開始或捨棄目標中的虛擬網路，請刪除在目標中建立的資源群組，將會刪除已移動的虛擬網路。  若要移除資源群組，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>清除

若要認可變更並完成虛擬網路的移動，請刪除來源虛擬網路或資源群組，使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[remove-new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 虛擬網路從一個區域移至另一個區域，並清除來源資源。  若要深入瞭解如何在 Azure 中的區域和嚴重損壞修復之間移動資源，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
