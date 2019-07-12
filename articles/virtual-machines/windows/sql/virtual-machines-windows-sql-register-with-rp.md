---
title: 在 Azure 中使用 SQL VM 的資源提供者註冊 SQL Server 虛擬機器 |Microsoft Docs
description: 註冊您的 SQL Server VM 與 SQL VM 資源提供者，以改善管理性。
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786741"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>在 Azure 中使用 SQL VM 的資源提供者註冊 SQL Server 虛擬機器

本文說明如何使用 SQL VM 的資源提供者註冊您的 Azure SQL Server 虛擬機器 (VM)。 

自動部署 SQL Server VM 的 marketplace 映像，透過 Azure 入口網站向資源提供者的 SQL Server VM。 不過，如果您選擇自行安裝 SQL Server Azure 虛擬機器上而不是從 Azure Marketplace 中選擇映像，然後您應該向 SQL Server VM 的資源提供者現在：

-  **合規性**– 每個 Microsoft 產品條款，請使用客戶[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)必須指出，Microsoft 使用 Azure Hybrid Benefit-時，若要這樣做，他們必須向 SQL VM 的資源提供者。 

-  **功能的優點**-向資源提供者註冊您的 SQL Server VM 會解除鎖定[自動修補](virtual-machines-windows-sql-automated-patching.md)，[自動備份](virtual-machines-windows-sql-automated-backup-v2.md)，監視和管理功能，以及[授權](virtual-machines-windows-sql-ahb.md)並[edition](virtual-machines-windows-sql-change-edition.md)彈性。 先前，這些警示是只可以使用 SQL Server VM 映像從 Azure Marketplace。

自我安裝 Azure VM 上的 SQL Server，或佈建 Azure VM 從 SQL server 的自訂 VHD 會遵守透過 Azure Hybrid Benefit 適用於 SQL Server 客戶，指出與 SQL VM 資源註冊到 Microsoft 使用狀況提供者。 

若要利用 SQL VM 的資源提供者，您也必須與您的訂用帳戶註冊 SQL VM 的資源提供者。 這可以使用 Azure 入口網站、 Azure CLI 和 PowerShell 來完成。 

## <a name="prerequisites"></a>先決條件

