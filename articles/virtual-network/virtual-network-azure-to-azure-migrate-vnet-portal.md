---
title: 使用 Azure 入口網站將 Azure 虛擬網路移至另一個 Azure 區域。
description: 使用 Azure Resource Manager 範本，使用 Azure 入口網站將 Azure 虛擬網路從一個 Azure 區域移至另一個區域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: d0f8a52eace9af4dc0865e1a2f277b9ab31c6858
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997415"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>使用 Azure 入口網站將 Azure 虛擬網路移至另一個區域

在許多情況下，您會想要將現有的 Azure 虛擬網路（Vnet）從一個區域移至另一個區域。 例如，您可能會想要使用相同的設定來建立虛擬網路，以進行現有虛擬網路的測試和可用性。 您也可能想要將生產虛擬網路移至另一個區域，做為嚴重損壞修復計畫的一部分。

您可以使用 Azure Resource Manager 範本來完成將虛擬網路移至另一個區域的工作。 若要這麼做，您可以將虛擬網路匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域。  如需 Resource Manager 和範本的詳細資訊，請[參閱快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>必要條件

- 請確定 Azure 虛擬網路位於您要移動的 Azure 區域中。

- 若要匯出虛擬網路並部署範本以在另一個區域中建立虛擬網路，您將需要網路參與者角色或更高版本。

- 虛擬網路對等互連將不會重新建立，如果它們仍然存在於範本中，將會失敗。  您必須先移除任何虛擬網路對等，然後再匯出範本，然後在移動虛擬網路之後重新建立對等。
    
- 識別來源網路配置，以及您目前使用的所有資源。 此配置包括但不限於負載平衡器、網路安全性群組（Nsg）和公用 Ip。

- 確認您的 Azure 訂用帳戶可讓您在使用的目的地區域中建立虛擬網路。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源，可支援在此程式中新增虛擬網路。  請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>準備和移動
下列步驟示範如何使用 Resource Manager 範本來準備要移動的虛擬網路，並使用入口網站將虛擬網路移至目的地區域。

### <a name="export-the-template-and-deploy-from-the-portal"></a>匯出範本並從入口網站部署

1. 登入[Azure 入口網站](http://portal.azure.com) > **資源群組**。
2. 找出包含來源虛擬網路的資源群組，然後按一下它。
3. 選取 >**設定** >  **匯出範本**。
4. 在 [**匯出範本**] 分頁中，選擇 [**部署**]。
5. 按一下 [**範本** > ] [**編輯參數**]，在線上編輯器中開啟**parameters json**檔案。
6. 若要編輯虛擬網路名稱的參數，請變更 [**參數**] 底下的 [**值**] 屬性：
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. 將編輯器中的 [來源虛擬網路名稱] 值變更為目標 VNET 的選擇名稱。 請務必以引號括住名稱。

8.  按一下編輯器中的 [**儲存**]。

9.  按一下 [**範本** > ] [**編輯範本**]，在線上編輯器中開啟**範本. json**檔案。 

10. 若要編輯將移動 VNET 的目的地區域，請在線上編輯器中變更 [**資源**] 底下的 [**位置**] 屬性：

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
 
11. 若要取得區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的程式碼是不含空格、**美國** = 中部**centralus**的區功能變數名稱稱。
 
12. 您也可以根據您的需求，變更範本中的其他參數，而且是選擇性的：

    * **位址空間**-您可以修改**資源** >  **addressSpace**區段並變更範本中的**addressPrefixes**屬性，以在儲存前先在範本中更改 VNET 的位址空間 **。** 檔案：
    
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

    * **子網**-修改**範本. json**檔案的**子**網區段，即可變更或新增子網名稱和子網位址空間。 您可以變更**範本. json**檔案中的**name**屬性，以變更子網的名稱。  藉由改變**範本. json**檔案中的**addressPrefix**屬性，即可變更子網位址空間：
    
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
                    ],
    ```

    在**範本. json**檔案中，若要變更位址首碼，您必須在兩個位置中編輯它，如上所列的區段和以下所列的**類型**區段。  變更**addressPrefix**屬性，使其符合上述各項：
                
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

13. 按一下線上編輯器中的 [**儲存**]。

14. 按一下 [**基本** > ] [**訂**用帳戶]，選擇將部署目標 VNET 的訂用帳戶。

15. 按一下 [**基本** > ] [**資源群組**]，選擇將部署目標 VNET 的資源群組。  您可以按一下 [**建立新**的]，為目標 VNET 建立新的資源群組。  請確定名稱與現有 VNET 的來源資源群組不同。 

16. 確認 [**基本** > ] [**位置**] 已設定為您想要部署 VNET 的目標位置。

17. 確認 [**設定**] 底下的 [名稱] 符合您在上面的 [參數編輯器] 中輸入的名稱。

18. 勾選 [**條款及條件**] 底下的方塊。

19. 按一下 [**購買**] 按鈕以部署目標虛擬網路。

## <a name="discard"></a>捨棄 

如果您想要捨棄目標虛擬網路，請刪除包含目標虛擬網路的資源群組。  若要這麼做，請從入口網站的儀表板中選取資源群組，然後選取 [總覽] 頁面頂端的 [**刪除**]。

## <a name="clean-up"></a>清除

若要認可變更並完成虛擬網路的移動，請刪除來源虛擬網路或資源群組。 若要這麼做，請在入口網站中從儀表板選取虛擬網路或資源群組，然後選取每個頁面頂端的 [**刪除**]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 虛擬網路從一個區域移至另一個區域，並清除來源資源。  若要深入瞭解如何在 Azure 中的區域和嚴重損壞修復之間移動資源，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
