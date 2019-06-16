---
title: Azure VM 的 SQL Server 版本資訊 | Microsoft Docs
description: 深入了解 Azure VM 的 SQL Server 新功能和改進功能
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
ms.date: 2/13/2019
ms.openlocfilehash: ee3aeb9f44d1b98d6307c6a72d1e4786ea1ec664
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076894"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 虛擬機器的 SQL Server 版本資訊

Azure 可讓您使用內建的 SQL Server 映像部署虛擬機器。 本文將摘要說明 [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 最新版本中的新功能與改進功能。 本文也會列出值得注意的內容更新，這些更新並未與該版本直接相關，但會在同一個時間範圍內發佈。 如需其他 Azure 服務的改進功能，請參閱[服務更新](https://azure.microsoft.com/updates)

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **在 Azure 入口網站中的新 SQL VM 管理** | 目前沒有新的方式來管理您在 Azure 入口網站中的 SQL Server VM。 如需詳細資訊，請參閱 <<c0> [ 在 Azure 入口網站中管理 SQL Server VM](virtual-machines-windows-sql-manage-portal.md)。  | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件 | 詳細資料 |
| --- | --- |
| **新 SQL VM 的入口網站管理** | 約有十幾個發行項已更新為新的 SQL VM 管理入口網站體驗。 | 
| &nbsp; | &nbsp; |


## <a name="april-2019"></a>2019 年 4 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **擴充支援的 SQL Server 2008/2008R2** | [擴充支援](virtual-machines-windows-sql-server-2008-eos-extend-support.md)SQL Server 2008 和 SQL Server 2008 R2 移轉*為-是*至 Azure VM。 | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>2019 年 3 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **自訂映像可支援性** | 您現在可以安裝[SQL IaaS 延伸模組](virtual-machines-windows-sql-server-agent-extension.md#installation)自訂的作業系統和 SQL 映像，以提供的功能受限[彈性授權](virtual-machines-windows-sql-ahb.md)。 當使用 SQL 資源提供者，註冊您的自訂映像指定為 'AHUB' 的授權類型為否則登錄將會失敗。  | 
| **具名執行個體可支援性** | 您現在可以利用[SQL IaaS 延伸模組](virtual-machines-windows-sql-server-agent-extension.md#installation)的具名執行個體，如果已正確地解除安裝的預設執行個體。 | 
| **入口網站的增強功能** | 部署 SQL Server VM 的 Azure 入口網站體驗已經過改造以提高可用性。 如需詳細資訊，請參閱簡介[快速入門](quickstart-sql-vm-create-portal.md)和更全面[How-to](virtual-machines-windows-portal-sql-server-provision.md)部署 SQL Server VM 指南。|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>2019 年 2 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **入口網站的改進** | 現在便能夠授權模式變更為隨用隨付從 SQL Server VM，以將為您擁有的授權使用[Azure 入口網站](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1)。|
|**使用 Azure SQL VM CLI 的 AG 部署簡化** | 這現在遠比以往若要部署的可用性群組在 Azure 中的 SQL Server VM。 [Azure SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid)可讓您建立 WSFC，ILB 和 AG 接聽程式，從命令列中，並記錄的時間 ！ 如需詳細資訊，請參閱 <<c0> [ 使用 Azure SQL VM CLI 設定 Azure VM 上的 SQL Server Always On 可用性群組](virtual-machines-windows-sql-availability-group-cli.md)。 | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018 年 12 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **新增 SQL 叢集群組資源提供者** | 新的資源提供者 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)，可定義 Windows 容錯移轉叢集的中繼資料。 將 SQL Server VM 加入 SqlVirtualMachineGroups  會啟動 Windows 容錯移轉叢集服務，並將 VM 加入叢集。  |
|**使用 Azure 快速入門範本自動設定可用性群組的部署** |現在您可以使用兩個 Azure 快速入門範本來建立 Windows 容錯移轉叢集、將 SQL Server VM 加入該叢集、建立接聽程式，以及設定內部負載平衡器。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure 快速入門範本，若要設定 Azure VM 上的 SQL Server Always On 可用性群組](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **自動化 SQL VM 資源提供者的註冊** | 本月之後部署的 SQL Server VM 會自動向新的 SQL Server 資源提供者註冊。 本月之前部署的 SQL Server VM 仍然需要手動註冊。 如需詳細資訊，請參閱 <<c0> [ 向 SQL VM 資源提供者註冊現有的 SQL VM](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **新增 SQL VM 資源提供者** |  適用於 SQL Server VM 的新資源提供者 (Microsoft.SqlVirtualMachine)，可為您的 SQL Server VM 提供更好的管理。 如需註冊 VM 的詳細資訊，請參閱[使用新資源提供者註冊現有 SQL VM](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider) (英文)。 |
|**切換授權模型** |現在可使用 Azure CLI 或 PowerShell 切換 SQL VM 的依使用量付費與自攜授權模型。 如需詳細資訊，請參閱[如何變更 SQL VM 的授權模型](virtual-machines-windows-sql-ahb.md)。 | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>其他資源

**Windows VM**：

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。
* [佈建 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虛擬機器中的 SQL Server 效能最佳作法](virtual-machines-windows-sql-performance.md)
* [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [佈建 SQL Server Linux 虛擬機器](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常見問題集 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)