若要向資源提供者註冊您的 SQL Server VM，您需要下列項目： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- A [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli)並[PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="register-with-sql-vm-resource-provider"></a>使用 SQL VM 資源提供者註冊
如果[SQL IaaS 延伸模組](virtual-machines-windows-sql-server-agent-extension.md)已安裝的 VM 上，然後向 SQL VM 資源提供者只會建立類型為 Microsoft.SqlVirtualMachine/SqlVirtualMachines 的中繼資料資源。 以下是向 SQL VM 資源提供者，如果在 VM 上已安裝 SQL IaaS 延伸模組的程式碼片段。 您必須提供 SQL Server 授權與為 SQL VM 資源提供者註冊時所需的型別 'PAYG' 或 'AHUB'。 

註冊 SQL Server VM 搭配使用 PowerShell 與下列程式碼片段：

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

如果未在 VM 上安裝 SQL IaaS 延伸模組，然後您可以向 SQL VM 資源提供者藉由指定輕量級的 SQL 管理模式。 在輕量級的 SQL 管理模式中，SQL VM 資源提供者就會自動安裝中的 SQL IaaS 擴充功能[輕量級模式](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)並確認 SQL Server 執行個體中繼資料; 這將會重新啟動 SQL Server 服務。 您必須提供 SQL Server 授權與為 SQL VM 資源提供者註冊時所需的型別 'PAYG' 或 'AHUB'。 

在輕量型的 SQL 管理模式下，下列程式碼片段中使用 PowerShell 註冊 SQL Server VM:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
使用中的 SQL VM 資源提供者註冊[輕量級模式](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)會確保合規性和啟用彈性授權，以及就地 SQL Server 版本更新。 容錯移轉叢集執行個體和多重執行個體部署都可以向 SQL VM 只能在輕量級模式中的資源提供者。 您可以遵循的指示，升級至 Azure 入口網站上找到[完整模式](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation)並隨時啟用完整的管理性功能集，SQL Server 重新啟動。 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>註冊 SQL Server 2008/R2 的 Windows Server 2008 Vm

SQL Server 2008 和 2008 R2 安裝在 Windows Server 2008 可以向資源提供中的 SQL VM [NoAgent](virtual-machines-windows-sql-server-agent-extension.md)模式。 此選項可確保合規性，並讓 SQL Server VM，但功能受限，在 Azure 入口網站中監視。

下表詳細說明在註冊期間提供的參數可接受的值：

| 參數 | 可接受的值                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`或 `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` 或 `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


若要註冊您的 SQL Server 2008 或 2008 R2，Windows Server 2008 執行個體上的，使用下列 Powershell 程式碼片段：  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>確認註冊狀態
您可以確認您的 SQL Server 是否已經搭配使用 Azure 入口網站、 Azure CLI 或 PowerShell SQL VM 資源提供者註冊。 

# <a name="azure-portaltabazure-portal"></a>[Azure 入口網站](#tab/azure-portal)
若要確認註冊使用 Azure 入口網站的狀態，請執行下列項目。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 瀏覽至您[SQL 虛擬機器](virtual-machines-windows-sql-manage-portal.md)。
1. 從清單中選取您的 SQL Server VM。 如果此處未列出您的 SQL Server VM，很可能與 SQL VM 的資源提供者尚未註冊您的 SQL Server VM。 
1. 檢視下的值`Status`。 如果`Status = Succeeded`，則 SQL Server VM 已與 SQL VM 的資源提供者成功註冊。 

    ![確認 SQL RP 註冊狀態](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

請確認目前的 SQL Server VM 註冊狀態，使用下列 AZ CLI 命令。 `ProvisioningState` 會顯示`Succeeded`如果登錄成功。 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

請確認目前的 SQL Server VM 註冊狀態，使用下列 PowerShell cmdlet。 `ProvisioningState` 會顯示`Succeeded`如果登錄成功。 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
錯誤指出 SQL Server VM，不向資源提供者註冊。 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>SQL VM 資源提供者註冊訂用帳戶 

若要與 SQL VM 的資源提供者註冊您的 SQL Server VM，您必須註冊資源提供者訂用帳戶。 您可以使用 Azure 入口網站或 Azure CLI 來這麼做。

# <a name="azure-portaltabazure-portal"></a>[Azure 入口網站](#tab/azure-portal)


下列步驟將註冊的 SQL VM 的資源提供者，您使用 Azure 入口網站的 Azure 訂用帳戶。 

1. 開啟 Azure 入口網站並瀏覽至 [所有服務]  。 
1. 瀏覽至 [訂用帳戶]  然後選取感興趣的訂用帳戶。  
1. 在 **訂用帳戶**頁面上，瀏覽至**資源提供者**。 
1. 在篩選條件中輸入 `sql` 以顯示 SQL 相關的資源提供者。 
1. 取決於您所需的動作，針對 **Microsoft.SqlVirtualMachine** 提供者選取 [註冊]  、[重新註冊]  或 [取消註冊]  。 

   ![修改提供者](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

下列程式碼片段會註冊您的 Azure 訂用帳戶的 SQL VM 資源提供者。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

下列 PowerShell 程式碼片段會註冊您的 Azure 訂用帳戶的 SQL VM 資源提供者。

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>備註

 - SQL VM 的資源提供者僅支援使用 Resource Manager 部署的 SQL Server Vm。 使用傳統的模型' 不支援部署 SQL Server Vm。 
 - SQL VM 的資源提供者僅支援 SQL Server Vm 部署至公用雲端。 不支援部署到私用或政府雲端。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


