---
title: Azure SQL 資料倉儲備份與還原 - 快照集、異地備援 | Microsoft Docs
description: 了解「Azure SQL 資料倉儲」中備份與還原的運作方式。 使用資料倉儲備份來將您的資料倉儲還原至主要區域中的某一個還原點。 使用異地備援備份來還原至不同的地理區域。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/30/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 914513bc19cc81da29efef12d50a6485233d169f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236584"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲中的備份與還原

了解如何在 Azure SQL 数据仓库中使用备份和还原。 使用資料倉儲還原點將您的資料倉儲還原或複製到主要區域中的先前狀態。 使用資料倉儲異地備援備份來還原至不同的地理區域。

## <a name="what-is-a-data-warehouse-snapshot"></a>什么是数据仓库快照

「資料倉儲快照集」會建立還原點，您可以利用此還原點將資料倉儲還原或複製到先前的狀態。  由於 SQL 資料倉儲是一個分散式系統，所以一個資料倉儲快照集會由位於 Azure 儲存體中的許多檔案組成。 快照集會從資料倉儲中儲存的資料內擷取增量變更。

「資料倉儲還原」係指從現有或已刪除的資料倉儲還原點建立的新資料倉儲。 還原資料倉儲是所有商務持續性和災害復原策略中不可或缺的一部分，因為它可在您的資料發生意外損毀或刪除之後重新建立該資料。 資料倉儲也是功能強大的機制，可建立資料倉儲的副本，以用於測試或開發。  SQL 資料倉儲還原費率取決於資料庫大小和來源和目標的資料倉儲的位置而異。 在相同區域內的平均來說，還原速度通常需要大約 20 分鐘的時間。 

## <a name="automatic-restore-points"></a>自動還原點

快照是创建还原点的服务的内置功能。 您不需啟用此功能。 使用者目前無法刪除自動還原點，因為服務會使用這些還原點來維護用於還原的 SLA。

SQL 資料倉儲可快照還原點建立那天一整天的資料倉儲，而此快照集可保留七天。 此保留期無法變更。 SQL 資料倉儲支援八小時的復原點目標 (RPO)。 您可以透過過去七天內所建立的任一個快照集，在主要區域中還原資料倉儲。

若要查看上一個快照集的開始時間，請在您的線上 SQL 資料倉儲上執行此查詢。

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>使用者定義的還原點

