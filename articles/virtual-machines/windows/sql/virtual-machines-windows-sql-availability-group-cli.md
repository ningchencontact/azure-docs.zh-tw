---
title: 使用 Azure SQL VM CLI 來設定 Azure VM 上的 SQL Server Always On 可用性群組
description: '您可以使用 Azure CLI 來建立在 Azure 中的 SQL Server VM 上的 Windows 容錯移轉叢集 」、 「 可用性群組接聽程式和 「 內部負載平衡器。 '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c5c5f4c8125f801edc89d47851871d8eb06a2f9
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762866"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure SQL VM CLI 來設定 Azure VM 上的 SQL Server Always On 可用性群組
這篇文章說明如何使用[Azure SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-latest/)部署在 Windows 容錯移轉叢集 (WSFC)，並將 SQL Server Vm 加入至叢集，以及建立內部負載平衡器和 Always On 可用性群組接聽程式。  Always On 可用性群組的實際的部署仍然是以手動方式透過 SQL Server Management Studio (SSMS)。 

## <a name="prerequisites"></a>必要條件
若要自動化的 Always On 可用性群組使用 Azure SQL VM CLI 安裝程式，您必須已經具備下列必要條件： 
- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 具有網域控制站的資源群組。 
- 一或多個網域[Azure 執行 SQL Server 2016 （或更新版本） Enterprise edition 中的 Vm](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)中*相同的可用性設定組或不同的可用性區域*已經經過[註冊與 SQL VM 的資源提供者](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 兩個可用 （未由任何實體） IP 位址、 一個內部負載平衡器，另一個可用性群組相同的子網路內的可用性群組接聽程式。 如果正在使用現有的負載平衡器，則只有一個可用的 IP 位址需要可用性群組接聽程式。 

## <a name="permissions"></a>權限
下列帳戶所需權限設定 Always On 可用性群組使用 Azure SQL VM CLI。 

- 現有的網域使用者帳戶網域中具有建立電腦物件的權限。  例如，網域系統管理員帳戶通常會有足夠的權限 (例如：account@domain.com)。 _此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。_
- 網域使用者帳戶控制 SQL Server 服務。 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>步驟 1-建立雲端見證與儲存體帳戶
叢集需要儲存體帳戶做為雲端見證。 您可以使用任何現有的儲存體帳戶，或您可以建立新的儲存體帳戶。 如果您想要使用現有的儲存體帳戶，請直接跳到下一節。 

下列程式碼片段會建立儲存體帳戶： 
```azurecli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > 您可能會看到錯誤`az sql: 'vm' is not in the 'az sql' command group`如果您使用 Azure CLI 的版本過時。 下載[最新版的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)即可解決這個錯誤。

## <a name="step-2---define-windows-failover-cluster-metadata"></a>步驟 2： 定義 Windows 容錯移轉叢集的中繼資料
Azure SQL VM CLI [az sql vm 群組](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest)群組管理的 Windows 容錯移轉叢集 (WSFC) 服務來裝載可用性群組的中繼資料的命令。 叢集中繼資料包含 AD 網域的叢集帳戶、 儲存體帳戶，以用來當做雲端見證和 SQL Server 版本。 使用[az sql vm 群組建立](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create)wsfc 定義之中繼資料，以便新增第一個 SQL Server VM 時，建立叢集時所定義。 

下列程式碼片段會定義叢集的中繼資料：
```azurecli
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3---add-sql-server-vms-to-cluster"></a>步驟 3-將 SQL Server Vm 新增至叢集
將第一個 SQL Server VM 加入至叢集中建立叢集。 [Az sql vm 新增對群組](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group)命令使用先前指定的名稱，建立叢集、 SQL Server Vm 上安裝的叢集角色，並將它們新增到叢集。 後續使用`az sql vm add-to-group`命令會將額外的 SQL Server Vm 加入至新建立的叢集。 

下列程式碼片段會建立叢集，並將第一個 SQL Server VM: 

```azurecli
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
若要將任何其他的 SQL Server Vm 加入至叢集，使用此命令，僅修改`-n`SQL Server VM 名稱的參數。 

## <a name="step-4---create-availability-group"></a>步驟 4-建立可用性群組
像平常一樣，使用手動建立可用性群組[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)， [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)，或[TRANSACT-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)。 

  >[!IMPORTANT]
  > 請勿**不**此時建立接聽程式，因為這是透過下列各節中的 Azure CLI。  

## <a name="step-5---create-internal-load-balancer"></a>步驟 5-建立內部負載平衡器

Always On 可用性群組 (AG) 接聽程式需要內部 Azure 負載平衡器 (ILB)。 ILB 會為 AG 接聽程式提供「浮動」IP 位址，以加快容錯移轉和重新連線的速度。 如果可用性群組中的 SQL Server VM 屬於相同的可用性設定組，則您可以使用基本的負載平衡器；否則就必須使用標準負載平衡器。  **ILB 應位於與 SQL Server VM 執行個體相同的 vNet 中。** 

下列程式碼片段會建立內部負載平衡器：

```azurecli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > 每個 SQL Server VM 的公用 IP 資源都應有標準 SKU，以便與標準負載平衡器相容。 若要確認 VM 公用 IP 資源的 SKU，請瀏覽至您的 [資源群組]，並為您所需的 SQL Server VM 選取 [公用 IP 位址] 資源，然後在 [概觀]窗格的 [SKU] 下方找出其值。  

## <a name="step-6---create-availability-group-listener"></a>步驟 6-建立可用性群組接聽程式
手動建立可用性群組之後, 您可以建立接聽程式使用[az sql vm ag 接聽程式](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)。 


- **子網路的資源識別碼**的值`/subnets/<subnetname>`附加至的 vNet 資源的資源識別碼。 若要識別的子網路的資源識別碼，執行下列作業：
   1. 瀏覽至您的資源群組中[Azure 入口網站](https://portal.azure.com)。 
   1. 選取 vNet 的資源。 
   1. 選取 **屬性**中**設定**窗格。 
   1. 找出之 vNet 的資源識別碼，並附加`/subnets/<subnetname>`至結尾，以便在建立子網路的資源識別碼。 例如︰
        - 我的 vNet 資源識別碼是： `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - 我的子網路名稱是`default`。
        - 因此，我的子網路的資源識別碼是： `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


下列程式碼片段會建立可用性群組接聽程式：

```azurecli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>修改可用性群組中的複本數目
沒有多一層的複雜性時部署到裝載於 Azure、 SQL Server Vm 的可用性群組，如資源現已管理資源提供者與`virtual machine group`。 因此，當新增或移除可用性群組的複本，是 SQL Server Vm 的相關資訊來更新接聽程式的中繼資料的額外的步驟中。 因此，當修改可用性群組中的複本數目，您也必須使用[az sql vm 群組 ag 接聽程式更新](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update)命令來更新接聽程式與 SQL Server Vm 的中繼資料。 


### <a name="add-a-replica"></a>新增複本

若要加入新的複本至可用性群組，執行下列作業：

1. 將 SQL Server VM 新增至叢集：
   ```azurecli
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. 使用 SQL Server Management Studio (SSMS) 將 SQL Server 執行個體為可用性群組內的複本。
1. 將 SQL Server VM 的中繼資料新增至接聽程式：
   ```azurecli
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>移除複本

若要從可用性群組移除複本，執行下列作業：

1. 從使用 SQL Server Management Studio (SSMS) 的可用性群組移除複本。 
1. 移除接聽程式的 SQL Server VM 中繼資料：
   ```azurecli
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 從叢集移除 SQL Server VM:
   ```azurecli
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>移除可用性群組接聽程式
如果您稍後要移除可用性群組接聽程式設定使用 Azure CLI，您必須瀏覽 SQL VM 的資源提供者。 由於接聽程式是透過 SQL VM 資源提供者註冊的，因為僅透過 SQL Server Management Studio 加以刪除，是不夠的。 實際上應該刪除使用 Azure CLI 的 SQL VM 資源提供者。 這麼做可從 SQL VM 資源提供者中移除 AG 接聽程式中繼資料，而真正將接聽程式從可用性群組中刪除。 

下列程式碼片段會同時從 SQL 資源提供者和您的可用性群組中刪除 SQL 可用性群組接聽程式： 

```azurecli
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [SQL Server VM 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切換 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)
* [Alwayson 可用性群組概觀&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [設定 Always On 可用性群組的伺服器執行個體&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [可用性群組的管理&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [監視可用性群組&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [針對 Always On 可用性群組的 TRANSACT-SQL 陳述式的概觀&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Always On 可用性群組的 PowerShell Cmdlet 概觀&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
