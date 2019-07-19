---
title: 在 Azure 中使用 SQL VM 資源提供者註冊 SQL Server 虛擬機器 |Microsoft Docs
description: 向 SQL VM 資源提供者註冊您的 SQL Server VM, 以改善管理能力。
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
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305878"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>在 Azure 中使用 SQL VM 資源提供者註冊 SQL Server 虛擬機器

本文說明如何向 SQL VM 資源提供者註冊您的 Azure SQL Server 虛擬機器 (VM)。 

透過 Azure 入口網站部署 SQL Server VM marketplace 映射, 會自動向資源提供者註冊 SQL Server VM。 不過, 如果您選擇在 Azure 虛擬機器上自行安裝 SQL Server, 而不是從 Azure Marketplace 選擇映射, 則您應該立即向資源提供者註冊您的 SQL Server VM:

-  **合規性**–根據 Microsoft 產品條款, 使用[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)的客戶必須向 Microsoft 表示使用 Azure Hybrid Benefit-若要這麼做, 則必須向 SQL VM 資源提供者註冊。 

-  **功能優點**-向資源提供者註冊您的 SQL Server VM, 可解除鎖定[自動修補](virtual-machines-windows-sql-automated-patching.md)、[自動備份](virtual-machines-windows-sql-automated-backup-v2.md)、監視和管理功能, 以及[授權](virtual-machines-windows-sql-ahb.md)和[版本](virtual-machines-windows-sql-change-edition.md)彈性。 之前, 這些僅適用于 Azure Marketplace 中的 SQL Server VM 映射。

在 Azure VM 上自行安裝 SQL Server, 或從具有 SQL Server 的自訂 VHD 布建 Azure VM 時, 會透過 Azure Hybrid Benefit 來進行 SQL Server, 條件是客戶藉由向 SQL VM 資源註冊, 來指出該使用 Microsoft。那裡. 

若要利用 SQL VM 資源提供者, 您也必須向您的訂用帳戶註冊 SQL VM 資源提供者。 您可以使用 Azure 入口網站、Azure CLI 和 PowerShell 來完成這項作業。 

## <a name="prerequisites"></a>必要條件

