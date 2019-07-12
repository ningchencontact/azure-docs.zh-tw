---
title: 如何在 Azure VM 上執行的 SQL Server 版本進行就地升級 |Microsoft Docs
description: 了解如何變更您在 Azure 中的 SQL Server VM 的版本。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607542"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>如何在 Azure VM 上執行的 SQL Server 版本進行就地升級

本文說明如何變更現有的 SQL Server 在 Azure 中 Windows 虛擬機器上的 SQL Server 版本。 

版本的 SQL Server 取決於產品金鑰，並指定與安裝程序。 版本要求什麼[功能](/sql/sql-server/editions-and-components-of-sql-server-2017)中的 SQL Server 產品所提供。 您可以變更安裝媒體與任一降級至降低成本，或若要啟用更多的功能升級的 SQL Server 版本。

如果您更新 SQL Server 使用安裝媒體與 SQL VM 的資源提供者註冊之後的版本，然後更新 Azure 據此計費您應該設定 SQL VM 資源的 SQL Server 版本屬性，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 瀏覽至您的 SQL Server 虛擬機器資源。 
1. 底下**設定**，選取**設定**，然後選取 從 下拉式清單底下的 所需的 SQL Server 版本**Edition**。 

   ![變更版本中繼資料](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 檢閱警告通知您，您必須首先，變更 SQL Server 版本，而且該版本屬性必須符合 SQL Server 版本。 
1. 選取 **套用**套用版本中繼資料變更。 


## <a name="prerequisites"></a>先決條件

若要執行的 SQL Server 版本的就地變更，您需要下列項目： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- Windows [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)向[SQL VM 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)。
- 設定媒體的 SQL server 所需的版本。 具有客戶[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)可以取得其安裝媒體[Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx)。 客戶不需要軟體保證可以使用安裝媒體從 marketplace 的 SQL Server VM 映像具有其所需的版本。


## <a name="upgrade-edition"></a>升級版本

  > [!WARNING]
  > - **升級的 SQL Server 版本將會重新啟動 SQL Server 的服務，以及任何相關聯的服務，例如 Analysis Services 和 R Services。** 

若要升級的 SQL Server 版本，取得所需的 SQL Server 版本的 SQL Server 安裝媒體，然後執行下列動作：

1. 從 SQL Server 安裝媒體啟動 Setup.exe。 
1. 瀏覽至**維護**，然後選擇**版本升級**選項。 

   ![升級 SQL Server 的版本](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. 選取 **下一步**直到您到達**準備好升級版**頁面，然後再選取**升級**。 安裝程式視窗可能會停止回應的幾分鐘的時間而變更生效，而您會看到**完成**確認您的版本升級已完成的頁面。 

SQL Server 版本升級之後，您就應該修改的 Sql 虛擬機器的 Azure 入口網站上的 「 版本 」 屬性，; 如上所示這會更新的中繼資料和與此 VM 相關聯的計費。

## <a name="downgrade-edition"></a>降級版本

  > [!WARNING]
  > - **降級版本的 SQL Server 需要完全解除安裝 SQL Server，可能會產生額外的停機時間**。 


若要降級的 SQL Server 版本，您必須完全解除安裝 SQL Server，然後再重新安裝一次所需的版本安裝媒體。 

您可以遵循下列步驟，以降級 SQL Server 的版本：

1. 備份所有資料庫，包括系統資料庫。 
1. 將系統資料庫 （master、 model 及 msdb） 移到新位置。 
1. 完整解除安裝 SQL Server 和所有相關聯的服務。 
1. 重新啟動虛擬機器。 
1. 安裝 SQL Server 媒體使用的 SQL Server 所需的版本。
1. 安裝最新的 service pack 和累計更新。  
1. 取代您之前移至不同位置的系統資料庫安裝期間所建立的新系統資料庫。 

一旦降級的 SQL Server 版本，您應該修改 SQL 虛擬機器，在 Azure 入口網站中的 '版本' 屬性，如上所示這會更新的中繼資料和與此 VM 相關聯的計費。

## <a name="remarks"></a>備註

 - SQL Server VM 的 [版本] 屬性必須符合安裝到虛擬機器適用於所有 SQL 虛擬機器包括 PAYG 和 BYOL 授權類型的 SQL Server 的版本。
 - 如果您卸除 SQL Server VM 資源，您將會回到硬式編碼版本設定的映像。
  - 若要變更版本的功能是 SQL VM 的資源提供者的功能。 部署在 Azure 入口網站的 marketplace 映像會自動向資源提供者的 SQL Server VM。 不過，自我安裝 SQL Server 的客戶將需要手動[註冊其 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。
- 將 SQL Server VM 加入至可用性設定組時，需要重新建立 VM。 這類，任何虛擬機器新增到可用性集將會回到預設版本，版本會需要一次修改。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


