---
title: Azure VM 的 SQL Server 版本資訊 | Microsoft Docs
description: 深入了解 Azure VM 的 SQL Server 新功能和改進功能
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: e3f44181ca1a5ea64815aadf52aa7ea792a21416
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358570"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 虛擬機器的 SQL Server 版本資訊

Azure 可讓您使用內建的 SQL Server 映像部署虛擬機器。 本文列出了部署在 Azure 虛擬機器上最新版 SQL Server 的新功能與改進功能。 

## <a name="december-2018"></a>2018 年 12 月

| **變更** | 詳細資料 |
| --- | --- |
| **新增 SQL 叢集群組資源提供者** | 新增資源提供者 (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroup)，用來定義 Windows 容錯移轉叢集的相關中繼資料。 將 SQL Server VM 加入 SqlVirtualMachineGroup 會啟動 Windows 容錯移轉叢集服務，並將 VM 加入叢集。  |
|**使用 Azure 快速入門範本自動設定可用性群組的部署** |現在您可以使用兩個 Azure 快速入門範本來建立 Windows 容錯移轉叢集、將 SQL Server VM 加入該叢集、建立接聽程式，以及設定內部負載平衡器。 如需詳細資訊，請參閱[使用 Azure 快速入門範本在 SQL Server VM 上建立 WSFC 和接聽程式，並設定 Always On 可用性群組的 ILB](virtual-machines-windows-sql-availability-group-quickstart-template.md)。 | 
| **自動化 SQL VM 資源提供者的註冊** | 本月之後部署的 SQL Server VM 會自動向新的 SQL Server 資源提供者註冊。 而本月之前部署的 SQL Server VM 仍然需要手動進行註冊。 如需詳細資訊，請參閱[將現有 SQL VM 註冊到新的資源提供者](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider) (英文)。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| **變更** | 詳細資料 |
| --- | --- |
| **新增 SQL VM 資源提供者** |  新增 SQL Server VM 的資源提供者 (Microsoft.SqlVirtualMachine)，可讓您更輕鬆地管理 SQL Server VM。 如需註冊 VM 的詳細資訊，請參閱[使用新資源提供者註冊現有 SQL VM](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider) (英文)。 |
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
* [佈建 SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常見問題集 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)
