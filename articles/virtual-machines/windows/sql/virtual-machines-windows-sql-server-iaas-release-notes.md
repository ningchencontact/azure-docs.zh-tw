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
ms.openlocfilehash: d5c003b2588813f8e8a1dfc2923b9d4c8d8c6cc8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820428"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 虛擬機器的 SQL Server 版本資訊

Azure 可讓您使用內建的 SQL Server 映像部署虛擬機器。 本文將摘要說明 [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 最新版本中的新功能與改進功能。 本文也會列出值得注意的內容更新，這些更新並未與該版本直接相關，但會在同一個時間範圍內發佈。 如需其他 Azure 服務的改進功能，請參閱[服務更新](https://azure.microsoft.com/updates)


## <a name="february-2019"></a>2019 年 2 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **入口網站的改進** | 現在便能夠授權模式變更為隨用隨付從 SQL Server VM，以將為您擁有的授權使用[Azure 入口網站](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1)。|
|**使用 Azure SQL VM CLI 的 AG 部署簡化** | 這現在遠比以往若要部署的可用性群組在 Azure 中的 SQL Server VM。 Azure SQL VM CLI 可讓您建立 WSFC，ILB 和 AG 接聽程式，從命令列和記錄的時間 ！ 如需詳細資訊，請參閱 <<c0> [ 使用 Azure SQL VM CLI 設定 Azure VM 上的 SQL Server Always On 可用性群組](virtual-machines-windows-sql-availability-group-cli.md)。 | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
|None | |
| | |

## <a name="december-2018"></a>2018 年 12 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **新增 SQL 叢集群組資源提供者** | 新的資源提供者 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)，可定義 Windows 容錯移轉叢集的中繼資料。 將 SQL Server VM 加入 SqlVirtualMachineGroups 會啟動 Windows 容錯移轉叢集服務，並將 VM 加入叢集。  |
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