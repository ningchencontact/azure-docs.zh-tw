---
title: 在 Azure 中使用 SQL VM 資源提供者註冊 SQL Server 虛擬機器 |Microsoft Docs
description: 向 SQL VM 資源提供者註冊您的 SQL Server VM，以改善管理能力。
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
ms.openlocfilehash: b0a7221107f05ff2239bd77cc18e7ffedc18efc1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023590"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>在 Azure 中使用 SQL VM 資源提供者註冊 SQL Server 虛擬機器

本文說明如何在 Azure 中使用 SQL VM 資源提供者註冊您的 SQL Server 虛擬機器（VM）。 

透過 Azure 入口網站部署 SQL Server VM Azure Marketplace 映射，會自動向資源提供者註冊 SQL Server VM。 如果您選擇在 Azure 虛擬機器上自行安裝 SQL Server，而不是從 Azure Marketplace 選擇映射，或如果您從具有 SQL Server 的自訂 VHD 布建 Azure VM，您應該向資源提供者註冊您的 SQL Server VM:

- **簡化授權管理**：根據 Microsoft 產品條款，客戶在使用[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)時，必須告訴 Microsoft。 向 SQL VM 資源提供者註冊，可簡化 SQL Server 授權管理，並可讓您在[入口網站](virtual-machines-windows-sql-manage-portal.md)或 Az CLI 中使用 Azure Hybrid Benefit 快速識別 SQL Server 的 vm： 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **功能優點**：向資源提供者註冊您的 SQL Server VM，可解除鎖定[自動修補](virtual-machines-windows-sql-automated-patching.md)、[自動備份](virtual-machines-windows-sql-automated-backup-v2.md)，以及監視和管理功能。 它也會將[授權](virtual-machines-windows-sql-ahb.md)和[版本](virtual-machines-windows-sql-change-edition.md)彈性解除鎖定。 先前，這些功能僅適用于從 Azure Marketplace SQL Server VM 映射。

- **免費管理**：向 SQL VM 資源提供者註冊，而且所有管理性模式完全免費。 與資源提供者沒有相關聯的額外成本，或變更管理模式。 

若要利用 SQL VM 資源提供者，您也必須向您的訂用帳戶註冊 SQL VM 資源提供者。 您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來完成這項操作。 

  > [!NOTE]
  > 向資源提供者註冊時，沒有相關聯的其他授權需求。 向 SQL VM 資源提供者註冊可提供簡化的方法，以滿足通知 Microsoft，已啟用 Azure Hybrid Benefit 來管理每個資源的授權註冊表單。 

如需使用 SQL VM 資源提供者之優點的詳細資訊，請參閱下列[channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner)影片： 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="在 Azure 上自行安裝 SQL Server 時，從 SQL VM 資源提供者獲益-Microsoft Channel 9 影片"></iframe>


## <a name="prerequisites"></a>必要條件

若要向資源提供者註冊您的 SQL Server VM，您需要下列各項： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- [SQL Server 的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli)或[PowerShell](/powershell/azure/new-azureps-module-az)的最新版本。 


## <a name="register-with-sql-vm-resource-provider"></a>向 SQL VM 資源提供者註冊
如果 VM 上未安裝[SQL Server IaaS 代理程式擴充](virtual-machines-windows-sql-server-agent-extension.md)功能，您可以藉由指定輕量 sql 管理模式向 sql VM 資源提供者註冊。 

