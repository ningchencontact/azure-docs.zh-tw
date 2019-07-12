---
title: 自動化 Azure 虛擬機器上使用 SQL Server IaaS 代理程式擴充功能的管理工作 |Microsoft Docs
description: 本文說明如何管理 SQL Server 代理程式擴充功能，此擴充功能可將特定 SQL Server 管理工作自動化。 其中包括自動備份、自動修補和 Azure 金鑰保存庫整合。
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
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798061"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>自動化 Azure 虛擬機器上使用 SQL Server IaaS 代理程式擴充功能的管理工作
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [傳統](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 代理程式擴充功能 (SqlIaasExtension) 會在 Azure 虛擬機器上執行，以將管理工作自動化。 本文章提供概觀和延伸模組以及安裝、 狀態及移除指示所支援的服務。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

如需檢視這篇文章的精簡版本，請參閱 [SQL Server IaaS 代理程式擴充功能 (傳統)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)。

有三個 SQL 管理能力模式，SQL IaaS 延伸模組：**完整**，**輕量型**，以及**NoAgent**。 

- **完整**模式提供所有功能，但需要重新啟動 SQL Server 和 SA 權限。 這是預設會安裝應該可用於管理 SQL Server VM 是單一執行個體的選項。 

- **輕量型**不需要重新啟動 SQL Server，但只支援 變更授權類型和版本的 SQL Server。 此選項應為搭配 SQL Server Vm 的多個執行個體，或參與容錯移轉叢集執行個體 (FCI)。 

- **NoAgent**是專用於 SQL Server 2008 和 Windows Server 2008 上安裝 SQL Server 2008 R2。 如需利用`NoAgent`模式為 Windows Server 2008 映像，請參閱[Windows Server 2008 註冊](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms)。 

## <a name="supported-services"></a>支援的服務
SQL Server IaaS 代理程式擴充功能支援下列管理工作︰

| 系統管理功能 | 描述 |
| --- | --- |
| **SQL 自動備份** |會自動排程的所有資料庫備份的預設執行個體或[正確安裝](virtual-machines-windows-sql-server-iaas-faq.md#administration)在 VM 上的 SQL Server 的具名執行個體。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動備份 (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)。 |
| **SQL 自動修補** |設定維護期間 (在此期間會進行 VM 的重要 Windows 更新)，以避免在工作負載尖峰時段進行更新。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md)。 |
| **Azure 金鑰保存庫整合** |讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。 如需詳細資訊，請參閱 [在 Azure VM (Resource Manager) 上設定 SQL Server 的 Azure 金鑰保存庫整合](virtual-machines-windows-ps-sql-keyvault.md)。 |

一旦安裝並執行 SQL Server IaaS 代理程式擴充功能之後，Azure 入口網站中虛擬機器的 SQL Server 面板即會提供這些管理功能，亦可透過 Azure PowerShell (若為 SQL Server Marketplace 映像) 以及 Azure PowerShell (若為手動安裝擴充功能) 使用這些功能。 

## <a name="prerequisites"></a>先決條件
在 VM 上使用 SQL Server IaaS 代理程式擴充功能的需求：

**作業系統**：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server 版本**：

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**：

* [下載及設定最新的 Azure PowerShell 命令](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>變更管理模式

您可以使用 PowerShell 檢視您的 SQL IaaS 代理程式目前的模式： 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

適用於 SQL Server Vm 具有*NoAgent*或是*輕量級*IaaS 擴充功能安裝，您可以升級到的模式*完整*使用 Azure 入口網站。 您不可能降級-若要這樣做，您必須完全解除安裝 SQL IaaS 延伸模組，然後再重新安裝。 

到升級代理程式模式，以便*完整*，執行下列動作： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至您[SQL 虛擬機器](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)資源。 
1. 選取您的 SQL Server 虛擬機器，然後選取**概觀**。 
1. 適用於使用 SQL Vm *NoAgent*或*輕量型*IaaS 模式中，選取訊息以取得**僅授權類型和版本的更新可用的 SQL IaaS 延伸模組與**。

    ![啟動模式變更，從入口網站](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 同意**重新啟動 SQL Server 服務**藉由選取核取方塊，然後按**確認**升級您的 IaaS 模式設為 'full'。 

    ![啟用完整管理的 IaaS 擴充功能](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>安裝
當您註冊使用 SQL Server VM 時，已安裝 SQL IaaS 延伸模組[SQL VM 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)。 不過，必要時，SQL IaaS 代理程式也可以安裝使用手動*完整*或是*輕量級*模式安裝。 

*完整*佈建使用 Azure 入口網站的 SQL Server 虛擬機器資源庫映像的其中一個時，會自動安裝 SQL Server IaaS 代理程式擴充功能。 

### <a name="full-mode-installation"></a>完整模式安裝
*完整*SQL IaaS 擴充功能提供完整的 SQL Server VM 上的單一執行個體的管理能力。 如果預設執行個體，然後延伸模組會使用預設執行個體，它也不支援管理其他執行個體。 如果沒有任何預設執行個體，但只有一個具名執行個體，它會管理具名執行個體。 如果沒有預設執行個體，而且有多個具名執行個體，將無法安裝擴充功能。 

安裝*完整*SQL IaaS 的模式將會重新啟動 SQL Server 服務。 若要避免重新啟動 SQL Server 服務，請安裝*輕量級*改為模式使用有限的管理能力。 

安裝 SQL IaaS 代理程式*完整*模式中使用 PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| 參數 | 可接受的值                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`或 `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - 如果尚未安裝延伸模組，安裝**完整**延伸模組重新啟動 SQL Server 服務。 使用**輕量級**模式，以避免重新啟動 SQL Server 服務。 
> - 更新 SQL IaaS 延伸模組不會重新啟動 SQL Server 服務。 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>以單一具名 SQL Server 執行個體的 VM 上安裝
SQL IaaS 延伸模組會使用具名執行個體上的 SQL Server 如果解除安裝的預設執行個體和 IaaS 擴充功能會重新安裝。

若要使用 SQL Server 的具名執行個體，執行下列作業：
   1. 部署市集中的 SQL Server VM。 
   1. IaaS 延伸模組解除安裝內在[Azure 入口網站](https://portal.azure.com)。
   1. 解除安裝 SQL Server 完全在 SQL Server VM。
   1. 安裝 SQL Server 的 SQL Server VM 中的具名執行個體。 
   1. 安裝從 Azure 入口網站中的 IaaS 延伸模組。  


### <a name="install-in-lightweight-mode"></a>在輕量級模式中安裝
輕量級模式不會重新啟動您的 SQL Server 服務，但它提供有限的功能。 

安裝 SQL IaaS 代理程式*輕量級*模式中使用 PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| 參數 | 可接受的值                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`或 `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>取得 SQL IaaS 延伸模組的狀態
其中一項驗證已安裝擴充功能的方法，是在 Azure 入口網站中檢視代理程式狀態。 請選取虛擬機器視窗中的 [所有設定]  ，然後按一下 [擴充功能]  。 您應該會看到列出 **SqlIaasExtension** 擴充功能。

![Azure 入口網站中的 SQL Server IaaS 代理程式擴充功能](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

您也可以使用 **Get-AzVMSqlServerExtension** Azure PowerShell Cmdlet。

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

前一個命令會確認已安裝代理程式，並提供一般狀態資訊。 您也可以使用下列命令取得有關自動備份和修補的特定狀態資訊。

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>移除
在 Azure 入口網站中，您可以解除安裝擴充功能上，按一下省略符號**延伸模組**虛擬機器屬性 視窗。 然後按一下 [刪除]  。

![將 Azure 入口網站中的 SQL Server IaaS 代理程式擴充功能解除安裝](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

您也可以使用 **Remove-AzVMSqlServerExtension** PowerShell Cmdlet。

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>後續步驟
開始使用擴充功能所支援的其中一項服務。 如需詳細資訊，請參閱文章中參考[支援的服務](#supported-services)一節。

如需在 Azure 虛擬機器上執行 SQL Server 的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。

