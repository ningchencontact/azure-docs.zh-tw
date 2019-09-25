---
title: 變更 Azure 中 SQL Server VM 的授權模型
description: 瞭解如何在 Azure 中切換 SQL Server 虛擬機器的授權，從隨用隨付，到使用 Azure Hybrid Benefit 攜帶您自己的授權。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05cd68c7be005a5b148b7d3e691c46a0d067b0c0
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262862"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>變更 Azure 中 SQL Server 虛擬機器的授權模型
本文說明如何使用新的 SQL VM 資源提供者**microsoft.sqlvirtualmachine**，在 Azure 中變更 SQL Server 虛擬機器（VM）的授權模型。

裝載 SQL Server 的 VM 有兩種授權模式：隨用隨付和 Azure Hybrid Benefit。 您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來修改 SQL Server VM 的授權模型。 

隨用隨付模型表示執行 Azure VM 的每秒成本，包括 SQL Server 授權的成本。
[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)可讓您將自己的 SQL Server 授權與執行 SQL SERVER 的 VM 搭配使用。 

Azure Hybrid Benefit 允許在 Azure 虛擬機器上使用具有軟體保證（「合格授權」）的 SQL Server 授權。 使用 Azure Hybrid Benefit，客戶不需支付在 VM 上使用 SQL Server 授權的費用。 但它們仍然必須支付基礎雲端計算成本（也就是基本費率）、儲存體和備份的費用。 他們也必須支付與服務使用相關聯的 i/o （如適用）。

根據 Microsoft 產品條款：「客戶必須指出他們在設定時，使用 Azure SQL Database （受控執行個體、彈性集區和單一資料庫）、Azure Data Factory、SQL Server Integration Services 或 SQL Server 虛擬機器的 Azure Hybrid BenefitAzure 上的工作負載。」

若要指出在 Azure VM 上使用 Azure Hybrid Benefit 進行 SQL Server 並符合規範，您有三個選項：

- 使用來自 Azure Marketplace 的自備授權 SQL Server 映射來布建虛擬機器。 此選項僅適用于具有 Enterprise 合約的客戶。
- 使用 Azure Marketplace 的隨用隨付 SQL Server 映射並啟用 Azure Hybrid Benefit 來布建虛擬機器。
- 在 Azure VM 上自行安裝 SQL Server、手動[註冊 SQL SERVER VM](virtual-machines-windows-sql-register-with-resource-provider.md)，以及啟動 Azure Hybrid Benefit。

布建 VM 時，會設定 SQL Server 的授權類型。 之後可以隨時變更。 在授權模型之間切換不會產生停機時間、不會重新開機 VM、不新增額外費用，而且會立即生效。 事實上，啟用 Azure Hybrid Benefit 可*降低*成本。

## <a name="prerequisites"></a>必要條件

