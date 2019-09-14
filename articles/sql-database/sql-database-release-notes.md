---
title: Azure SQL Database 版本資訊 |Microsoft Docs
description: 瞭解 Azure SQL Database 服務和 Azure SQL Database 檔中的新功能和改進
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ef19d22b5c47c51f5ee3f74a4d7ab06725f7ed41
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968322"
---
# <a name="sql-database-release-notes"></a>SQL Database 版本資訊

本文列出目前處於公開預覽狀態 SQL Database 功能。 如需 SQL Database 更新與改進功能，請參閱[SQL Database 服務更新](https://azure.microsoft.com/updates/?product=sql-database)。 如需其他 Azure 服務的更新和改進，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="features-in-public-preview"></a>公開預覽中的功能

### <a name="single-databasetabsingle-database"></a>[單一資料庫](#tab/single-database)

| 功能 | 詳細資料 |
| ---| --- |
| 使用單一資料庫和彈性集區加速資料庫復原 | 如需相關資訊，請參閱[加速資料庫](sql-database-accelerated-database-recovery.md)復原。|
|近似計數相異|如需相關資訊，請參閱[近似計數相異](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)。|
|Rowstore 上的批次模式（相容性層級150）|如需相關資訊，請參閱[Rowstore 上的批次模式](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)。|
| 資料探索與分類  |如需詳細資訊，請參閱[Azure SQL Database 和 SQL 資料倉儲資料探索 & 分類](sql-database-data-discovery-and-classification.md)。|
| 彈性資料庫工作 | 如需相關資訊，請參閱[建立、設定和管理彈性作業](elastic-jobs-overview.md)。 |
| 彈性查詢 | 如需相關資訊，請參閱[彈性查詢總覽](sql-database-elastic-query-overview.md)。 |
| 彈性交易 | [跨雲端資料庫的分散式交易](sql-database-elastic-transactions-overview.md)。 |
|記憶體授與意見反應（資料列模式）（在相容性層級150之下）|如需相關資訊，請參閱[記憶體授與意見反應（資料列模式）](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)。|
| Azure 入口網站中的查詢編輯器 |如需相關資訊，請參閱[使用 Azure 入口網站的 SQL 查詢編輯器來連接及查詢資料](sql-database-connect-query-portal.md)。|
| 具有單一資料庫和彈性集區的 R services/機器學習服務 |如需詳細資訊，請參閱[Azure SQL Database 中的 Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
| 使用受控實例重新建立已卸載的資料庫 |如需相關資訊，請參閱[在 AZURE SQL 受控執行個體中重新建立](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)已卸載的資料庫。|
| 使用受控實例進行複寫 |如需相關資訊，請參閱[在 Azure SQL Database 受控實例資料庫中設定](replication-with-sql-database-managed-instance.md)複寫。|
| 無伺服器計算層級 | 如需相關資訊，請參閱[SQL Database 無伺服器（預覽）](sql-database-serverless.md)。|
|SQL Analytics|如需相關資訊，請參閱[Azure SQL 分析](../azure-monitor/insights/azure-sql.md)。|
|資料表變數延後編譯（在相容性層級150之下）|如需相關資訊，請參閱[資料表變數延遲編譯](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)。|
| 使用受控實例的威脅偵測 |如需相關資訊，請參閱[在 Azure SQL Database 受控實例中設定威脅偵測](sql-database-managed-instance-threat-detection.md)。|
| 具有受控實例之攜帶您自己的金鑰（BYOK）的透明資料加密（TDE） |如需詳細資訊[，請參閱 Azure Key Vault 中客戶管理的金鑰的 Azure SQL 透明資料加密：攜帶您自己的金鑰支援](transparent-data-encryption-byok-azure-sql.md)。|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[受控執行個體](#tab/managed-instance)

| 功能 | 詳細資料 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">實例集區</a> | 方便且符合成本效益的方式，將較小的 SQL 實例遷移至雲端。 |
| <a href="https://aka.ms/managed-instance-tde-byok">攜帶您自己的加密金鑰</a> | 從內部部署遷移已啟用透明資料加密（TDE）並具有現有 TDE 金鑰的資料庫。 |
| <a href="https://aka.ms/managed-instance-failover-groups">地理位置分散的容錯移轉群組</a> | 將實例的複本保存在另一個區域中，並確保您的資料即使在區域嚴重損壞情況下仍可供使用。 |
| <a href="https://aka.ms/managed-instance-aadlogins">實例層級 Azure AD 伺服器主體（登入）</a> | 使用<a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a>語句來建立伺服器層級的登入。 |
| [異動複寫](sql-database-managed-instance-transactional-replication.md) | 將資料表中的變更複寫到位於受控實例、單一資料庫或 SQL Server 實例上的其他資料庫，或在其他受控實例或 SQL Server 實例中變更某些資料列時，更新您的資料表。 |
| &nbsp; |

---

## <a name="updates"></a>更新

如需 SQL Database 更新和增強功能的清單，請參閱[SQL Database 服務更新](https://azure.microsoft.com/updates/?product=sql-database)。

如需所有 Azure 服務的更新和改善，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>參與內容

若要參與 Azure SQL Database 檔，請參閱檔[參與者指南](https://docs.microsoft.com/contribute/)。
