---
title: 備份 Azure Stack 檔案與應用程式 | Microsoft Docs
description: 使用 Azure 備份可備份 Azure Stack 檔案和應用程式，並復原到 Azure Stack 環境。
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 99ac43efa5d3211bbe2d790f28532e682058313c
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075872"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>備份 Azure Stack 上的檔案和應用程式
您可以使用 Azure 備份在 Azure Stack 上保護 (或備份) 檔案和應用程式。 若要備份檔案和應用程式，請將 Microsoft Azure 備份伺服器安裝成為在 Azure Stack 上執行的虛擬機器。 您可以保護在相同虛擬網路中任何 Azure Stack 伺服器上執行的應用程式。 在您安裝 Azure 備份伺服器後，請新增 Azure 磁碟，以增加可保存短期備份資料的本機存放區。 Azure 備份伺服器會使用 Azure 存放區進行長期保存。

> [!NOTE]
> 雖然 Azure 備份伺服器和 System Center Data Protection Manager (DPM) 類似，但不支援將 DPM 搭配 Azure Stack 使用。
>


## <a name="azure-backup-server-protection-matrix"></a>Azure 備份伺服器保護矩陣
Azure 備份伺服器會保護下列 Azure Stack 虛擬機器工作負載。

| 受保護的資料來源 | 保護和復原 |
| --------------------- | ----------------------- |
| Windows Server Semi Annual Channel - 資料中心/企業/標準 | 磁碟區、檔案、資料夾 |
| Windows Server 2016 - 資料中心/企業版/標準版 | 磁碟區、檔案、資料夾 |
| Windows Server 2012 R2 - 資料中心/企業版/標準版 | 磁碟區、檔案、資料夾 |
| Windows Server 2012 - 資料中心/企業版/標準版 | 磁碟區、檔案、資料夾 |
| Windows Server 2008 R2 - 資料中心/企業版/標準版 | 磁碟區、檔案、資料夾 |
| SQL Server 2016 | 資料庫 |
| SQL Server 2014 | 資料庫 |
| SQL Server 2012 SP1 | 資料庫 |
| SharePoint 2013 | 伺服器陣列、資料庫、前端、網路伺服器 |
| SharePoint 2010 | 伺服器陣列、資料庫、前端、網路伺服器 |


## <a name="install-azure-backup-server"></a>安裝 Azure 備份伺服器
若要將 Azure 備份伺服器安裝在 Azure Stack 虛擬機器上，請參閱[使用 Azure 備份伺服器準備將工作負載進行備份](backup-azure-microsoft-azure-backup.md) (英文)。 在安裝和設定 Azure 備份伺服器前，請注意以下事項：

### <a name="determining-size-of-virtual-machine"></a>決定虛擬機器大小
若要在 Azure Stack 虛擬機器上執行 Azure 備份伺服器，請使用 A2 或更大的大小。 如需選擇虛擬機器大小方面的協助，請下載 [Azure Stack VM 大小計算機](https://www.microsoft.com/download/details.aspx?id=56832) (英文)。

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Azure Stack 虛擬機器上的虛擬網路
Azure Stack 工作負載中使用的所有虛擬機器必須屬於同一個 Azure 虛擬網路與 Azure 訂用帳戶。

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>將備份資料儲存在本機磁碟與 Azure 中
Azure 備份伺服器會將備份資料儲存在連結至虛擬機器的 Azure 磁碟上，以復原作業。 在磁碟與儲存空間連結到虛擬機器後，Azure 備份伺服器便會為您管理存放區。 備份資料存放區的數量視連結到每個 [Azure Stack 虛擬機器](../azure-stack/user/azure-stack-storage-overview.md)的磁碟數目和大小而定。 每個 Azure Stack VM 的大小，均有可連結至虛擬機器的磁碟數目上限。 例如，A2 是四個磁碟。 A3 是八個磁碟。 A4 是 16 個磁碟。 同樣地，磁碟的大小和數目決定了總備份存放集區。

> [!IMPORTANT]
> 您**不應該**將作業復原 (備份) 資料保留在 Azure 備份伺服器連結的磁碟上超過五天。
>

將備份資料儲存在 Azure 中，會縮小 Azure Stack 上的備份基礎結構。 如果資料超過五天，請儲存到 Azure 中。

若要將備份資料儲存在 Azure 中，請建立或使用復原服務保存庫。 在準備 Azure 備份伺服器工作負載的備份作業時，需[設定復原服務保存庫](backup-azure-microsoft-azure-backup.md#recovery-services-vault)。 設定完成後，每次執行備份作業時，系統就會在保存庫中建立復原點。 每個復原服務保存庫可保留最多 9999 個復原點。 您可以保留備份資料多年，視復原點建立的數目及保留的天數而定。 例如，您可以建立每月復原點，並保留復原點五年。
 
### <a name="using-sql-server"></a>使用 SQL Server
如果您想要使用遠端 SQL Server 作為 Azure 備份伺服器資料庫，僅需選取執行 SQL Server 的 Azure Stack VM。

### <a name="azure-backup-server-vm-performance"></a>Azure 備份伺服器 VM 效能
如果與其他虛擬機器共用，則存放區帳戶大小和 IOPS 限制會影響 Azure 備份伺服器虛擬機器效能。 有鑑於此，Azure 備份伺服器虛擬機器應使用另外的存放區帳戶。 在 Azure 備份伺服器上執行的 Azure 備份代理程式需要暫存存放區，以供：
    - 自用 (快取位置)
    - 供從雲端還原的資料使用 (本機暫存區域)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>設定 Azure 備份暫存磁碟存放區
每個 Azure Stack 虛擬機器皆隨附暫存磁碟存放區，可供使用者作為磁碟區 `D:\`。 Azure 備份所需的本機暫存區域可設定成位在 `D:\` 中，快取位置可位於 `C:\`。 如此一來，便無須將存放區與連結到 Azure 備份伺服器虛擬機器的資料磁碟劃分開來。

### <a name="scaling-deployment"></a>調整部署
如果您想要調整部署，您有以下幾種選擇：
  - 縱向擴展：將 Azure 備份伺服器虛擬機器的大小從 A 系列增加到 D 系列，並[遵照 Azure Stack 虛擬機器指示](../azure-stack/user/azure-stack-manage-vm-disks.md)增加本機存放區。
  - 卸載資料：將較舊的資料傳送至 Azure 備份伺服器，只將最新資料保留在與 Azure 備份伺服器連結的存放區。
  - 橫向擴展：新增更多的 Azure 備份伺服器來保護工作負載。

## <a name="see-also"></a>另請參閱
如需使用 Azure 備份伺服器保護其他工作負載的資訊，請參閱下列文章：
- [備份 SharePoint 伺服器陣列](backup-azure-backup-sharepoint-mabs.md)
- [備份 SQL Server](backup-azure-sql-mabs.md)
