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
ms.openlocfilehash: 44aee447c7f935cd67ca55902c53c5f6f9eb6fda
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52576991"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 虛擬機器的 SQL Server 版本資訊

Azure 可讓您使用內建的 SQL Server 映像部署虛擬機器。 本文列出了部署在 Azure 虛擬機器上最新版 SQL Server 的新功能與改進功能。 


## <a name="november-2018"></a>2018 年 11 月
- **新 SQL 資源提供者**：SQL VM 有新的資源提供者，可更有效率地管理您的 VM。 如需註冊 VM 的詳細資訊，請參閱[使用新資源提供者註冊舊版 SQL VM](virtual-machines-windows-sql-ahb.md#register-legacy-sql-vm-with-new-resource-provider) (英文)。
- **切換授權模型**：現在可使用 Azure CLI 或 Powershell 切換 SQL VM 的依使用量付費與自攜授權模型。 如需詳細資訊，請參閱[如何變更 SQL VM 的授權模式](virtual-machines-windows-sql-ahb.md) (英文)



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
