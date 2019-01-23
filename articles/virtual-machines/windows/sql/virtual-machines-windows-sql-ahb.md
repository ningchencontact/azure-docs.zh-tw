---
title: 如何在 Azure 中變更 SQL Server VM 的授權模型 | Microsoft Docs
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
ms.openlocfilehash: 1b1c7192eb8389d3ad3a1c7c935d9c7e2d8769a9
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359913"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>如何在 Azure 中變更 SQL Server 虛擬機器的授權模型
本文說明如何在 Azure 中使用新的 SQL VM 資源提供者 **Microsoft.SqlVirtualMachine**，來變更 SQL Server 虛擬機器的授權模型。 裝載 SQL Server 的虛擬機器 (VM) 有兩個授權模型 - 依使用量付費和自備授權 (BYOL)。 現在，您可以使用 PowerShell 或 Azure CLI 來修改 SQL Server VM 所使用的授權模型。 

**依使用量付費**模型意謂著執行 Azure VM 的每秒鐘費用都包含 SQL Server 授權的費用。

**自備授權**模型也稱為 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，其可讓您對執行 SQL Server 的 VM 使用您自己的 SQL Server 授權。 如需價格的詳細資訊，請參閱 [SQL Server VM 定價指南](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)。

在兩個授權模型之間切換時，**不會產生停機時間**、不會重新啟動 VM、**無須支付額外費用** (事實上，啟動 AHB 會「降低」成本)，而且**立即生效**。 

## <a name="prerequisites"></a>必要條件
使用 SQL VM 資源提供者需要 SQL IaaS 擴充。 因此，若要繼續運用 SQL VM 資源提供者，您需要下列項目：
- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 已安裝 [SQL IaaS 擴充](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)的 [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 


## <a name="register-existing-sql-server-vm-with-new-resource-provider"></a>向新的資源提供者註冊現有的 SQL Server VM
在授權模型之間切換的功能是新的 SQL VM 資源提供者 (Microsoft.SqlVirtualMachine) 所提供的一項功能。 在 2018 年 12 月之後部署的 SQL Server VM 都會自動向新的資源提供者註冊。 不過，在此日期之前部署的現有 VM 都必須手動向資源提供者註冊，才能切換其授權模型。 




  > 如果您卸除 SQL VM 資源，將會回到映像的硬式編碼授權設定。 


### <a name="powershell"></a>PowerShell

下列程式碼片段會將您連線到 Azure，並確認您使用哪一個訂用帳戶識別碼。 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

下列程式碼片段會先為您的訂用帳戶註冊新的 SQL 資源提供者，然後向新的資源提供者註冊您現有的 SQL Server VM。 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine


# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>入口網站
您也可以使用入口網站註冊新的 SQL VM 資源提供者。 若要這樣做，請遵循下列步驟：
1. 開啟 Azure 入口網站並瀏覽至 [所有服務]。 
1. 瀏覽至 [訂用帳戶] 然後選取感興趣的訂用帳戶。  
1. 在 [訂用帳戶] 刀鋒視窗中，瀏覽至 [資源提供者]。 
1. 在篩選條件中輸入 `sql` 以顯示 SQL 相關的資源提供者。 
1. 取決於您所需的動作，針對 **Microsoft.SqlVirtualMachine** 提供者選取 [註冊]、[重新註冊] 或 [取消註冊]。 

  ![修改提供者](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>使用 PowerShell 
您可以使用 PowerShell 來變更您的授權模型。  在切換授權模型之前，請確定您的 SQL Server VM 已向新的 SQL 資源提供者註冊。 

以下程式碼片段會將您依使用量付費的授權模型切換為 BYOL (或使用 Azure Hybrid Benefit)： 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
``` 

以下程式碼片段會將您的 BYOL 模型切換成依使用量付費：
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > 若要切換授權，您必須使用新的 SQL VM 資源提供者。 如果您在向新的提供者註冊您的 SQL Server VM 之前就嘗試執行這些命令，可能會遇到這個錯誤：`Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` 如果您看到此錯誤，請[向新的資源提供者註冊您的 SQL Server VM](#register-existing-SQL-vm-with-new-resource-provider)。 
 

## <a name="use-azure-cli"></a>使用 Azure CLI
您可以使用 Azure CLI 來變更您的授權模型。  在切換授權模型之前，請確定您的 SQL Server VM 已向新的 SQL 資源提供者註冊。 

以下程式碼片段會將您依使用量付費的授權模型切換為 BYOL (或使用 Azure Hybrid Benefit)：
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

以下程式碼片段會將您的 BYOL 模型切換成依使用量付費： 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >若要切換授權，您必須使用新的 SQL VM 資源提供者。 如果您在向新的提供者註冊您的 SQL Server VM 之前就嘗試執行這些命令，可能會遇到這個錯誤：`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` 如果您看到此錯誤，請[向新的資源提供者註冊您的 SQL Server VM](#register-existing-SQL-vm-with-new-resource-provider)。 

## <a name="view-current-licensing"></a>檢視目前的授權 

下列程式碼片段可讓您檢視您 SQL Server VM 目前的授權模型。 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>已知錯誤

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>虛擬機器上未安裝 SQL IaaS 擴充
SQL IaaS 擴充是向 SQL VM 資源提供者註冊 SQL Server VM 的必要先決條件。 如果您在安裝 SQL IaaS 擴充之前嘗試註冊 SQL Server VM，將會遇到下列錯誤：

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

若要解決此問題，請在嘗試註冊 SQL Server VM 之前安裝 SQL IaaS 擴充。 

  > [!NOTE]
  > 安裝 SQL IaaS 擴充將會重新啟動 SQL Server 服務，因此僅應在維護期間進行。 如需詳細資訊，請參閱 [SQL IaaS 擴充安裝](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation)。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>無法驗證參數 'Sku' 上的引數
您可能會在使用版本大於 4.0 的 Azure PowerShell 變更 SQL Server VM 的授權模型時遇到此錯誤：

`Set-AzureRmResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

若要解決此錯誤，在切換授權模型時，請將先前所提到之 PowerShell 程式碼片段中的這些行取消註解： 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

使用下列程式碼來確認您的 Azure PowerShell 版本：

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


