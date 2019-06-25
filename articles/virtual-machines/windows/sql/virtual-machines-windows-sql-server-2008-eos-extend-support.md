---
title: 擴充 SQL Server 2008 和 SQL Server 2008 R2 與 Azure 的支援
description: 了解如何透過將您的 SQL Server 執行個體移轉至 Azure，或購買保留執行個體內部的延伸的支援擴充 SQL Server 2008 和 SQL Server 2008 R2 的支援。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ecb7030fa3652525a36ce15d66ea6e5daf9c3296
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304214"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>擴充 SQL Server 2008 和 SQL Server 2008 R2 與 Azure 的支援

SQL Server 2008 和 SQL Server 2008 R2 兩者即將[支援 (EOS) 生命週期結尾](https://www.microsoft.com/sql-server/sql-server-2008)。 因為我們眾多客戶仍在使用這兩個版本，我們提供數個選項可以繼續享有這項支援。 您可以將您的內部部署 SQL Server 執行個體移轉至 Azure 虛擬機器 (Vm)，移轉到 Azure SQL Database，或維持內部並購買延伸的安全性更新。

不同於受管理的執行個體中，移轉到 Azure VM 不需要 recertifying 您的應用程式。 與不同的是保持內部部署，您會收到免費的延伸的安全性修補程式，藉由移轉至 Azure VM。

這篇文章的其餘部分會提供您的 SQL Server 執行個體移轉到 Azure VM 的考量。

## <a name="provisioning"></a>佈建

沒有隨用隨付`SQL Server 2008 R2 on Windows Server 2008 R2`在 Azure marketplace 映像。

在 SQL Server 2008 客戶必須自我安裝或升級至 SQL Server 2008 R2。 同樣地，在 Windows Server 2008 上的客戶必須將自訂的 VHD，從其 VM 部署或升級至 Windows Server 2008 R2。

透過 Marketplace 部署的映像隨附預先安裝的 SQL IaaS 延伸模組。 SQL IaaS 延伸模組是有彈性的授權和自動修補的需求。 部署自我安裝的 Vm 的客戶必須手動安裝 SQL IaaS 擴充功能。 SQL IaaS 延伸模組不支援在 Windows 2008 上。

  > [!NOTE]
  > 儘管 SQL Server`Create`並`Manage`刀鋒視窗將會使用 SQL Server 2008 r2 映像，在 Azure 入口網站中，下列功能屬_不支援_:自動備份、 Azure 金鑰保存庫整合、 R 服務和儲存體組態。

## <a name="licensing"></a>授權
隨用隨付的 SQL Server 2008 r2 部署可以轉換成[Azure Hybrid Benefit (適 AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/)。

若要轉換隨用隨付軟體保證 (SA) 基礎的授權，客戶應該向 SQL VM[資源提供者](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。 一旦向 SQL VM 的資源提供者，SQL 授權型別會互換適 AHB 與隨用隨付。

Azure VM 上自行安裝的 SQL Server 2008 或 SQL Server 2008 R2 執行個體可以與 SQL 資源提供者註冊，並將其授權類型轉換成隨用隨付。

## <a name="migration"></a>移轉
您可以使用手動備份/還原方法，Azure vm 移轉 EOS SQL Server 執行個體這是最常見移轉方法從內部部署至 Azure VM。

### <a name="azure-site-recovery"></a>Azure Site Recovery

大量移轉，我們建議您[Azure Site Recovery](/azure/site-recovery/site-recovery-overview)服務。 使用 Azure Site Recovery，客戶可以複寫至 Azure VM，包括從內部部署的 SQL Server 的整個 VM。

SQL Server 需要應用程式一致的 Azure Site Recovery 快照集，以確保復原;和 Azure Site Recovery 支援與最小值 1 小時間隔內的應用程式一致快照集。 使用 Azure Site Recovery 移轉可能適用於 SQL Server 最低的 RPO 為 1 小時和 RTO 是 2 小時再加上 SQL Server 復原時間。

### <a name="database-migration-service"></a>Database Migration Service

[資料庫移轉服務](/azure/dms/dms-overview)是客戶的選項，如果從內部部署移轉至 Azure VM，藉由升級至 SQL Server 2012 的 SQL Server 和更新版本。

## <a name="disaster-recovery"></a>災害復原

EOS 上的 SQL Server Azure VM 的災害復原解決方案如下所示：

- **SQL Server 備份**:您可以使用 Azure 備份來保護您免受勒索軟體、 意外刪除及損毀的 EOS SQL Server。 解決方案目前為預覽狀態，EOS SQL server 和 SQL Server 2008 和 2008 R2 支援在 Windows 2008 R2 SP1 上執行。 如需詳細資訊，請參閱這[文章](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2)
- **記錄傳送**:您可以建立記錄傳送複本，在另一個區域或 Azure 區域的持續還原，以縮短 RTO。 客戶必須以手動方式設定記錄傳送。
- **Azure Site Recovery**：您可以在區域和透過 Azure Site Recovery 複寫的區域之間複寫 VM。 SQL Server 需要應用程式一致快照，以確保發生災害時復原。 Azure Site Recovery 會提供最小值 1 小時 RPO 和 2 個小時 + SQL Server 復原時間 RTO 的 EOS SQL Server DR。

## <a name="security-patching"></a>安全性修補
與 SQL 註冊 SQL Server VM 之後，將透過 Microsoft Update 管道傳遞適用於 SQL Server Vm 的延伸的安全性更新[資源提供者](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。 手動或自動，可能是您可以下載修補程式。

**Automated patching** 。 自動修補可讓 Azure 自動修補 SQL Server 和作業系統。 如果已安裝 SQL IaaS 延伸模組，您可以指定一週、 時間和維護期間的持續時間的日期。 Azure 會在此維護期間執行修補。 維護期間排程會使用 VM 地區設定做為時間。  如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補](virtual-machines-windows-sql-automated-patching.md)。


## <a name="next-steps"></a>後續步驟

將您的 SQL Server VM 移轉至 Azure

* [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)

在 Azure 虛擬機器上開始使用 SQL Server：

* [在 Azure 入口網站中建立 SQL Server VM](quickstart-sql-vm-create-portal.md)

獲得有關 SQL VM 常見問題的答案：

* [Azure 虛擬機器上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
