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
ms.openlocfilehash: 232f8b13174aed19477ee0b139c83fba439049ac
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101538"
---
# <a name="sql-database-release-notes"></a>SQL Database 版本資訊

本文列出目前處於公開預覽狀態 SQL Database 功能。 如需 SQL Database 更新與改進功能，請參閱[SQL Database 服務更新](https://azure.microsoft.com/updates/?product=sql-database)。 如需其他 Azure 服務的更新和改進，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="features-in-public-preview"></a>公開預覽中的功能

### <a name="single-databasetabsingle-database"></a>[單一資料庫](#tab/single-database)

| 功能 | 詳細資料 |
| ---| --- |
| [Azure 私人連結](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Private Link 可將資料保留在 Azure 網路中，避免將其暴露於網際網路，藉此簡化了網路架構，同時可保護 Azure 中端點之間的連線安全。 您也可利用 Private Link 在 Azure 上建立及呈現您自己的服務。 |
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
| 無伺服器計算層級 | 如需相關資訊，請參閱[SQL Database 無伺服器（預覽）](sql-database-serverless.md)。|
|SQL Analytics|如需相關資訊，請參閱[Azure SQL 分析](../azure-monitor/insights/azure-sql.md)。|
|資料表變數延後編譯（在相容性層級150之下）|如需相關資訊，請參閱[資料表變數延遲編譯](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)。|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[受控執行個體](#tab/managed-instance)

| 功能 | 詳細資料 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">實例集區</a> | 方便且符合成本效益的方式，將較小的 SQL 實例遷移至雲端。 |
| <a href="https://aka.ms/managed-instance-tde-byok">具有攜帶您自己的金鑰的透明資料加密（TDE）（BYOK）</a> |如需詳細資訊[，請參閱 Azure Key Vault 中客戶管理的金鑰的 Azure SQL 透明資料加密：攜帶您自己的金鑰支援](transparent-data-encryption-byok-azure-sql.md)。|
| <a href="https://aka.ms/managed-instance-aadlogins">實例層級 Azure AD 伺服器主體（登入）</a> | 使用<a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a>語句來建立伺服器層級的登入。 |
| [異動複寫](sql-database-managed-instance-transactional-replication.md) | 將資料表中的變更複寫到位於受控實例、單一資料庫或 SQL Server 實例上的其他資料庫，或在其他受控實例或 SQL Server 實例中變更某些資料列時，更新您的資料表。 如需相關資訊，請參閱[在 Azure SQL Database 受控實例資料庫中設定](replication-with-sql-database-managed-instance.md)複寫。 |
| 威脅偵測 |如需相關資訊，請參閱[在 Azure SQL Database 受控實例中設定威脅偵測](sql-database-managed-instance-threat-detection.md)。|
| 使用受控實例重新建立已卸載的資料庫 |如需相關資訊，請參閱[在 AZURE SQL 受控執行個體中重新建立](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)已卸載的資料庫。|
| &nbsp; |

---

## <a name="new-features"></a>新功能

### <a name="managed-instance-h2-2019-updates"></a>受控實例 H2 2019 更新

- 使用[全域追蹤旗標](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)來設定您的受控實例行為。

### <a name="managed-instance-h1-2019-updates"></a>受控實例 H1 2019 更新

下列功能已在 H1 2019 中的受控實例部署模型中啟用：
  - 支援訂用帳戶的訂用帳戶， <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 訂閱者的每月點數</a>，以及增加的[地區限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
  - 對 <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 和 SharePoint 2019 </a> 以及 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a> 的支援
  - 建立具有<a href="https://aka.ms/managed-instance-collation">伺服器層級定序</a>和您所選擇<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">時區</a>的實例。
  - 受控實例現在會受到<a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">內建防火牆</a>的保護。
  - 將實例設定為使用[公用端點](sql-database-managed-instance-public-endpoint-configure.md)、 [Proxy 覆寫](/sql-database-connectivity-architecture.md#connection-policy)連線以取得更佳的網路效能、 <a href="https://aka.ms/four-cores-sql-mi-update">4 虛擬核心在第5代硬體產生</a>，或<a href="https://aka.ms/managed-instance-configurable-backup-retention">將備份保留設定為35天</a>以進行時間點還原。 長期備份保留期（最多10年）仍未啟用，因此您可以使用<a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">僅限複本備份</a>做為替代方案。
  - 新功能可讓您<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">使用 PowerShell 將資料庫異地還原至另一個資料中心</a>、[重新命名資料庫](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[刪除虛擬叢集](sql-database-managed-instance-delete-virtual-cluster.md)。
  - 新的內建[實例參與者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)可讓責任區隔（SoD）符合安全性原則，並遵循企業標準。
  - 受控實例可在下列 Azure Government 區域中正式推出（US Gov 德克薩斯州、US Gov 亞利桑那州），以及中國北部2和中國東部2。 這也適用于下列公用區域：澳大利亞中部、澳大利亞中部2、巴西南部、法國南部、阿拉伯聯合大公國中部、阿拉伯聯合大公國北部、南非北部、南非西部。

## <a name="fixed-known-issues"></a>已修正的已知問題

- **8 月 2019** -受控實例中完全支援自主資料庫。

## <a name="updates"></a>更新

如需 SQL Database 更新和增強功能的清單，請參閱[SQL Database 服務更新](https://azure.microsoft.com/updates/?product=sql-database)。

如需所有 Azure 服務的更新和改善，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>參與內容

若要參與 Azure SQL Database 檔，請參閱檔[參與者指南](https://docs.microsoft.com/contribute/)。
