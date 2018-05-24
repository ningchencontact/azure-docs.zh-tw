---
title: Azure SQL Database 自動、異地備援備份 | Microsoft Docs
description: SQL Database 每隔幾鐘會自動建立一個本機資料庫備份，並使用 Azure 讀取權限異地備援儲存體來進行異地備援。
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 37bbbf8ea5a5d8439b300d0740e4f1a048e98e91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189063"
---
# <a name="learn-about-automatic-sql-database-backups"></a>了解自動 SQL Database 備份

SQL Database 會自動建立資料庫備份，並使用 Azure 讀取權限異地備援儲存體 (RA-GRS) 來提供異地備援。 這些備份是自動建立的，且不需額外付費。 您不需要執行任何動作來建立備份。 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 如果您想要在自己的儲存體容器中保留備份，您可以設定長期的備份保留原則。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL Database 備份？

針對還原時間點 (PITR) 目的，SQL Database 會使用 SQL Server 技術來建立[完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差異](https://msdn.microsoft.com/library/ms175526.aspx)及[交易記錄](https://msdn.microsoft.com/library/ms191429.aspx)備份。 交易記錄備份根據效能層級和資料庫活動量的頻率通常每隔 5-10 分鐘會進行一次。 具有完整和差異備份的交易記錄備份可讓您將資料庫還原到特定的時間點，至裝載資料庫相同的伺服器。 在您還原資料庫時，服務會判斷需要還原的完整、差異及交易記錄備份。


您可以使用這些備份︰

* 將資料庫還原至保留期限內的某一個時間點。 這項作業將會在與原始資料庫相同的伺服器中建立新的資料庫。
* 將已刪除的資料庫還原至其被刪除的時間，或保留期限內的任何時間。 已刪除的資料庫只能在當中已建立原始資料庫的相同伺服器中還原。
* 將資料庫還原到另一個地理區域。 這可讓您在無法存取您的伺服器和資料庫時，從地理災害中復原。 在世界各地任何現有的伺服器中建立新的資料庫。 
* 如果已使用長期保留原則設定資料庫，請從特定長期備份還原資料庫。 這可讓您還原舊版的資料庫來符合規範要求，或執行舊版的應用程式。 請參閱[長期保留](sql-database-long-term-retention.md)。
* 若要執行還原，請參閱[從備份還原資料庫](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將檔案從某個位置複製到另一個位置。 SQL 的「資料庫複寫」  指的是保持多個次要資料庫與主要資料庫同步。 
> 

## <a name="how-often-do-backups-happen"></a>備份頻率是？
每週進行一次完整備份，通常每幾個小時進行一次差異資料庫備份，以及通常每隔 5 - 10 分鐘進行一次交易記錄備份。 建立資料庫之後，會立即排程第一次完整備份。 通常會在 30 分鐘內完成，但如果資料庫很大，則時間可能更久。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。 資料庫備份的確切時間，依 SQL Database 服務整體系統工作負載維持平衡而決定。 

備份儲存體異地複寫是根據 Azure 儲存體複寫排程進行。

## <a name="how-long-do-you-keep-my-backups"></a>您會保留我的備份多久？
每個 SQL Database 備份都具有保留週期，此週期是依據資料庫的服務層，而且在[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)與[以虛擬核心為基礎的購買模型 (預覽)](sql-database-service-tiers-vcore.md) 之間有所不同。 


### <a name="database-retention-for-dtu-based-purchasing-model"></a>以 DTU 為基礎之購買模型的資料庫保留
以 DTU 為基礎之採購模型中的資料庫保留期限取決於服務層。 資料庫保留期限如下：

* 基本服務層為 7 天。
* 標準服務層為 35 天。
* 進階服務層為 35 天。
* 一般用途層可以設定為最多 35 天 (預設值為 7 天)*
* 業務關鍵層 (預覽) 可以設定為最多 35 天 (預設值為 7 天)*

\* 在預覽期間，備份保留期限無法設定且固定為 7 天。

如果您將具有較長備份保留期限的資料庫轉換為具有較短保留期限的資料庫，則無法使用比目標層保留期限還要舊的所有現有備份。

如果您將具有較短保留期限的資料庫升級為具有較長期間的資料庫，則 SQL Database 會保留現有的備份，直到達到較長的保留期限為止。 

如果您刪除資料庫，SQL Database 會以和保存線上資料庫備份的相同方式保存備份。 例如，假設您刪除保存期限為 7 天的「基本」資料庫。 已保存 4 天的備份會再保存 3 天。

> [!IMPORTANT]
> 如果您刪除裝載 SQL Database 的 Azure SQL Server，所有屬於該伺服器的資料庫也會一併刪除且無法復原。 您無法還原已刪除的伺服器。

### <a name="database-retention-for-the-vcore-based-purchasing-model-preview"></a>以虛擬核心為基礎之購買模型 (預覽) 的資料庫保留

為了支援 SQL Database 的還原時間點 (PITR) 和長期保留 (LTR) 功能，系統會配置儲存體供資料庫備份使用。 這個儲存體會分別配置給每個資料庫，並以兩個不同的每一資料庫費用來計費。 

- **PITR**：個別的資料庫備份會自動複製到 RA-GRS 儲存體。 儲存體大小會隨著新備份的建立而動態地增加。  每週完整備份、每日差異備份以及每 5 分鐘複製一次的交易記錄備份都使用此儲存體。 儲存體耗用量取決於資料庫的變動率及保留期限。 您可以為每個資料庫設定 7 到 35 天的不同保留期限。 系統會提供等於資料大小 1 倍的最小儲存體數量，且無額外費用。 對於大多數資料庫來說，此數量就足以儲存 7 天份的備份。 如需詳細資訊，請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)
- **LTR**：SQL Database 提供選項讓您設定完整備份的長期保留期，最長可達 10 年之久。 如果啟用 LTR 原則，這些備份會自動儲存在 RA-GRS 儲存體中，但您可以控制備份的複製頻率。 為了符合不同的合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留期限。 此設定會定義要將多少儲存體用於 LTR 備份。 您可以使用 LTR 定價計算機來估算 LTR 儲存體的成本。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

## <a name="how-to-extend-the-backup-retention-period"></a>如何延長備份保留期限？

如果您的應用程式需要比最大 PITR 備份保留期限還要長的時間可進行備份，您可以設定個別資料庫的長期備份保留原則 (LTR 原則)。 這可讓您將保留期限從最多 35 天延長至多達 10 年。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

一旦您使用 Azure 入口網站或 API 將 LTR 原則新增至資料庫，每週的完整資料庫備份將會自動複製到個別的 RA-GRS 儲存體容器進行長期保留 (LTR 儲存體)。 如果您的資料庫是使用 TDE 加密，則備份會自動加密靜止。 SQL Database 將根據其時間戳記和 LTR 原則，自動刪除過期的備份。 設定原則之後，您不需要管理備份排程或擔心清理舊檔案的工作。 您可以使用 Azure 入口網站或 PowerShell 來檢視、還原或刪除這些備份。

## <a name="are-backups-encrypted"></a>備份經過加密？

Azure SQL 資料庫啟用 TDE 時，也會加密備份。 所有新的 Azure SQL Database 預設都會設定為啟用 TDE。 如需 TDE 的詳細資訊，請參閱 [Azure SQL Database 的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>自動備份是否符合 GDPR 的規範？
如果備份包含個人資料，而該資料受限於一般資料保護規定 (GDPR)，您必須套用增強式安全性措施以保護資料免於遭受未經授權的存取。 為了符合 GDPR 的規範，您需要一個方法來管理資料擁有者的資料要求，而不必存取備份。  針對短期備份，有一個解決方案可以將備份時間縮短為 30 天以內，這是允許完成資料存取要求的時間。  如果需要較長期的備份，建議只在備份中儲存「經過假名化處理」資料。 例如，如果需要刪除或更新個人相關資料，就不需要刪除或更新現有的備份。 您可以在 [GDPR 合規性的資料治理](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html)中找到有關 GDPR 最佳做法的詳細資訊。

## <a name="next-steps"></a>後續步驟

- 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入了解其他 Azure SQL Database 業務持續性解決方案，請參閱[業務持續性概觀](sql-database-business-continuity.md)。
- 若要使用 Azure 入口網站還原至某個時間點，請參閱[使用 Azure 入口網站將資料庫還原至時間點](sql-database-recovery-using-backups.md)。
- 若要使用 PowerShell 還原至某個時間點，請參閱[使用 PowerShell 將資料庫還原至時間點](scripts/sql-database-restore-database-powershell.md)。
- 若要使用 Azure 入口網站在「Azure 復原服務」保存庫中設定、管理自動備份的長期保留及從該保留還原，請參閱[使用 Azure 入口網站來管理長期備份保留 (英文)](sql-database-long-term-backup-retention-configure.md)。
- 若要使用 PowerShell 在「Azure 復原服務」保存庫中設定、管理自動備份的長期保留及從該保留還原，請參閱[使用 PowerShell 來管理長期備份保留 (英文)](sql-database-long-term-backup-retention-configure.md)。