使用此功能，可以在大型修改之前和之后手动触发快照，以便创建数据仓库的还原点。 此功能可确保在出现工作负荷中断或用户错误的情况下，还原点在逻辑上是一致的，这样可以提供额外的数据保护，缩短恢复时间。 使用者定義的還原點可保留七天，而且會自動刪除。 您無法變更使用者定義還原點的保留期。 **只保證在任何時間點設定 42 個使用者定義的還原點**，因此必須先[刪除還原點](https://go.microsoft.com/fwlink/?linkid=875299)，才能再建立另一個還原點。 您可以透過 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) 或 Azure 入口網站觸發快照集，以建立使用者定義的還原點。

> [!NOTE]
> 如果您需要保留還原點超過 7 天，請[在此](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)投票給這項功能。 您也可以建立使用者定義的還原點，並從新建立的還原點還原到新資料倉儲。 還原之後，資料倉儲會上線，而您可以無限期地暫停它來節省計算成本。 暫停的資料庫會產生儲存體費用，以「Azure 進階儲存體」費率計費。 如果您需要作用中的已還原資料倉儲副本，您可以讓其恢復上線 (只需幾分鐘的時間)。

### <a name="restore-point-retention"></a>還原點保留

下面列出了还原点保留期的详细信息：

1. 在達到 7 天還原期間，**以及**總共至少有 42 個還原點 (包含使用者定義和自動定義) 時，SQL 資料倉儲會刪除還原點
2. 資料倉儲暫停時不會擷取快照集
3. 還原點的存留期是依自建立還原點起算之絕對日曆天為基準而測量，資料倉儲暫停期間也包含在內
4. 在這段時間中的任一時間點，只要這些還原點尚未達到 7 天的保留期，資料倉儲保證都能儲存最多 42 個使用者定義的還原點，以及 42 個自動還原點
5. 如果已建立快照集，接著資料倉儲就會最多暫停 7 天；讓還原點能夠持續至收集到總共 42 個還原點 (包含使用者定義及自動還原點)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>資料倉儲被卸除時的快照集保留期

當您卸除資料倉儲時，SQL 資料倉儲會建立最後的快照集，並將它儲存七天。 您可以將資料倉儲還原到刪除期間建立的最後一個還原點。

> [!IMPORTANT]
> 如果您刪除邏輯 SQL Server 執行個體，所有屬於該執行個體的資料庫也會一併刪除，且無法復原。 您無法還原已刪除的伺服器。

## <a name="geo-backups-and-disaster-recovery"></a>異地備份和災害復原

SQL 資料倉儲每天都會對[配對的資料中心](../best-practices-availability-paired-regions.md)執行一次異地備份。 異地還原的 RPO 為 24 小時。 您可以在任何 SQL 資料倉儲支援的區域中將異地備份還原到伺服器。 萬一您無法存取主要地區中的還原點，異地備份可以確保您能夠還原資料倉儲。

預設會開啟異地備份。 如果您的資料倉儲是 Gen1，則可以視需要[選擇退出](/powershell/module/az.sql/set-azsqldatabasegeobackuppolicy)。 您無法選擇退出 Gen2 的異地備份，因為資料保護是既定的保證。

> [!NOTE]
> 如果您的異地備份需要較短的 RPO，請[在此](https://feedback.azure.com/forums/307516-sql-data-warehouse)投票給這項功能。 您也可以建立使用者定義的還原點，並從新建立的還原點還原到不同區域中的新資料倉儲。 還原之後，資料倉儲會上線，而您可以無限期地暫停它來節省計算成本。 暫停的資料庫會產生儲存體費用，以「Azure 進階儲存體」費率計費。 如果您需要作用中的資料倉儲副本，您可以讓其恢復運上線 (只需幾分鐘的時間)。

## <a name="backup-and-restore-costs"></a>備份與還原成本

您將發現 Azure 帳單含有儲存體的明細項目以及災害復原儲存體的明細項目。 儲存體費用是在主要區域中儲存資料，加上快照集擷取增量變更的總費用。 如需快照集計費方式的詳細說明，請參閱[了解快照集產生費用的方式](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)。 異地備援的費用則涵蓋儲存異地備份的成本。  

您主要資料倉儲和 7 天快照集變更的總成本會四捨五入到最接近的 TB。 例如，如果您的資料倉儲為 1.5 TB，且快照集取用 100 GB，就會以 Azure 進階儲存體費率向您收取 2 TB 資料費用。

如果您正在使用異地備援儲存體，您會收到個別的儲存體收費項目。 異地備援儲存體是依據標準讀取權限異地備援儲存體 (RA-GRS) 費率收費。

如需有關 SQL 資料倉儲價格的詳細資訊，請參閱 [SQL 資料倉儲價格]。 您不會支付資料輸出，跨區域還原時。

## <a name="restoring-from-restore-points"></a>從還原點還原

每個快照都會建立一個代表快照開始時間的還原點。 若要還原資料倉儲，您需選擇一個還原點並發出還原命令。  

您可以保留已還原的資料倉儲和目前的資料倉儲，或是刪除它們其中之一。 如果您想要以已還原的資料倉儲取代目前的資料倉儲，便可以使用 [ALTER DATABASE (Azure SQL 資料倉儲)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) 搭配 MODIFY NAME 選項來將它重新命名。

若要還原資料倉儲，請參閱[使用 Azure 入口網站來還原資料倉儲](sql-data-warehouse-restore-database-portal.md)、[使用 PowerShell 來還原資料倉儲](sql-data-warehouse-restore-database-powershell.md)或[使用 REST API 來還原資料倉儲](sql-data-warehouse-restore-database-rest-api.md)。

若要還原已刪除或暫停的資料倉儲，您可以[建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="cross-subscription-restore"></a>跨訂用帳戶還原

如果您需要直接還原訂用帳戶，這項功能的投票[此處](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)。 還原至不同的邏輯伺服器和['將'](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)跨訂用帳戶，才能執行跨訂用帳戶還原伺服器。 

## <a name="geo-redundant-restore"></a>異地備援還原

您可以[將資料倉儲還原](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region)到在所選效能等級上支援 SQL 資料倉儲的任何區域。

> [!NOTE]
> 若要執行異地備援還原，您不可選擇退出這項功能。

## <a name="next-steps"></a>後續步驟

如需有關災害規劃的詳細資訊，請參閱[商務持續性概觀](../sql-database/sql-database-business-continuity.md)
