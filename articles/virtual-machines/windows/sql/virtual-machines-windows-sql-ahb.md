---
title: 如何在 Azure 中變更 SQL VM 的授權模型 | Microsoft Docs
description: 了解如何在 Azure 中切換 SQL 虛擬機器的授權。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3bcbfc876ed27d16180ca03801f2054ad804e148
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52576982"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-virtual-machine-in-azure"></a>如何在 Azure 中變更 SQL 虛擬機器的授權模型
本文說明如何在 Azure 中使用新的 SQL 資源提供者 (**Microsoft.SqlVirtualMachine**) 變更 SQL Server 虛擬機器的授權模型。 裝載 SQL Server 的虛擬機器 (VM) 有兩個授權模型 - 依使用量付費和自備授權 (BYOL)。 而現在，只要使用 PowerShell 或 Azure CLI，您就可以修改 SQL VM 所使用的授權模型。 

**依使用量付費**模型意謂著執行 Azure VM 的每秒鐘費用都包含 SQL Server 授權的費用。

**自備授權**模型也稱為 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，其可讓您對執行 SQL Server 的 VM 使用您自己的 SQL Server 授權。 如需價格的詳細資訊，請參閱 [SQL VM 定價指南](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)。

在兩個授權模型之間切換時，**不會產生停機時間**、不會重新啟動 VM、**無須支付額外費用** (事實上，啟動 AHB 會降低成本)，而且**立即生效**。 


## <a name="register-legacy-sql-vm-with-new-resource-provider"></a>向新的資源提供者註冊舊版 SQL VM
在授權模型之間切換的功能是新的 SQL VM 資源提供者 (Microsoft.SqlVirtualMachine) 所提供的一項功能。 在這個階段中，若要能夠切換您的授權模型，首先必須對訂用帳戶註冊這個新的提供者，然後向新的 SQL VM 資源提供者註冊舊版 VM。 

  >[!IMPORTANT]
  > 如果您卸除 SQL VM 資源，將會回到映像的硬式編碼授權設定。 

### <a name="powershell"></a>Powershell

下列程式碼會先向您的訂用帳戶註冊新的 SQL 資源提供者，然後向新的資源提供者註冊舊版的 SQL VM。 

```powershell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>

# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your legacy SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>入口網站
您也可以使用入口網站註冊新的 SQL VM 資源提供者。 若要這樣做，請遵循下列步驟：
1. 開啟 Azure 入口網站並瀏覽至 [所有服務]。 
1. 瀏覽至 [訂用帳戶] 然後選取感興趣的訂用帳戶。  

  ![選取您的訂用帳戶](media/virtual-machines-windows-sql-ahb/open-subscriptions.png)

1. 在 [訂用帳戶] 刀鋒視窗中，瀏覽至 [資源提供者]。 
1. 在篩選條件中輸入 `sql` 以顯示 SQL 相關的資源提供者。 
1. 取決於您所需的動作，針對 **Microsoft.SqlVirtualMachine** 提供者選取 [註冊]、[重新註冊] 或 [取消註冊]。 

  ![修改提供者](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>使用 PowerShell 
您可以使用 PowerShell 來變更您的授權模型。  在切換授權模型之前，請確定您的 SQL VM 已向新的 SQL 資源提供者註冊。 

此程式碼片段會將您依使用量付費的授權模型切換為 BYOL (或使用 Azure Hybrid Benefit)： 
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

此程式碼片段會將您的 BYOL 模型切換成依使用量付費：
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > 若要切換授權，您必須使用新的 SQL VM 資源提供者。 如果您在向新的提供者註冊您的 SQL VM 之前就嘗試執行這些命令，您可能會遇到這個錯誤：`Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` 如果您看到此錯誤，請[向新的資源提供者註冊您的 SQL VM](#register-legacy-SQL-vm-with-new-resource-provider)。 
 

## <a name="use-azure-cli"></a>使用 Azure CLI
您可以使用 Azure CLI 來變更您的授權模型。  在切換授權模型之前，請確定您的 SQL VM 已向新的 SQL 資源提供者註冊。 

此程式碼片段會將您依使用量付費的授權模型切換為 BYOL (或使用 Azure Hybrid Benefit)：
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

此程式碼片段會將您的 BYOL 模型切換成依使用量付費： 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >若要切換授權，您必須使用新的 SQL VM 資源提供者。 如果您在向新的提供者註冊您的 SQL VM 之前就嘗試執行這些命令，您可能會遇到這個錯誤：`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` 如果您看到此錯誤，請[向新的資源提供者註冊您的 SQL VM](#register-legacy-SQL-vm-with-new-resource-provider)。 

## <a name="view-current-licensing"></a>檢視目前的授權 

下列程式碼片段可讓您檢視您 SQL VM 目前的授權模型。 

```powershell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