使用 SQL VM 資源提供者需要 SQL Server IaaS 延伸模組。 因此，您需要下列各項：
- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- [軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- 向[SQL vm 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊的[SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 。


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>變更已向資源提供者註冊之 Vm 的授權 

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

您可以直接從入口網站修改授權模型： 

1. 開啟[Azure 入口網站](https://portal.azure.com)，並開啟 SQL Server VM 的[SQL 虛擬機器資源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)。 
1. 選取 [**設定**] 底下的 **[設定]** 。 
1. 選取 [ **Azure Hybrid Benefit** ] 選項，然後選取核取方塊以確認您擁有具有軟體保證的 SQL Server 授權。 
1. 選取 **[** **設定**] 頁面底部的 [套用]。 

![在入口網站中 Azure Hybrid Benefit](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用 Azure CLI 來變更您的授權模型。  

下列程式碼片段會將您的隨用隨付授權模型切換為自備授權（或使用 Azure Hybrid Benefit）：

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

下列程式碼片段會將您的自備授權模型切換成隨用隨付： 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
您可以使用 PowerShell 來變更您的授權模型。

下列程式碼片段會將您的隨用隨付授權模型切換為自備授權（或使用 Azure Hybrid Benefit）：

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

下列程式碼片段會將您的自備授權模型切換成隨用隨付：

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>變更未向資源提供者註冊之 Vm 的授權

如果您從隨用隨付 Azure Marketplace 映射布建 SQL Server VM，則 SQL Server 授權類型將會是隨用隨付。 如果您使用來自 Azure Marketplace 的自備授權映射來布建 SQL Server VM，授權類型將會是 AHUB。 從預設布建的所有 SQL Server Vm （隨用隨付）或自備授權 Azure Marketplace 映射都會自動向 SQL VM 資源提供者註冊，因此他們可以變更[授權類型](#change-the-license-for-vms-already-registered-with-the-resource-provider)。

您僅有資格透過 Azure Hybrid Benefit 在 Azure VM 上自行安裝 SQL Server。 您應將 SQL Server 授權設定為 Azure Hybrid Benefit，以向[SQL VM 資源提供者註冊這些 vm](virtual-machines-windows-sql-register-with-resource-provider.md) ，以根據 Microsoft 產品條款指出 Azure Hybrid Benefit 的使用方式。

只有在 SQL Server VM 已向 SQL VM 資源提供者註冊時，您才可以將 SQL Server VM 的授權類型變更為隨用隨付或 Azure Hybrid Benefit。

## <a name="remarks"></a>備註

- Azure 雲端解決方案提供者（CSP）客戶可以使用 Azure Hybrid Benefit，方法是先部署隨用隨付 VM，然後將它轉換成自備授權（如果它們具有有效的軟體保證）。
- 如果您卸載 SQL Server 的 VM 資源，將會回到映射的硬式編碼授權設定。 
- 變更授權模型的能力是 SQL VM 資源提供者的一項功能。 透過 Azure 入口網站部署 Azure Marketplace 映射，會自動向資源提供者註冊 SQL Server VM。 但正在自行安裝 SQL Server 的客戶必須手動[註冊其 SQL SERVER VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 
- 將 SQL Server VM 新增至可用性設定組需要重新建立 VM。 因此，任何新增至可用性設定組的 Vm 都會回到預設的隨用隨付授權類型。 必須再次啟用 Azure Hybrid Benefit。 


## <a name="limitations"></a>限制

- 變更授權模型僅適用于具有軟體保證的客戶。
- 只有 SQL Server 的 Standard 和 Enterprise 版本才支援變更授權模型。 不支援 Express、Web 和 Developer 的授權變更。 
- 只有透過 Azure Resource Manager 模型部署的虛擬機器才支援變更授權模型。 不支援透過傳統模型部署的 Vm。 您可以將 VM 從傳統遷移至 Resource Manager 模型，並向 SQL VM 資源提供者註冊。 向 SQL VM 資源提供者註冊 VM 之後，即可在 VM 上使用授權模型變更。
- 只有公用雲端安裝才會啟用變更授權模型。
- 只有在具有單一 NIC （網路介面）的虛擬機器上，才支援變更授權模型。 在有多個 NIC 的虛擬機器上，您應該先移除其中一個 Nic （藉由使用 Azure 入口網站），然後再嘗試此程式。 否則，您會收到類似下列的錯誤： 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  雖然您可以在變更授權模型之後將 NIC 新增回 VM，但透過 Azure 入口網站中的 [SQL Server 設定] 頁面完成的作業（例如自動修補和備份）將不再被視為受支援。

## <a name="known-errors"></a>已知錯誤

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>找不到資源\< \<群組「資源群組 >」下的資源 ' microsoft.sqlvirtualmachine/SqlVirtualMachines/資源群組 > '。
當您嘗試在尚未向 SQL VM 資源提供者註冊的 SQL Server VM 上變更授權模型時，就會發生此錯誤：

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

您必須向您的[訂](virtual-machines-windows-sql-register-with-resource-provider.md#register-the-sql-vm-resource-provider-with-a-subscription)用帳戶註冊資源提供者，然後向[資源提供者註冊您的 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>無法驗證參數 'Sku' 上的引數
嘗試使用4.0 以後的 Azure PowerShell 版本來變更您的 SQL Server VM 授權模型時，可能會遇到此錯誤：

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

若要解決此錯誤，請在切換授權模型時，取消批註先前提到的 PowerShell 程式碼片段中的下列幾行：

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
使用下列程式碼來確認您的 Azure PowerShell 版本：
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


