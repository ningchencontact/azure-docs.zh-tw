---
title: "SQL VM 上的自動化管理工作 (Resource Manager) | Microsoft 文件"
description: "本文說明如何管理 SQL Server 代理程式延伸模組，將特定的 SQL Server 系統管理工作自動化。 其中包括自動備份、自動修補和 Azure 金鑰保存庫整合。"
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/05/2018
ms.author: jroth
ms.openlocfilehash: 1d2b681660ae6f59dec8a287baa853085c64ebeb
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>使用 SQL Server 代理程式延伸模組 (Resource Manager) 自動化 Azure 虛擬機器上的管理工作
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [傳統](../classic/sql-server-agent-extension.md)
> 
> 

SQL Server IaaS 代理程式擴充功能 (SQLIaaSExtension) 會在 Azure 虛擬機器上執行，以將管理工作自動化。 本文章提供支援的延伸模組以及安裝、 狀態及移除指示服務的概觀。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

如需檢視這篇文章的精簡版本，請參閱 [SQL Server IaaS 代理程式擴充功能 (傳統)](../classic/sql-server-agent-extension.md)。

## <a name="supported-services"></a>支援的服務
SQL Server IaaS 代理程式擴充功能支援下列管理工作︰

| 系統管理功能 | 說明 |
| --- | --- |
| **SQL 自動備份** |針對 VM 中 SQL Server 的預設執行個體，將所有資料庫的備份排程自動化。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動備份 (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)。 |
| **SQL 自動修補** |設定維護期間 (在此期間會進行 VM 的更新)，以避免在工作負載尖峰時段進行更新。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md)。 |
| **Azure 金鑰保存庫整合** |讓您在 SQL Server VM 上自動安裝和設定 Azure 金鑰保存庫。 如需詳細資訊，請參閱 [在 Azure VM (Resource Manager) 上設定 SQL Server 的 Azure 金鑰保存庫整合](virtual-machines-windows-ps-sql-keyvault.md)。 |

一旦安裝並執行，SQL Server IaaS 代理程式延伸模組提供這些管理功能的虛擬機器在 Azure 入口網站和 Azure PowerShell for SQL Server marketplace 映像，以及 Azure 上 SQL Server手動安裝的擴充功能的 PowerShell。 

## <a name="prerequisites"></a>必要條件
在 VM 上使用 SQL Server IaaS 代理程式擴充功能的需求：

**作業系統**：

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 版本**：

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**：

* [下載及設定最新的 Azure PowerShell 命令](/powershell/azure/overview)

## <a name="installation"></a>安裝
當您佈建其中一個 SQL Server 虛擬機器資源庫映像時，系統會自動安裝 SQL Server IaaS 代理程式擴充功能。 如果您需要在其中一個 SQL Server 虛擬機器上手動重新安裝擴充功能，請使用下列 PowerShell 命令：

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

> [!IMPORTANT]
> 如果尚未安裝擴充功能，安裝此擴充功能會重新啟動 SQL Server 服務。

您也可以將「SQL Server IaaS 代理程式擴充功能」安裝在只有 OS 的 Windows Server 虛擬機器上。 只有當您也已在該機器上手動安裝 SQL Server 時，才支援此做法。 然後使用相同的 **Set-AzureVMSqlServerExtension** PowerShell Cmdlet 來手動安裝擴充功能。

> [!NOTE]
> 如果您手動在只有作業系統的 Windows Server 虛擬機器上安裝「SQL Server IaaS 代理程式擴充功能」，您便無法透過 Azure 入口網站管理 SQL Server 組態設定。 在此情況下，您必須使用 PowerShell 來進行所有變更。

## <a name="status"></a>狀態
請確認已安裝擴充功能的其中一種方式是在 Azure 入口網站中檢視代理程式狀態。 選取**所有設定**在虛擬機器視窗，然後按一下**延伸**。 您應該會看到列出 **SQLIaaSExtension** 擴充功能。

![SQL Server IaaS 代理程式延伸模組在 Azure 入口網站](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

您也可以使用**Get AzureVMSqlServerExtension** Azure PowerShell cmdlet。

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

前一個命令會確認已安裝代理程式，並提供一般狀態資訊。 您也可以使用下列命令取得有關自動備份和修補的特定狀態資訊。

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>移除
在 Azure 入口網站中，您可以解除安裝擴充功能上，按一下省略符號**延伸**的虛擬機器內容的視窗。 然後按一下 [刪除] 。

![解除安裝 SQL Server IaaS 代理程式延伸模組，在 Azure 入口網站](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

您也可以使用**移除 AzureRmVMSqlServerExtension** PowerShell cmdlet。

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>後續步驟
開始使用擴充功能所支援的其中一項服務。 如需詳細資訊，請參閱文章中所參考[支援服務](#supported-services)本文一節。

如需在 Azure 虛擬機器上執行 SQL Server 的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。

