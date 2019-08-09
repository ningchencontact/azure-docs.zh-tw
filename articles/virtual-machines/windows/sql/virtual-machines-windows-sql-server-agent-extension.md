---
title: 使用 SQL Server IaaS 代理程式擴充功能, 將 Azure 虛擬機器上的管理工作自動化 |Microsoft Docs
description: 本文說明如何管理 SQL Server IaaS 代理程式擴充功能, 以將特定的 SQL Server 管理工作自動化。 這些包括自動備份、自動修補和 Azure Key Vault 整合。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d95760745dc3554bc63271cedc63dcf3bf017c5c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855225"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>使用 SQL Server IaaS 代理程式擴充功能, 將 Azure 虛擬機器上的管理工作自動化
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [傳統](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 代理程式擴充功能 (SqlIaasExtension) 會在 Azure 虛擬機器上執行，以將管理工作自動化。 本文提供延伸模組支援之服務的總覽。 本文也提供安裝、狀態及移除擴充功能的指示。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

若要查看本文的傳統版本, 請參閱[SQL Server vm 的 SQL Server IaaS 代理程式擴充功能 (傳統)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)。

SQL Server IaaS 延伸模組有三種管理模式: 

- **完整**模式會提供所有功能, 但需要重新開機 SQL Server 和系統管理員許可權。 這是預設安裝的選項。 使用它來管理具有單一實例的 SQL Server VM。 

- **輕量**不需要重新開機 SQL Server, 但只支援變更 SQL Server 的授權類型和版本。 針對具有多個實例的 SQL Server Vm, 或參與容錯移轉叢集實例 (FCI), 請使用此選項。 

- **NoAgent**專用於安裝在 Windows Server 2008 上的 SQL Server 2008 和 SQL Server 2008 R2。 如需針對 Windows Server 2008 映射使用此模式的相關資訊, 請參閱[Windows server 2008 註冊](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)。 

## <a name="supported-services"></a>支援的服務
SQL Server IaaS 代理程式擴充功能支援下列管理工作︰

| 系統管理功能 | 描述 |
| --- | --- |
| **SQL Server 自動備份** |針對預設實例或虛擬機器上[已正確安裝](virtual-machines-windows-sql-server-iaas-faq.md#administration)之 SQL Server 實例的所有資料庫, 自動排程備份。 如需詳細資訊, 請參閱[Azure 虛擬機器中的 SQL Server 自動備份 (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)。 |
| **SQL Server 自動修補** |設定維護期間 (在此期間會進行 VM 的重要 Windows 更新)，以避免在工作負載尖峰時段進行更新。 如需詳細資訊, 請參閱[Azure 虛擬機器中的 SQL Server 的自動修補 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md)。 |
| **Azure Key Vault 整合** |讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。 如需詳細資訊, 請參閱[在 Azure 虛擬機器上設定 SQL Server 的 Azure Key Vault 整合 (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)。 |

安裝並執行 SQL Server Iaas 代理程式擴充功能之後, 它會讓系統管理功能可供使用:

* 在 Azure 入口網站中虛擬機器的 [SQL Server] 面板上, 以及 Azure Marketplace 上 SQL Server 影像的 [Azure PowerShell]。
* 透過 Azure PowerShell 來手動安裝延伸模組。 

## <a name="prerequisites"></a>先決條件
以下是在您的 VM 上使用 SQL Server IaaS 代理程式擴充功能的需求:

**作業系統**：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server 版本**：

* SQL Server 2008 
* SQL SERVER 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**：

* [下載及設定最新的 Azure PowerShell 命令](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>變更管理模式

您可以使用 PowerShell 來查看 SQL Server IaaS 代理程式的目前模式: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

針對已安裝 NoAgent 或輕型 IaaS 擴充功能的 SQL Server Vm, 您可以使用 Azure 入口網站將模式升級為 full。 無法降級。 若要這麼做, 您必須完全卸載 SQL Server IaaS 擴充功能, 然後再安裝一次。 

若要將代理程式模式升級為完整: 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您的[SQL 虛擬機器](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)資源。 
1. 選取您的 SQL Server 虛擬機器, 然後選取 **[總覽**]。 
1. 針對具有 NoAgent 或輕量 IaaS 模式的 SQL Server Vm, 請選取 [**唯一授權類型] 和 [版本更新], 並提供 SQL IaaS 擴充**功能訊息。

   ![從入口網站變更模式的選項](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 選取 [**我同意在虛擬機器上重新開機 SQL Server 服務**] 核取方塊, 然後選取 [**確認**] 將 IaaS 模式升級為 [完整]。 

    ![核取方塊, 同意在虛擬機器上重新開機 SQL Server 服務](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>安裝
當您向[SQL VM 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-the-sql-vm-resource-provider)註冊您的 SQL Server VM 時, 會安裝 SQL Server IaaS 延伸模組。 如有需要, 您可以使用完整或輕量模式來手動安裝 SQL Server IaaS 代理程式。 

當您使用 Azure 入口網站布建其中一部 SQL Server 的虛擬機器 Azure Marketplace 映射時, 會自動安裝完整模式的 SQL Server IaaS 代理程式延伸模組。 

### <a name="install-in-full-mode"></a>以完整模式安裝
SQL Server IaaS 擴充功能的完整模式可為 SQL Server VM 上的單一實例提供完整的管理能力。 如果有預設實例, 延伸模組將會與預設實例搭配使用, 而且不支援管理其他實例。 如果沒有預設實例, 但只有一個已命名的實例, 則會管理已命名的實例。 如果沒有預設實例, 而且有多個已命名的實例, 將無法安裝延伸模組。 

使用 PowerShell 安裝具有完整模式的 SQL Server IaaS 代理程式:

  ```powershell-interactive
     // Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register the SQL Server VM with 'Full' SQL Server IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| 參數 | 可接受的值                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` 或 `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> 如果尚未安裝延伸模組, 安裝完整擴充功能會重新開機 SQL Server 服務。 若要避免重新開機 SQL Server 服務, 請改為安裝具有有限管理能力的輕量模式。
> 
> 更新 SQL Server IaaS 延伸模組並不會重新開機 SQL Server 服務。 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>在具有單一名為 SQL Server 實例的 VM 上安裝
SQL Server IaaS 擴充功能會在 SQL Server 上使用名為的實例, 如果預設實例已卸載, 且已重新安裝 IaaS 延伸模組。

若要使用 SQL Server 的已命名實例:
   1. 從 Azure Marketplace 部署 SQL Server VM。 
   1. 從[Azure 入口網站](https://portal.azure.com)卸載 IaaS 延伸模組。
   1. 完全卸載 SQL Server VM 內的 SQL Server。
   1. 使用 SQL Server VM 中的已命名實例來安裝 SQL Server。 
   1. 從 Azure 入口網站安裝 IaaS 延伸模組。  


### <a name="install-in-lightweight-mode"></a>以輕量模式安裝
輕量模式不會重新開機您的 SQL Server 服務, 但它會提供有限的功能。 

使用 PowerShell 以輕量模式安裝 SQL Server IaaS 代理程式:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| 參數 | 可接受的值                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` 或 `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>取得 SQL Server IaaS 延伸模組的狀態
其中一項驗證已安裝擴充功能的方法，是在 Azure 入口網站中檢視代理程式狀態。 選取 [虛擬機器] 視窗中的 [**所有設定**], 然後選取 [**擴充**功能]。 您應該會看到列出 **SqlIaasExtension** 擴充功能。

![Azure 入口網站中 SQL Server IaaS 代理程式延伸模組的狀態](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

您也可以使用**set-azvmsqlserverextension** Azure PowerShell Cmdlet:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

先前的命令會確認代理程式已安裝, 並提供一般狀態資訊。 您可以使用下列命令來取得有關自動備份和修補的特定狀態資訊:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>移除
在 Azure 入口網站中, 您可以在虛擬機器內容的 [**擴充**功能] 視窗中選取省略號來卸載擴充功能。 然後選取 [刪除]。

![卸載 Azure 入口網站中的 SQL Server IaaS 代理程式擴充功能](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

您也可以使用**Set-azvmsqlserverextension** PowerShell Cmdlet:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>後續步驟
開始使用此延伸模組支援的其中一項服務。 如需詳細資訊, 請參閱本文的[支援的服務](#supported-services)一節中所參考的文章。

如需在 Azure 虛擬機器上執行 SQL Server 的詳細資訊, 請參閱[什麼是 azure 虛擬機器上的 SQL Server？](virtual-machines-windows-sql-server-iaas-overview.md)。