若要向資源提供者註冊您的 SQL Server VM, 您將需要下列各項: 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- [SQL Server 的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli)和[PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="register-with-sql-vm-resource-provider"></a>向 SQL VM 資源提供者註冊
如果 VM 上已安裝[Sql IaaS 擴充](virtual-machines-windows-sql-server-agent-extension.md)功能, 則向 SQL VM 資源提供者註冊只需要建立 Microsoft.sqlvirtualmachine/SqlVirtualMachines 類型的中繼資料資源。 以下是在 VM 上已安裝 SQL IaaS 擴充功能時, 要向 SQL VM 資源提供者註冊的程式碼片段。 向 SQL VM 資源提供者註冊為 ' PAYG ' 或 ' AHUB ' 時, 您需要提供所需的 SQL Server 授權類型。 

使用 PowerShell 註冊 SQL Server VM, 並搭配下列程式碼片段:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

如果 VM 上未安裝 SQL IaaS 擴充功能, 您可以藉由指定輕量 SQL 管理模式向 SQL VM 資源提供者註冊。 在輕量 SQL 管理模式中, SQL VM 資源提供者會自動以[輕量模式](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)安裝 Sql IaaS 擴充功能, 並驗證 SQL Server 實例中繼資料;這不會重新開機 SQL Server 服務。 向 SQL VM 資源提供者註冊為 ' PAYG ' 或 ' AHUB ' 時, 您需要提供所需的 SQL Server 授權類型。 

使用 PowerShell 搭配下列程式碼片段, 在輕量 SQL 管理模式中註冊 SQL Server VM:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

以[輕量模式](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)向 SQL VM 資源提供者註冊, 將可確保合規性並啟用彈性的授權, 以及就地 SQL Server 版本更新。 容錯移轉叢集實例和多重實例部署只能在輕量模式中向 SQL VM 資源提供者註冊。 您可以遵循 Azure 入口網站上找到的指示升級至[完整模式](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation), 並使用 SQL Server 重新開機, 隨時啟用完整的管理功能集。 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>在 Windows Server 2008 Vm 上註冊 SQL Server 2008/R2

安裝在 Windows Server 2008 上的 SQL Server 2008 和 2008 R2 可以向[NoAgent](virtual-machines-windows-sql-server-agent-extension.md)模式中提供的 SQL VM 資源進行註冊。 此選項可確保合規性, 並允許在功能有限的 Azure 入口網站中監視 SQL Server 的 VM。

下表詳細說明註冊期間所提供參數的可接受值:

| 參數 | 可接受的值                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`、或`'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` 或 `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


若要在 Windows Server 2008 實例上註冊您的 SQL Server 2008 或 2008 R2, 請使用下列 Powershell 程式碼片段:  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>確認註冊狀態
您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來驗證您的 SQL Server 是否已向 SQL VM 資源提供者註冊。 

### <a name="azure-portal"></a>Azure 入口網站 
若要使用 Azure 入口網站來確認註冊的狀態, 請執行下列動作。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 流覽至您的[SQL 虛擬機器](virtual-machines-windows-sql-manage-portal.md)。
1. 從清單中選取您的 SQL Server VM。 如果此處未列出您的 SQL Server VM, 可能是您的 SQL Server VM 尚未向 SQL VM 資源提供者註冊。 
1. 查看底下的值`Status`。 如果`Status = Succeeded`為, 則 SQL Server 的 vm 已成功向 SQL vm 資源提供者註冊。 

    ![使用 SQL RP 註冊來驗證狀態](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Az CLI

使用下列 AZ CLI 命令, 確認目前 SQL Server 的 VM 註冊狀態。 `ProvisioningState`會顯示`Succeeded`註冊是否成功。 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

使用下列 PowerShell Cmdlet 來確認目前 SQL Server 的 VM 註冊狀態。 `ProvisioningState`會顯示`Succeeded`註冊是否成功。 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
錯誤表示 SQL Server 的 VM 尚未向資源提供者註冊。 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>向訂用帳戶註冊 SQL VM 資源提供者 

若要向 SQL VM 資源提供者註冊您的 SQL Server VM, 您必須向您的訂用帳戶註冊資源提供者。 您可以使用 Azure 入口網站或 Azure CLI 來執行此動作。

### <a name="azure-portal"></a>Azure 入口網站

下列步驟會使用 Azure 入口網站, 將 SQL VM 資源提供者註冊到您的 Azure 訂用帳戶。 

1. 開啟 Azure 入口網站並瀏覽至 [所有服務]  。 
1. 瀏覽至 [訂用帳戶]  然後選取感興趣的訂用帳戶。  
1. 在 [**訂閱**] 頁面上, 流覽至 [**資源提供者**]。 
1. 在篩選條件中輸入 `sql` 以顯示 SQL 相關的資源提供者。 
1. 取決於您所需的動作，針對 **Microsoft.SqlVirtualMachine** 提供者選取 [註冊]  、[重新註冊]  或 [取消註冊]  。 

   ![修改提供者](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Az CLI
下列程式碼片段會向您的 Azure 訂用帳戶註冊 SQL VM 資源提供者。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

下列 PowerShell 程式碼片段會向您的 Azure 訂用帳戶註冊 SQL VM 資源提供者。

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>備註

 - SQL VM 資源提供者僅支援使用「Resource Manager」部署 SQL Server Vm。 不支援使用「傳統模型」部署 SQL Server Vm。 
 - SQL VM 資源提供者僅支援部署至公用雲端 SQL Server Vm。 不支援對私人或政府雲端進行部署。 
 
## <a name="frequently-asked-questions"></a>常見問題集 

**我應該在 Azure Marketplace 上註冊從 SQL 映射布建的 SQL Server VM 嗎？**

資料分割 從 Azure Marketplace 上的 SQL 映射布建的 Microsoft autoregisters Vm。 只有在未從 Azure marketplace 上的 SQL 映射布建 VM, 且已自行安裝 SQL Server 時, 才需要向 SQL VM 資源提供者註冊。

**SQL VM 資源提供者是否適用於所有客戶？** 

是的。 客戶應該向 SQL VM 資源提供者註冊其 SQL Server VM (如果它們並未使用 Azure marketplace 中的 SQL Server 映射, 並自行安裝 SQL Server 或自訂的 VHD)。 所有訂閱類型 (直接、EA 和 CSP) 所擁有的 Vm 可以向 SQL VM 資源提供者註冊。

**如果我的 SQL Server VM 已安裝 SQL IaaS 延伸模組, 我應該向 SQL VM 資源提供者註冊嗎？**

如果您的 SQL Server VM 已自行安裝, 而不是從 Azure marketplace 上的 SQL 映射布建, 則您應該向 SQL VM 資源提供者註冊, 即使您已安裝 SQL IaaS 擴充功能也一樣。 向 SQL VM 資源提供者註冊, 會建立 SqlVirtualMachines 類型的新資源。 安裝 SQL IaaS 擴充功能並不會建立該資源。

**向 SQL VM 資源提供者註冊時, 預設的 SQL 管理模式是什麼？**

向 SQL VM RP 註冊時的預設 SQL 管理模式已_滿_。 如果向 SQL VM 資源提供者註冊時未設定 SQL 管理屬性, 則會將模式設定為完整管理能力。 在 VM 上安裝 SQL IaaS 擴充功能是在完整管理模式下向 SQL VM 資源提供者註冊的必要條件。

**向 SQL VM 資源提供者註冊的必要條件為何？**

在輕量模式或無代理程式模式中, 不需要向 SQL VM 資源提供者註冊。 以完整模式向 SQL VM 資源提供者註冊的必要條件是已在 VM 上安裝 SQL IaaS 延伸模組。

**如果 VM 上未安裝 SQL IaaS 擴充功能, 是否可以向 SQL VM 資源提供者註冊？**

是, 如果您未在 VM 上安裝 SQL IaaS 擴充功能, 您可以在輕量管理模式中向 SQL VM 資源提供者註冊。 在輕量模式中, SQL VM 資源提供者會使用主控台應用程式來確認 SQL 實例的版本。 主控台應用程式會在確認 VM 上至少有一個 SQL 實例正在執行之後, 自行關閉。 以輕量模式向 SQL VM 資源提供者註冊, 將不會重新開機 SQL Server 且不會在 VM 上建立代理程式。

**向 SQL VM 資源提供者註冊會在我的 VM 上安裝代理程式嗎？**

資料分割 向 SQL VM 資源提供者註冊只會建立新的中繼資料資源, 而且不會在 VM 上安裝代理程式。 只有在啟用完整管理能力時才需要 SQL IaaS 擴充功能, 因此將管理性模式從輕量升級到 Full 會安裝 SQL IaaS 延伸模組, 並 SQL Server 重新開機。

**向 SQL VM 資源提供者註冊 SQL Server 會在我的 VM 上重新開機嗎？**

資料分割 向 SQL VM 資源提供者註冊只會建立新的中繼資料資源, 而且不會在 VM 上重新開機 SQL Server。 只有安裝 SQL IaaS 擴充功能時, 才需要重新開機 SQL Server;需要和 SQL IaaS 擴充功能, 才能啟用完整的管理能力。 將管理性模式從輕量升級到 full 會安裝 SQL IaaS 延伸模組, 並 SQL Server 重新開機。

**向 SQL VM 資源提供者註冊時, 輕量和無代理程式管理模式有何差異？** 

無代理程式管理模式僅適用于 Windows Server 2008 上的 SQL Server 2008/R2;而且這是這些版本唯一可用的管理模式。 對於所有其他版本的 SQL Server, 可用的兩個管理模式是輕量且完整的。 無代理程式模式需要客戶設定 SQL Server 版本和版本屬性;輕量模式會查詢 VM, 以尋找 SQL 實例的版本。

**我可以使用 Azure CLI 以輕量或無代理程式模式向 SQL VM 資源提供者註冊嗎？**

資料分割 使用 Azure PowerShell 向 SQL VM 資源提供者註冊時, 才可以使用 [SQL 管理模式] 屬性。 Azure CLI 不支援設定 SQL 管理性屬性, 而且一律會在預設模式下向 SQL VM 資源提供者註冊-完整管理性。

**是否可以向 SQL VM 資源提供者註冊, 而不需要指定 SQL 授權類型？**

資料分割 向 SQL VM RP 註冊時, SQL 授權類型不是選擇性的屬性。 當您使用 AZ CLI 和 PowerShell 在所有管理性模式 (無代理程式、輕量和完整) 註冊 SQL VM 資源提供者時, 您必須將 SQL 授權類型設定為 PAYG 或 AHUB。

**我可以將 SQL IaaS 擴充功能從無代理程式模式升級為完整模式嗎？**

資料分割 無代理程式模式無法使用 [完整] 或 [輕量] 升級 SQL 管理模式。 這是 Windows Server 2008 的技術限制。

**我可以從輕量模式將 SQL IaaS 擴充功能更新至完整模式嗎？**

是的。 透過 PowerShell 或 Azure 入口網站, 可支援從輕型升級至完整的 SQL 管理模式。而且, 它需要重新開機 SQL Server。

**我可以將 SQL IaaS 擴充功能從完整模式降級為無代理程式或輕量管理模式嗎？**

資料分割 不支援降級 SQL IaaS 擴充功能管理模式。 SQL 管理性模式無法從完整模式降級為輕量或無代理程式模式;而且無法從輕量模式降級為無代理程式模式。 若要從完整的管理性變更管理性模式,移除 SQL IaaS 擴充功能;卸載 Micorsoft Microsoft.sqlvirtualmachine 資源, 然後使用 SQL VM 資源提供者重新註冊 SQL Server VM。

**我可以從 Azure 入口網站向 SQL VM 資源提供者註冊嗎？**

資料分割 Azure 入口網站中無法使用 SQL VM 資源提供者註冊。 Azure CLI 或 PowerShell 支援以完整管理性模式向 SQL VM 資源提供者註冊;而且, 只有 Azure PowerShell Api 才支援以輕量或無代理程式管理性模式向 SQL VM 資源提供者註冊。

**在安裝 SQL Server 之前, 我可以向 SQL VM 資源提供者註冊 VM 嗎？**

資料分割 VM 至少應有一個 SQL 實例, 才能成功向 SQL VM 資源提供者註冊。 如果 VM 上沒有 SQL 實例, 則新的 Micosoft Microsoft.sqlvirtualmachine 資源會處於失敗狀態。

**如果有多個 SQL 實例, 可以向 SQL VM 資源註冊 VM 嗎？**

是的。 SQL VM 資源提供者只會註冊一個 SQL 實例。 SQL VM 資源提供者會在多個實例的情況下註冊預設 SQL 實例。 如果沒有預設實例, 則只支援在輕量模式下註冊。 若要從輕量升級到完整的管理性模式, 預設的 SQL 實例應該存在, 或 VM 應該只有一個已命名的 SQL 實例。

**我可以向 SQL VM 資源提供者註冊 SQL Server 容錯移轉叢集實例嗎？**

是的。 Azure VM 上的 SQL FCI 實例可以在輕量模式中向 SQL VM 資源提供者註冊。 不過, SQL FCI 實例無法升級至完整的管理模式。

**如果已設定 Always ON 可用性群組, 可以向 SQL VM RP 註冊我的 VM 嗎？**

是的。 在 Azure VM 上使用 SQL VM 資源提供者註冊 SQL Server 實例時, 不會有任何限制 (如果參與 Always ON 可用性群組設定)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
