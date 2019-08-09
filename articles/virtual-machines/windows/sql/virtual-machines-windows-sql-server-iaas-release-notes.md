---
title: Azure 虛擬機器上 SQL Server 的版本資訊 |Microsoft Docs
description: 瞭解 Azure VM 上 SQL Server 的新功能和改進
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/01/2019
ms.openlocfilehash: a482e599914e8b91a237f4395e9c0c47ea84ad59
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846070"
---
# <a name="release-notes-for-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上 SQL Server 的版本資訊

Azure 可讓您使用內建 SQL Server 映射來部署虛擬機器 (VM)。 本文摘要說明[Azure 虛擬機器上](https://azure.microsoft.com/services/virtual-machines/sql-server/)最新版本 SQL Server 的新功能和改進。 本文也會列出值得注意的內容更新，這些更新並未與該版本直接相關，但會在同一個時間範圍內發佈。 如需其他 Azure 服務的改進功能, 請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="july-2019"></a>2019年7月

### <a name="documentation-improvements"></a>文件改進

| 文件 | 詳細資料 |
| --- | --- |
| **將 SQL VM 移至不同的區域** | 使用 Azure Site Recovery, 將[您的 SQL SERVER VM 從一個區域遷移至另一個區域](virtual-machines-windows-sql-move-different-region.md)。 |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>2019 年 6 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **新的 SQL IaaS 安裝模式** | 現在可以在[輕量模式](virtual-machines-windows-sql-server-agent-extension.md)中安裝 SQL Server IaaS 延伸模組, 以避免重新開機 SQL Server 服務。  |
| **SQL Server 版本修改** | 您現在可以變更 SQL Server VM 的[版本屬性](virtual-machines-windows-sql-change-edition.md)。 |
| **SQL VM 資源提供者的變更** | 您可以使用新的 SQL IaaS 模式,[向 SQL vm 資源提供者註冊您的 SQL SERVER VM](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-the-sql-vm-resource-provider) 。 這項功能包括[Windows 2008 映射](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)。|
| **使用 Azure Hybrid Benefit 攜帶您自己的授權映射** | 從 Azure Marketplace 部署的自備授權映射現在可以[將其授權類型切換成隨用隨付](virtual-machines-windows-sql-ahb.md#remarks)。| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>2019 年 5 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **Azure 入口網站中的新 SQL Server VM 管理** | 現在有一種方法可以在 Azure 入口網站中管理您的 SQL Server VM。 如需詳細資訊, 請參閱[管理 Azure 入口網站中的 SQL Server vm](virtual-machines-windows-sql-manage-portal.md)。  | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件 | 詳細資料 |
| --- | --- |
| **新增 SQL Server VM 管理入口網站** | 大約有數十篇文章已更新為新的 SQL Server VM 管理入口網站體驗。 | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>2019 年 4 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **SQL Server 2008/2008 R2 的延伸支援** | 藉由依現有*方式*遷移至 Azure VM, 擴充 SQL Server 2008 和 SQL Server 2008 R2 的[支援](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>2019年3月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **自訂映射可支援性** | 您現在可以將[SQL Server IaaS 延伸](virtual-machines-windows-sql-server-agent-extension.md#installation)模組安裝到自訂作業系統和 SQL 映射, 以提供[彈性授權](virtual-machines-windows-sql-ahb.md)的有限功能。 當您向 SQL 資源提供者註冊您的自訂映射時, 請將授權類型指定為 "AHUB"。 否則, 註冊將會失敗。 | 
| **命名實例可支援性** | 如果預設實例已正確卸載, 您現在可以搭配使用[SQL Server IaaS 擴充](virtual-machines-windows-sql-server-agent-extension.md#installation)功能與已命名的實例。 | 
| **入口網站增強功能** | 已改頭換面部署 SQL Server VM 的 Azure 入口網站體驗, 以改善可用性。 如需詳細資訊, 請參閱簡單的[快速入門](quickstart-sql-vm-create-portal.md)和更完整的作法[指南](virtual-machines-windows-portal-sql-server-provision.md), 以部署 SQL Server 的 VM。|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>2019 年 2 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **入口網站改進** | 現在可以使用[Azure 入口網站](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider), 將 SQL Server VM 的授權模型從 [隨用隨付] 變更為 [自備授權]。|
|**使用 Azure SQL Server VM CLI 簡化可用性群組部署** | 現在您可以比以往更輕鬆地將可用性群組部署至 Azure 中的 SQL Server VM。 您可以使用[Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) , 從命令列建立 Windows 容錯移轉叢集、內部負載平衡器, 以及可用性群組接聽程式。 如需詳細資訊, 請參閱[使用 azure SQL SERVER VM CLI 在 AZURE vm 上設定 SQL Server 的 Always On 可用性群組](virtual-machines-windows-sql-availability-group-cli.md)。 | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018 年 12 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **SQL Server 叢集的新資源提供者** | 新的資源提供者 (Microsoft.sqlvirtualmachine/SqlVirtualMachineGroups) 會定義 Windows 容錯移轉叢集的中繼資料。 將 SQL Server VM 加入至*SqlVirtualMachineGroups*啟動 Windows Server 容錯移轉叢集 (WSFC) 服務, 並將 vm 加入叢集。  |
|**使用 Azure 快速入門範本自動設定可用性群組部署** |現在可以建立 Windows 容錯移轉叢集、將 SQL Server 的 Vm 加入其中、建立接聽程式, 以及使用兩個 Azure 快速入門範本來設定內部負載平衡器。 如需詳細資訊, 請參閱[使用 azure 快速入門範本來設定 AZURE VM 上 SQL Server 的 Always On 可用性群組](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **自動向 SQL VM 資源提供者註冊** | 本月之後部署的 SQL Server VM 會自動向新的 SQL Server 資源提供者註冊。 在這個月之前部署 SQL Server Vm 仍需要手動註冊。 如需詳細資訊, 請參閱[在 Azure 中使用 SQL VM 資源提供者註冊 SQL Server 的虛擬機器](virtual-machines-windows-sql-register-with-resource-provider.md)。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **新增 SQL VM 資源提供者** |  新的資源提供者 (Microsoft.sqlvirtualmachine) 可讓您更有效地管理 SQL Server 的 Vm。 如需註冊 Vm 的詳細資訊, 請參閱[在 Azure 中使用 SQL VM 資源提供者註冊 SQL Server 的虛擬機器](virtual-machines-windows-sql-register-with-resource-provider.md)。 |
|**切換授權模型** | 您現在可以使用 Azure CLI 或 PowerShell, 在 SQL Server VM 的按使用量付費和自備授權模型之間切換。 如需詳細資訊, 請參閱[如何在 Azure 中變更 SQL Server 虛擬機器的授權模型](virtual-machines-windows-sql-ahb.md)。 | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>其他資源

**Windows VM**：

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [佈建 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虛擬機器上 SQL Server 的高可用性和嚴重損壞修復](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虛擬機器上 SQL Server 的效能最佳作法](virtual-machines-windows-sql-performance.md)
* [Azure 虛擬機器上 SQL Server 的應用程式模式和開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [布建 SQL Server Linux 虛擬機器](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常見問題集 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)