在註冊程式期間指定輕量時，SQL VM 資源提供者會自動以[輕量模式](#change-management-modes)安裝 Sql IaaS 延伸模組，並驗證 SQL Server 實例中繼資料。這不會重新開機 SQL Server 服務。 向 SQL VM 資源提供者註冊為 ' PAYG ' 或 ' AHUB ' 時，您需要提供所需的 SQL Server 授權類型。

以輕量模式向 SQL VM 資源提供者註冊，將可確保合規性並啟用彈性的授權，以及就地 SQL Server 版本更新。 容錯移轉叢集實例和多重實例部署只能在輕量模式中向 SQL VM 資源提供者註冊。 您可以隨時[升級](#change-management-modes)為完整管理模式，但是這麼做會重新開機 SQL Server 服務。 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
如果 VM 上已安裝 SQL Server IaaS 擴充功能，請使用下列程式碼片段向 SQL VM 資源提供者註冊。 向 SQL VM 資源提供者註冊時，您必須提供您想要的 SQL Server 授權類型：隨用隨付（`PAYG`）或 Azure Hybrid Benefit （`AHUB`）。 

使用下列 PowerShell 程式碼片段來註冊 SQL Server VM：

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

針對付費版本（Enterprise 或 Standard）：

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

免費版本（Developer、Web 或 Express）：

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

如果手動將 SQL IaaS 延伸模組安裝到 VM，您可以直接建立 Microsoft.sqlvirtualmachine/SqlVirtualMachines 類型的中繼資料資源，以完整模式向 SQL VM 資源提供者註冊。 以下是在 VM 上已安裝 SQL IaaS 擴充功能時，要向 SQL VM 資源提供者註冊的程式碼片段。 您需要提供所需的 SQL Server 授權類型為 ' PAYG ' 或 ' AHUB '。 若要在完整管理模式中註冊，請使用下列 PowerShell 命令：

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>在 Windows Server 上註冊 SQL Server 2008 或 2008 R2 2008 Vm

安裝在 Windows Server 2008 上的 SQL Server 2008 和 2008 R2，可以在[無代理程式模式下](#change-management-modes)向 SQL VM 資源提供者註冊。 此選項可確保合規性，並允許在功能有限的 Azure 入口網站中監視 SQL Server 的 VM。

下表詳細說明註冊期間所提供參數的可接受值：

| 參數 | 可接受的值                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` 或 `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` 或 `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


若要在 Windows Server 2008 實例上註冊您的 SQL Server 2008 或 2008 R2 實例，請使用下列 Powershell 或 Az CLI 程式碼片段：  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>確認註冊狀態
您可以使用 Azure 入口網站、Azure CLI 或 PowerShell，來確認您的 SQL Server VM 是否已向 SQL VM 資源提供者註冊。 

### <a name="azure-portal"></a>Azure 入口網站 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 移至您的[SQL Server 虛擬機器](virtual-machines-windows-sql-manage-portal.md)。
1. 從清單中選取您的 SQL Server VM。 如果您的 SQL Server VM 未列于此處，可能尚未向 SQL VM 資源提供者註冊。 
1. 查看 [**狀態**] 底下的值。 如果**狀態**為 [**成功**]，則 SQL Server 的 vm 已成功向 SQL vm 資源提供者註冊。 

![使用 SQL RP 註冊來驗證狀態](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>命令列

使用 Az CLI 或 PowerShell 來確認目前 SQL Server 的 VM 註冊狀態。 如果註冊成功，`ProvisioningState` 會顯示 `Succeeded`。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

錯誤表示 SQL Server 的 VM 尚未向資源提供者註冊。 

## <a name="change-management-modes"></a>變更管理模式

SQL Server IaaS 延伸模組有三個免費的管理模式： 

- **完整**模式會提供所有功能，但需要重新開機 SQL Server 和系統管理員許可權。 這是預設安裝的選項。 使用它來管理具有單一實例的 SQL Server VM。 完整模式會安裝兩個對記憶體和 CPU 的影響最小的 windows 服務，這可以透過 [工作管理員] 來監視。 使用完整的管理模式並不會產生任何費用。 

- **輕量**不需要重新開機 SQL Server，但只支援變更 SQL Server 的授權類型和版本。 針對具有多個實例的 SQL Server Vm，或參與容錯移轉叢集實例（FCI），請使用此選項。 使用輕量模式時，不會影響記憶體或 CPU。 使用輕量管理模式並沒有相關聯的成本。 

- **NoAgent**專用於 Windows Server 2008 上安裝的 SQL Server 2008 和 SQL Server 2008 R2。 使用 NoAgent 模式時，不會影響記憶體或 CPU。 使用 NoAgent 管理性模式沒有相關聯的成本。 

您可以使用 PowerShell 來查看 SQL Server IaaS 代理程式的目前模式： 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

已安裝*羽量級*IaaS 延伸模組 SQL Server vm 可以使用 Azure 入口網站將模式升級為_full_ 。 在作業系統升級為 Windows 2008 R2 （含）以上版本之後，_無代理程式_模式中的 SQL Server vm 可以升級至_完整_版本。 不可能降級-若要這麼做，您必須完全卸載 SQL IaaS 擴充功能，然後再次安裝。 

若要將代理程式模式升級為完整： 


### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您的[SQL 虛擬機器](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)資源。 
1. 選取您的 SQL Server 虛擬機器，然後選取 **[總覽**]。 
1. 針對具有 NoAgent 或輕量 IaaS 模式的 SQL Server Vm，請選取 [**唯一授權類型] 和 [版本更新]，並提供 SQL IaaS 擴充**功能訊息。

   ![從入口網站變更模式的選項](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 選取 [**我同意在虛擬機器上重新開機 SQL Server 服務**] 核取方塊，然後選取 [**確認**] 將 IaaS 模式升級為 [完整]。 

    ![核取方塊，同意在虛擬機器上重新開機 SQL Server 服務](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>命令列

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

執行下列 Az CLI 程式碼片段：

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

執行下列 PowerShell 程式碼片段：

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```
---

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>向訂用帳戶註冊 SQL VM 資源提供者 

若要向 SQL VM 資源提供者註冊您的 SQL Server VM，您必須向您的訂用帳戶註冊資源提供者。 您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來執行此動作。

### <a name="azure-portal"></a>Azure 入口網站

1. 開啟 Azure 入口網站並移至 **所有服務**。 
1. 移至 [訂用帳戶]，然後選取您感**的訂閱。**  
1. 在 [**訂閱**] 頁面上，移至 [**資源提供者**]。 
1. 在篩選準則中輸入**sql** ，以顯示 sql 相關的資源提供者。 
1. 針對**microsoft.sqlvirtualmachine**提供者，選取 [**註冊**]、[**重新註冊**] 或 [**取消**登錄]，視您想要的動作而定。 

![修改提供者](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>命令列

使用 Az CLI 或 PowerShell，將您的 SQL VM 資源提供者註冊到您的 Azure 訂用帳戶。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
下列程式碼片段會向您的 Azure 訂用帳戶註冊 SQL VM 資源提供者。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>備註

- SQL VM 資源提供者僅支援透過 Azure Resource Manager 部署 SQL Server Vm。 不支援透過傳統模型部署的 SQL Server Vm。 
- SQL VM 資源提供者僅支援部署至公用雲端的 SQL Server Vm。 不支援對私人或政府雲端進行部署。 
 

## <a name="frequently-asked-questions"></a>常見問題集 

**我應該在 Azure Marketplace 中註冊從 SQL Server 映射布建的 SQL Server VM 嗎？**

資料分割 Microsoft 會從 Azure Marketplace 中的 SQL Server 映射自動註冊布建的 Vm。 只有在*未*從 Azure Marketplace 中的 SQL Server 映射布建 VM，而且 SQL Server 已自行安裝時，才需要向 SQL VM 資源提供者註冊。

**SQL VM 資源提供者是否適用於所有客戶？** 

是的。 客戶應該向 SQL VM 資源提供者註冊其 SQL Server 的 Vm （如果未使用 Azure Marketplace 的 SQL Server 映射），而改為自行安裝的 SQL Server，或其是否有自訂的 VHD。 所有訂閱類型（直接、Enterprise 合約和雲端解決方案提供者）所擁有的 Vm 可以向 SQL VM 資源提供者註冊。

**如果我的 SQL Server VM 已安裝 SQL Server IaaS 延伸模組，我應該向 SQL VM 資源提供者註冊嗎？**

如果您的 SQL Server VM 已自行安裝，而不是從 Azure Marketplace 中的 SQL Server 映射布建，則您應該向 SQL VM 資源提供者註冊，即使您已安裝 SQL Server IaaS 延伸模組也一樣。 向 SQL VM 資源提供者註冊，會建立 SqlVirtualMachines 類型的新資源。 安裝 SQL Server IaaS 延伸模組並不會建立該資源。

**向 SQL VM 資源提供者註冊時，預設的管理模式是什麼？**

當您向 SQL VM 資源提供者註冊時，預設的管理模式已*滿*。 當您向 SQL VM 資源提供者註冊時，如果未設定 SQL Server 管理屬性，則會將模式設定為完整的管理能力。 在 VM 上安裝 SQL Server IaaS 擴充功能，是以完整管理模式向 SQL VM 資源提供者註冊的必要條件。

**向 SQL VM 資源提供者註冊的必要條件為何？**

在輕量模式或無代理程式模式中，不需要向 SQL VM 資源提供者註冊。 以完整模式向 SQL VM 資源提供者註冊的必要條件是已在 VM 上安裝 SQL Server IaaS 延伸模組。

**如果 VM 上未安裝 SQL Server IaaS 擴充功能，是否可以向 SQL VM 資源提供者註冊？**

是，如果您未在 VM 上安裝 SQL Server IaaS 擴充功能，您可以在輕量管理模式中向 SQL VM 資源提供者註冊。 在輕量模式中，SQL VM 資源提供者會使用主控台應用程式來確認 SQL Server 實例的版本。 

向 SQL VM 資源提供者註冊時的預設 SQL 管理模式已_滿_。 如果向 SQL VM 資源提供者註冊時未設定 SQL 管理屬性，則會將模式設定為完整管理能力。 在 VM 上安裝 SQL IaaS 擴充功能是在完整管理模式下向 SQL VM 資源提供者註冊的必要條件。

**向 SQL VM 資源提供者註冊會在我的 VM 上安裝代理程式嗎？**

資料分割 向 SQL VM 資源提供者註冊，只會建立新的中繼資料資源。 它不會在 VM 上安裝代理程式。

只有在啟用完整的管理能力時，才需要 SQL Server IaaS 延伸模組。 將管理性模式從輕量升級為 full 會安裝 SQL Server IaaS 延伸模組，並將 SQL Server 重新開機。

**是否會向 VM 上的 SQL Server VM 資源提供者註冊 SQL Server 重新開機？**

資料分割 向 SQL VM 資源提供者註冊，只會建立新的中繼資料資源。 它不會在 VM 上重新開機 SQL Server。 

只有安裝 SQL Server IaaS 延伸模組時，才需要重新開機 SQL Server。 而且需要 SQL Server IaaS 延伸模組，才能啟用完整的管理能力。 將管理性模式從輕量升級為 full 會安裝 SQL Server IaaS 延伸模組，並將 SQL Server 重新開機。

**向 SQL VM 資源提供者註冊時，輕量和無代理程式管理模式有何差異？** 

無代理程式管理模式僅適用于 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 這是唯一可用於這些版本的管理模式。 對於所有其他版本的 SQL Server，這兩個可用的管理模式是輕量且完整的。 

無代理程式模式需要客戶設定 SQL Server 版本和版本屬性。 輕量模式會查詢 VM，以尋找 SQL Server 實例的版本。

**我可以使用 Azure CLI，以輕量或無代理程式模式向 SQL VM 資源提供者註冊嗎？**

資料分割 只有當您使用 Azure PowerShell 向 SQL VM 資源提供者註冊時，才可以使用 [管理模式] 屬性。 Azure CLI 不支援設定 SQL Server 管理性屬性。 它一律會向 SQL VM 資源提供者註冊預設模式中的完整管理能力。

**我可以向 SQL VM 資源提供者註冊，而不需要指定 SQL Server 授權類型嗎？**

資料分割 當您向 SQL VM 資源提供者註冊時，SQL Server 授權類型不是選擇性的屬性。 您必須使用 Azure CLI 和 PowerShell，在所有管理性模式（無代理程式、輕量和完整）中向 SQL VM 資源提供者註冊時，將 SQL Server 授權類型設定為隨用隨付或 Azure Hybrid Benefit。

**我可以將 SQL Server IaaS 擴充功能從無代理程式模式升級為完整模式嗎？**

資料分割 無代理程式模式無法使用 [完整] 或 [輕量] 升級管理模式。 這是 Windows Server 2008 的技術限制。

**是否可以將 SQL Server IaaS 延伸模組從輕量模式升級至完整模式？**

是的。 透過 PowerShell 或 Azure 入口網站，可支援從輕型升級至完整的管理性模式。 需要重新開機 SQL Server。

**我可以將 SQL Server IaaS 擴充功能從完整模式降級為無代理程式或輕量管理模式嗎？**

資料分割 不支援降級 SQL Server IaaS 擴充功能管理模式。 管理模式無法從完整模式降級為輕量或無代理程式模式，而且無法從輕量模式降級為無代理程式模式。 

若要從完整的管理性變更管理性模式，請移除 SQL Server IaaS 延伸模組。 然後，卸載 Microsoft.sqlvirtualmachine 資源，然後使用 SQL VM 資源提供者重新註冊 SQL Server VM。

**我可以從 Azure 入口網站向 SQL VM 資源提供者註冊嗎？**

資料分割 Azure 入口網站中無法使用 SQL VM 資源提供者註冊。 Azure CLI 或 PowerShell 支援以完整管理性模式向 SQL VM 資源提供者註冊。 只有 Azure PowerShell Api 才支援以輕量或無代理程式管理性模式向 SQL VM 資源提供者註冊。

**在安裝 SQL Server 之前，我可以向 SQL VM 資源提供者註冊 VM 嗎？**

資料分割 VM 至少應該要有一個 SQL Server 實例，才能成功向 SQL VM 資源提供者註冊。 如果 VM 上沒有 SQL Server 實例，新的 Microsoft.sqlvirtualmachine 資源會處於失敗狀態。

**如果有多個 SQL Server 實例，可以向 SQL VM 資源提供者註冊 VM 嗎？**

是的。 SQL VM 資源提供者只會註冊一個 SQL Server 實例。 SQL VM 資源提供者會在多個實例的情況下，註冊預設的 SQL Server 實例。 如果沒有預設實例，則只支援在輕量模式下註冊。 若要從輕量升級到完整的管理性模式，預設 SQL Server 實例應該存在，或 VM 應該只有一個名為 SQL Server 實例。

**我可以向 SQL VM 資源提供者註冊 SQL Server 容錯移轉叢集實例嗎？**

是的。 在輕量模式中，可以向 SQL VM 資源提供者註冊 Azure VM 上的 SQL Server 容錯移轉叢集實例。 不過，SQL Server 容錯移轉叢集實例無法升級至完整的管理性模式。

**如果已設定 Always On 可用性群組，可以向 SQL VM 資源提供者註冊我的 VM 嗎？**

是的。 如果您要參與 Always On 可用性群組設定，則在 Azure VM 上使用 SQL VM 資源提供者註冊 SQL Server 實例沒有任何限制。

**向 SQL VM 資源提供者註冊的成本為何，或升級為完整的管理性模式？**
無。 向 SQL VM 資源提供者註冊，或使用三種管理模式中的任何一種，都不會產生任何費用。 使用資源提供者管理您的 SQL Server VM 完全免費。 

**使用不同的管理模式會對效能有何影響？**
使用*NoAgent*和*輕量*管理模式時，不會有任何影響。 使用安裝在作業系統上的兩個服務的*完整*管理模式時，會產生最小的影響。 這些可以透過工作管理員來監視。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
