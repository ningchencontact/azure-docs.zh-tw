---
title: 使用 Azure 擴充 SQL Server 2008 和 SQL Server 2008 R2 的支援
description: 瞭解如何藉由將您的 SQL Server 實例遷移至 Azure 來擴充 SQL Server 2008 和 SQL Server 2008 R2 的支援, 或購買延伸支援以保留內部部署實例。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 93e0032cd283eda034519ca29a0e1cf501b5cde6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100460"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>使用 Azure 擴充 SQL Server 2008 和 SQL Server 2008 R2 的支援

SQL Server 2008 和 SQL Server 2008 R2 都已接近[其支援 (EOS) 生命週期的結尾](https://www.microsoft.com/sql-server/sql-server-2008)。 因為許多客戶仍在使用這兩個版本, 所以我們會提供數個選項來繼續取得支援。 您可以將內部部署 SQL Server 實例遷移至 Azure 虛擬機器 (Vm)、遷移至 Azure SQL Database, 或保持內部部署和購買延伸的安全性更新。

與受控實例不同的是, 遷移至 Azure VM 不需要 recertifying 您的應用程式。 與保持內部部署不同的是, 您將會藉由遷移至 Azure VM, 而收到免費的延伸安全性修補程式。

本文的其餘部分提供將您的 SQL Server 實例遷移至 Azure VM 的考慮。

## <a name="provisioning"></a>佈建

Azure Marketplace 上提供**Windows Server 2008 r2**映射的隨用隨付 SQL Server 2008 r2。

在 SQL Server 2008 的客戶必須自行安裝或升級至 SQL Server 2008 R2。 同樣地, Windows Server 2008 上的客戶也必須從自訂 VHD 部署其 VM, 或升級至 Windows Server 2008 R2。

透過 Azure Marketplace 部署的映射隨附預先安裝的 SQL IaaS 延伸模組。 SQL IaaS 擴充功能是彈性授權和自動修補的需求。 部署自我安裝 Vm 的客戶必須手動安裝 SQL IaaS 延伸模組。 Windows Server 2008 不支援 SQL IaaS 延伸模組。

> [!NOTE]
> 雖然 SQL Server**建立**和**管理**blade 會使用 Azure 入口網站中的 SQL Server 2008 R2 映射, 但_不支援_下列功能:自動備份、Azure Key Vault 整合、R 服務和存放裝置設定。

## <a name="licensing"></a>授權
隨用隨付 SQL Server 2008 R2 部署可以轉換成[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)。

若要將以軟體保證 (SA) 為基礎的授權轉換成隨用隨付, 客戶應向 SQL VM[資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊。 在該註冊之後, Azure Hybrid Benefit 與隨用隨付的 SQL 授權類型將可互換。

在 Azure VM 上自行安裝的 SQL Server 2008 或 SQL Server 2008 R2 實例可以向 SQL VM 資源提供者註冊, 並將其授權類型轉換成隨用隨付。

## <a name="migration"></a>遷移
您可以使用手動備份/還原方法, 將 EOS SQL Server 實例遷移至 Azure VM。 這是從內部部署到 Azure VM 的最常見遷移方法。

### <a name="azure-site-recovery"></a>Azure Site Recovery

針對大量遷移, 我們建議[Azure Site Recovery](/azure/site-recovery/site-recovery-overview)服務。 透過 Azure Site Recovery, 客戶可以複寫整個 VM, 包括從內部部署環境到 Azure VM 的 SQL Server。

SQL Server 需要應用程式一致的 Azure Site Recovery 快照集, 以確保復原。 Azure Site Recovery 支援最少1小時間隔的應用程式一致快照集。 具有 Azure Site Recovery 遷移 SQL Server 的最小復原點目標 (RPO) 為1小時。 復原時間目標 (RTO) 是2小時加上 SQL Server 復原時間。

### <a name="database-migration-service"></a>資料庫移轉服務

如果客戶是從內部部署遷移至 Azure VM, 並將 SQL Server 升級至2012版或更新版本, 就可以選擇[資料庫移轉服務](/azure/dms/dms-overview)。

## <a name="disaster-recovery"></a>嚴重損壞修復

Azure VM 上的 EOS SQL Server 的嚴重損壞修復解決方案如下:

- **SQL Server 備份**:使用 Azure 備份協助保護您的 EOS SQL Server 免于勒索軟體、意外刪除和損毀。 此解決方案目前針對 EOS SQL Server 處於預覽狀態, 並支援在 Windows 2008 R2 SP1 上執行 SQL Server 2008 和 2008 R2。 如需詳細資訊, 請參閱[這篇文章](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2)。
- **記錄傳送**:您可以在另一個區域或 Azure 區域中, 使用持續還原來建立記錄傳送複本, 以減少 RTO。 您需要手動設定記錄傳送。
- **Azure Site Recovery**：您可以透過 Azure Site Recovery 複寫, 在區域和區域之間複寫您的 VM。 SQL Server 需要應用程式一致的快照集, 以確保在發生嚴重損壞時進行復原。 Azure Site Recovery 提供最少1小時的 RPO 和2小時 (加上 SQL Server 復原時間) RTO 來進行 EOS SQL Server 嚴重損壞修復。

## <a name="security-patching"></a>安全性修補
在 SQL Server VM 已向 SQL VM[資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊之後, 會透過 Microsoft Update 通道傳遞 SQL Server vm 的擴充安全性更新。 您可以手動或自動下載修補程式。

*Automated patching* 。 自動修補可讓 Azure 自動修補 SQL Server 和作業系統。 如果已安裝 SQL Server IaaS 擴充功能, 您可以指定維護期間的星期幾、時間和持續時間。 Azure 會在此維護期間執行修補。 維護期間排程會使用 VM 地區設定做為時間。  如需詳細資訊, 請參閱[Azure 虛擬機器上 SQL Server 的自動修補](virtual-machines-windows-sql-automated-patching.md)。


## <a name="next-steps"></a>後續步驟

將您的 SQL Server VM 遷移至 Azure:

* [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)

開始使用 Azure 虛擬機器上的 SQL Server:

* [在 Azure 入口網站中建立 SQL Server VM](quickstart-sql-vm-create-portal.md)

取得 SQL Server Vm 的常見問題解答:

* [Azure 虛擬機器上 SQL Server 的常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
