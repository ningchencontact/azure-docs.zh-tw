---
title: 針對 Azure Cosmos DB 帳戶設定虛擬網路和子網路型存取
description: 本文件將說明設定 Azure Cosmos DB 虛擬網路服務端點所需的步驟。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 37cc7dcb3157459f6f6387323cc44d6f97954e92
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035809"
---
# <a name="access-azure-cosmos-db-resources-from-virtual-networks"></a>從虛擬網路存取 Azure Cosmos DB 資源

您可以設定 Azure Cosmos DB 帳戶，只允許從 Azure 虛擬網路的特定子網路存取。 若要限制只能從虛擬網路中的子網路進行連線來存取 Azure Cosmos DB 帳戶：
 
1. 使子網路能夠將子網路和虛擬網路身分識別傳送至 Azure Cosmos DB。 為此，您可以在特定子網路上啟用 Azure Cosmos DB 的服務端點。

1. 在 Azure Cosmos DB 帳戶中新增規則，將子網路指定為可存取帳戶的來源。

> [!NOTE]
> 在子網路上啟用 Azure Cosmos DB 帳戶的服務端點時，送到 Azure Cosmos DB 的流量來源就會從公用 IP 切換為虛擬網路和子網路。 對於任何要從這個子網路存取的 Azure Cosmos DB 帳戶，都會套用流量切換。 如果您的 Azure Cosmos DB 帳戶有允許此子網路的 IP 型防火牆，已啟用服務的子網路所發出的要求將不再符合 IP 防火牆規則，而會被拒絕。 
>
> 若要深入了解，請參閱本文的[從 IP 防火牆規則移轉至虛擬網路存取控制清單](#migrate-from-firewall-to-vnet)一節所說明的步驟。 

以下幾節將說明如何設定 Azure Cosmos DB 帳戶的虛擬網路服務端點。

## <a id="configure-using-portal"></a>使用 Azure 入口網站來設定服務端點

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>對現有的 Azure 虛擬網路和子網路設定服務端點

1. 從 [所有資源] 刀鋒視窗中，尋找要保護的 Azure Cosmos DB 帳戶。

1. 從設定功能表中選取 [防火牆與虛擬網路]，並選擇允許從 [選取的網路] 進行存取。

1. 若要將存取權授與現有虛擬網路的子網路，請在 [虛擬網路] 下選取 [新增現有 Azure 虛擬網路]。

1. 選取您要從中新增 Azure 虛擬網路的**訂用帳戶**。 選取要提供 Azure Cosmos DB 帳戶存取權的 Azure **虛擬網路**和**子網路**。 接下來，選取 [啟用] 以啟用具有 "Microsoft.AzureCosmosDB" 服務端點的選定網路。 完成時，請選取 [新增]。 

   ![選取虛擬網路和子網路](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. 在 Azure Cosmos DB 帳戶啟用從虛擬網路的存取後，它將只會允許來自這個選定子網路的流量。 您所新增的虛擬網路和子網路應會如下列螢幕擷取畫面所示：

   ![已成功設定虛擬網路和子網路](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 若要啟用虛擬網路服務端點，您必須具備下列訂用帳戶權限：
  * 虛擬網路的訂用帳戶：網路參與者
  * Azure Cosmos DB 帳戶的訂用帳戶：DocumentDB 帳戶參與者

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>對新的 Azure 虛擬網路和子網路設定服務端點

1. 從 [所有資源] 刀鋒視窗中，尋找要保護的 Azure Cosmos DB 帳戶。  

1. 從設定功能表中選取 [防火牆與 Azure 虛擬網路]，並選擇允許從 [選取的網路] 進行存取。  

1. 若要授與新 Azure 虛擬網路的存取權，請在 [虛擬網路] 下選取 [新增虛擬網路]。  

1. 提供建立新虛擬網路所需的詳細資料，然後選取 [建立]。 這會建立已啟用 "Microsoft.AzureCosmosDB" 服務端點的子網路。

   ![選取虛擬網路與新虛擬網路的子網路](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

如果您的 Azure Cosmos DB 帳戶會由其他 Azure 服務 (例如 Azure 搜尋服務) 使用，或是會從串流分析或 Power BI 進行存取，則您可以選取 [接受來自公用 Azure 資料中心內的連線] 來允許存取。

若要確保您可以從入口網站存取 Azure Cosmos DB 計量，您必須啟用 [允許從 Azure 入口網站存取] 選項。 若要深入了解這些選項，請參閱[設定 IP 防火牆](how-to-configure-firewall.md)一文。 啟用存取之後，請選取 [儲存] 以儲存設定。

## <a id="remove-vnet-or-subnet"></a>移除現有虛擬網路或子網路 

1. 從 [所有資源] 刀鋒視窗中，尋找您要對其指派端點的 Azure Cosmos DB 帳戶。  

2. 從設定功能表中選取 [防火牆與虛擬網路]。  

3. 若要移除虛擬網路或子網路規則，請選取該虛擬網路或子網路旁的 […]，然後選取 [移除]。

   ![移除虛擬網路](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  選取 [儲存] 以套用變更。

## <a id="configure-using-powershell"></a>使用 Azure PowerShell 來設定服務端點

> [!NOTE]
> 當您使用 PowerShell 或 Azure CLI 時，請務必在參數中指定 IP 篩選器和虛擬網路 ACL 的完整清單，而不是只指定必須新增的項目。

若要使用 Azure PowerShell 對 Azure Cosmos DB 帳戶設定服務端點，請使用下列步驟：  

1. 安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 並[登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。  

1. 對現有的虛擬網路子網路啟用服務端點。  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. 取得虛擬網路資訊。

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. 請執行下列 Cmdlet 以取得 Azure Cosmos DB 帳戶的屬性：  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 初始化變數以供稍後使用。 從現有的帳戶定義設定所有變數。

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. 若要使用新組態來更新 Azure Cosmos DB 帳戶屬性，請執行下列 Cmdlet： 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. 若要確認 Azure Cosmos DB 帳戶已透過上一個步驟中設定的虛擬網路服務端點進行更新，請執行下列命令：

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>使用 Azure CLI 來設定服務端點 

1. 為虛擬網路中的子網路啟用服務端點。

1. 以子網路存取控制清單 (ACL) 更新現有的 Azure Cosmos DB 帳戶。

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. 以子網路 ACL 建立新的 Azure Cosmos DB 帳戶。

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>從 IP 防火牆規則移轉至虛擬網路 ACL 

只有在 Azure Cosmos DB 帳戶有允許子網路的現有 IP 防火牆規則，且您想要使用虛擬網路和子網路型 ACL 來取代 IP 防火牆規則時，才使用下列步驟。

為子網路開啟 Azure Cosmos DB 帳戶的服務端點後，就會從包含虛擬網路和子網路資訊的來源傳送要求，而不是公用 IP。 這類要求不符合 IP 篩選器。 從已啟用服務端點的子網路存取的所有 Azure Cosmos DB 帳戶，都會發生此來源切換。 若要防止停止運作的狀況，請使用下列步驟：

1. 請執行下列 Cmdlet 以取得 Azure Cosmos DB 帳戶的屬性：

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 初始化變數以供稍後使用。 從現有的帳戶定義設定所有變數。 使用 `ignoreMissingVNetServiceEndpoint` 旗標，將虛擬網路 ACL 新增至所有要從子網路存取的 Azure Cosmos DB 帳戶。

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. 若要使用新組態來更新 Azure Cosmos DB 帳戶屬性，請執行下列 Cmdlet：

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. 對您從子網路存取的所有 Azure Cosmos DB 帳戶重複步驟 1-3。

1.  等候 15 分鐘，然後更新子網路以啟用服務端點。

1.  對現有的虛擬網路子網路啟用服務端點。

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzureRmVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
    ```

1. 移除子網路的 IP 防火牆規則。

## <a name="next-steps"></a>後續步驟

* 若要設定 Azure Cosmos DB 的防火牆，請參閱[防火牆支援](firewall-support.md)一文。
