---
title: 管理使用 Azure 入口網站在 Azure 中的 SQL Server Vm |Microsoft Docs
description: 了解如何在 Azure 上裝載的 SQL Server VM 存取 Azure 入口網站中的 SQL 虛擬機器資源。
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
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e2a807bbd6baeb2f14a6d36f5d98a28d48725449
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082712"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>管理使用 Azure 入口網站在 Azure 中的 SQL Server Vm

沒有新的存取點，來管理您在 Azure 上使用的 SQL Server VM [Azure 入口網站](https://portal.azure.com)。 

**SQL 虛擬機器**資源現在是獨立管理服務，可讓您同時檢視所有的 SQL Server Vm，並修改 SQL Server 專屬的設定： 

![SQL 虛擬機器資源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>備註

- **SQL 虛擬機器**資源是建議的方法，來檢視和管理 SQL Server Vm。 不過，目前**SQL 虛擬機器**資源，不支援的管理[結束支援 (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server Vm。 若要管理針對 EOS SQL Server Vm 的設定，請使用已被取代[SQL Server 組態 索引標籤](#access-sql-server-configuration-tab)改。 
- **SQL 虛擬機器**資源只會提供給 SQL Server Vm 已[向 SQL VM 的資源提供者](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider)。 


## <a name="access-sql-virtual-machine-resource"></a>存取 SQL 虛擬機器資源
若要存取 SQL 虛擬機器資源，執行下列作業：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 
1. 選取 [所有服務]  。 
1. 型別`SQL virtual machines`在搜尋方塊中。
1. （選擇性）：接下來選取星號**SQL 虛擬機器**將此選項加入 [我的最愛] 功能表。 
1. 選取  **SQL 虛擬機器**。 

   ![SQL VM 的虛擬機器中找到所有服務](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 這樣會列出所有 SQL Server Vm 可用的訂用帳戶內。 選取您想要管理以啟動**SQL 虛擬機器**資源。 如果您的 SQL Server VM 不是那麼明顯，請使用 [搜尋] 方塊。 

![所有可用的 SQL Vm](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

選取您的 SQL Server VM 將會開啟**SQL 虛擬機器**資源： 


![SQL 虛擬機器資源](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > **SQL 虛擬機器**資源為專用的 SQL Server 設定。 選取名稱中的 VM**虛擬機器**欄位，以瀏覽至特定的 vm，但不限於 SQL Server 的設定。 

## <a name="access-sql-server-configuration-tab"></a>存取 SQL Server 組態 索引標籤
SQL Server 組態 索引標籤已被取代。 在此階段中，它是唯一的方法，來管理[結束支援 (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server Vm 和 SQL Server Vm 尚未[向 SQL VM 的資源提供者](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider)。

若要存取已被取代的 SQL server 組態 索引標籤，您必須瀏覽至**虛擬機器**資源。 若要這樣做，請執行下列動作：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 
1. 選取 [所有服務]  。 
1. 型別`virtual machines`在搜尋方塊中。
1. （選擇性）：接下來選取星號**虛擬機器**將此選項加入 [我的最愛] 功能表。 
1. 選取 [虛擬機器]  。 

   ![搜尋虛擬機器](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 這樣會列出訂用帳戶中的所有虛擬機器。 選取您想要管理以啟動**虛擬機器**資源。 如果您的 SQL Server VM 不是那麼明顯，請使用 [搜尋] 方塊。 
1. 選取  **SQL Server 組態**中**設定**窗格，即可管理您的 SQL Server。 

![SQL Server 設定](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


