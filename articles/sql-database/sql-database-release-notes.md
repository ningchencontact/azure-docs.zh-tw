---
title: Azure SQL Database 版本資訊 |Microsoft Docs
description: 深入了解的新功能與在 Azure SQL Database 服務和 Azure SQL Database 文件中的增強功能
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: 04c19c9a8603fee46914eb94b50c4f97e34e9c42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070221"
---
# <a name="sql-database-release-notes"></a>SQL Database 版本資訊

本文列出的新功能與 SQL Database 文件和 SQL Database 服務中的增強功能。 SQL Database 服務的增強功能，請參閱[SQL Database 服務更新](https://azure.microsoft.com/updates/?product=sql-database)。 其他 Azure 服務的增強功能，請參閱 <<c0> [ 服務更新](https://azure.microsoft.com/updates)。

## <a name="features-in-public-preview"></a>在公開預覽中的功能

| 功能 | 詳細資料 |
| ---| --- |
| 使用單一資料庫和彈性集區的加速的資料庫復原 | 如需資訊，請參閱[加速資料庫復原](sql-database-accelerated-database-recovery.md)。|
|相異的近似計數|如需資訊，請參閱[近似 Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)。|
|（在 相容性層級 150） 下的資料列存放區上的批次模式|如需資訊，請參閱[資料列存放區上的批次模式](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)。|
| 資料探索與分類  |如需資訊，請參閱[Azure SQL Database 和 SQL 資料倉儲的資料探索與分類](sql-database-data-discovery-and-classification.md)。|
| 彈性資料庫工作 | 如需資訊，請參閱[建立、 設定和管理彈性工作](elastic-jobs-overview.md)。 |
| 彈性查詢 | 如需資訊，請參閱[彈性查詢概觀](sql-database-elastic-query-overview.md)。 |
| 彈性交易 | [分散式交易的雲端資料庫](sql-database-elastic-transactions-overview.md)。 |
|（在 相容性層級 150） 下的記憶體授與回饋 （資料列模式）|如需資訊，請參閱[記憶體授與意見反應 （資料列模式）](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)。|
| 在 Azure 入口網站中的查詢編輯器 |如需資訊，請參閱[使用 Azure 入口網站的 SQL 查詢編輯器進行連線及查詢資料](sql-database-connect-query-portal.md)。|
| R services/機器學習服務與單一資料庫和彈性集區 |如需資訊，請參閱[Azure SQL Database 中的 Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
| 重新建立受管理的執行個體卸除的資料庫 |如需資訊，請參閱[重新建立 Azure SQL 受控執行個體中卸除資料庫](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)。|
| 受管理的執行個體的複寫 |如需資訊，請參閱[在 Azure SQL Database 受控執行個體資料庫中設定複寫](replication-with-sql-database-managed-instance.md)。|
| 無伺服器計算層級 | 如需資訊，請參閱[SQL Database 無伺服器 （預覽）](sql-database-serverless.md)。|
|SQL Analytics|如需資訊，請參閱[Azure SQL 分析](../azure-monitor/insights/azure-sql.md)。|
|資料表變數延後編譯 （相容性層級 150）|如需資訊，請參閱[資料表變數延後編譯](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)。|
| 受管理的執行個體的威脅偵測 |如需資訊，請參閱[設定威脅偵測，在 Azure SQL Database 受控執行個體](sql-database-managed-instance-threat-detection.md)。|
| 透明資料加密 (TDE 與攜帶您自己金鑰 (BYOK) 與受管理的執行個體) |如需資訊，請參閱[Azure SQL 透明資料加密與 Azure Key Vault 中客戶管理的金鑰：攜帶您自己的金鑰支援](transparent-data-encryption-byok-azure-sql.md)。|
| &nbsp; |

## <a name="june-2019"></a>2019 年 6 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| 4 個 Vcore 第 5 代硬體產生受控執行個體| 如需詳細資訊，請參閱 https://aka.ms/four-cores-sql-mi-update 。 |
| 受管理的執行個體-支援的訂用帳戶使用的 Azure 每月信用額度 Visual Studio 訂閱者 | 如需詳細資訊，請參閱 https://aka.ms/sql-mi-visual-studio-subscribers 。 |
|受控執行個體的較大區域的限制| 如需詳細資訊，請參閱 <<c0> [ 區域的資源限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
| &nbsp; |


## <a name="may-2019"></a>2019 年 5 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|超大規模發行的正式運作的服務層| 如需詳細資訊，請參閱 <<c0> [ 最多 100 TB 的超大規模服務層](sql-database-service-tier-hyperscale.md)並[取得高效能調整您的 Azure 資料庫工作負載，使用超大規模](https://azure.microsoft.com/blog/get-high-performance-scaling-for-your-azure-database-workloads-with-hyperscale/)。|
|發表的公開預覽版的無伺服器計算層級 | 如需詳細資訊，請參閱 < [SQL Database 無伺服器 （預覽）](sql-database-serverless.md)。|
|針對正式發行的 managed 執行個體的時區支援 |如需詳細資訊，請參閱 < [Azure SQL Database 受控執行個體的時區](sql-database-managed-instance-timezone.md)。|
|發行正式運作的受控執行個體的執行個體定序 |如需資訊，請參閱[使用 PowerShell 與 Azure Resource Manager 範本建立 Azure SQL Database 中的受管理的執行個體](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)。|
| 交易記錄檔的速率和目標 IOPS 增加的資料庫使用以 vCore 為基礎的購買模型| 如需詳細資訊，請參閱 <<c0> [ 使用以 vCore 為基礎的購買模型的單一資料庫的資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)並[使用以 DTU 為基礎的購買模型的單一資料庫的資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)。
| &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| 超大規模服務層 docs GA 發行版本更新| 如需詳細資訊，請參閱 <<c0> [ 最多 100 TB 的超大規模服務層](sql-database-service-tier-hyperscale.md)。|
|公開預覽版本所隨附的無伺服器計算層 docs| 如需詳細資訊，請參閱 < [SQL Database 無伺服器 （預覽）](sql-database-serverless.md)。|
| &nbsp; |

## <a name="april-2019"></a>2019 年 4 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| 在公開預覽中的受控執行個體的公用端點| 如需詳細資訊，請參閱 <<c0> [ 使用 Azure SQL Database 受控執行個體，使用公用端點的安全地](sql-database-managed-instance-public-endpoint-securely.md)。|
| 時區支援受管理的執行個體處於公開預覽狀態| 如需詳細資訊，請參閱 < [Azure SQL Database 受控執行個體的時區](sql-database-managed-instance-timezone.md)。|
| 保護 Azure SQL Database 管理的身分識別，第二個公開預覽版本| 請參閱[受管理的身分識別保護 Azure SQL Database 變得更簡單](https://azure.microsoft.com/blog/securing-azure-sql-databases-with-managed-identities-just-got-easier/)。|
| &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| 在公開預覽中的受控執行個體的公用端點| 如需詳細資訊，請參閱 <<c0> [ 使用 Azure SQL Database 受控執行個體，使用公用端點的安全地](sql-database-managed-instance-public-endpoint-securely.md)。|
| 時區支援受管理的執行個體處於公開預覽狀態| 如需詳細資訊，請參閱 < [Azure SQL Database 受控執行個體的時區](sql-database-managed-instance-timezone.md)。 |
| Azure SQL Database 中的資源控管 | 如需詳細資訊，請參閱 < [Azure SQL Database 中的資源控管](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 || &nbsp; |

## <a name="march-2019"></a>2019 年 3 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| 讀取向外延展支援發行正式運作的 Azure SQL database | 如需詳細資訊，請參閱 <<c0> [ 讀取相應放大](sql-database-read-scale-out.md)。|
| &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| 新增的記錄單一資料庫的限制|如需詳細資訊，請參閱 <<c0> [ 單一資料庫 vCore 資源限制](sql-database-vcore-resource-limits-single-databases.md)。|
| 新增的記錄彈性集區和集區的資料庫的限制|如需詳細資訊，請參閱 <<c0> [ 彈性集區虛擬核心資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。|
| 新增的交易記錄檔的速率控管| 加入新內容[交易記錄檔的速率控管](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)。|
| 適用於單一資料庫和彈性集區使用 az.sql 模組的已更新的 PowerShell 範例 | 如需詳細資訊，請參閱 <<c0> [ 單一資料庫和彈性集區的 PowerShell 範例](sql-database-powershell-samples.md#single-database-and-elastic-pools)。|
| &nbsp; |

## <a name="february-2019"></a>2019 年 2 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|建立可繼續的線上索引現已全面上市| 如需詳細資訊，請參閱 < [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)。|
|改善的路由表的受管理的執行個體支援| 如需詳細資訊，請參閱 <<c0> [ 網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。|
|受管理的執行個體中支援的資料庫重新命名 | 如需詳細資訊，請參閱 < [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current)並[sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql)語法。|
|SQL Database 做為來源的 Stream Analytics 參考資料。 | 如需詳細資訊，請參閱 < [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)並[Azure Stream Analytics 現在支援 Azure SQL Database 做為參考資料輸入](https://azure.microsoft.com/blog/azure-stream-analytics-now-supports-azure-sql-database-as-reference-data-input/)。|
|資料移轉小幫手加入受管理的執行個體的支援。 |如需詳細資訊，請參閱 <<c0> [ 在 DMA 中最新消息](https://docs.microsoft.com/sql/dma/dma-whatsnew)。|
|SQL Server 移轉小幫手加入支援目標受管理的執行個體的整備程度評估。 | 如需詳細資訊，請參閱 < [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant)。
|資料移轉服務支援從 Amazon RDS 移轉至受控執行個體 | 如需詳細資訊，請參閱[教學課程：將 RDS SQL Server 移轉到 Azure SQL Database 或 Azure SQL Database 受控執行個體上線使用 DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md)。|
| &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
|正在新增受控執行個體部署選項說明|更新許多文章，以釐清適用於單一資料庫、 彈性集區，以及受管理的執行個體的部署選項。 |
|針對以 DTU 為基礎的購買模型的已更新的 tempdb 大小 | 如需詳細資訊，請參閱 < [SQL Database 中的 Tempdb 資料庫](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。|
|已更新的匯入和匯出 bacpac 檔案的受管理的執行個體的支援| 如需詳細資訊，請參閱 <<c0> [ 從 BACPAC 匯入](sql-database-import.md)並[匯出至 BACPAC](sql-database-export.md)。 |
| &nbsp; |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| 計算資源的其他資料粒度選項 | 一般用途和業務關鍵服務層[單一資料庫](sql-database-vcore-resource-limits-single-databases.md)並[彈性集區](sql-database-vcore-resource-limits-elastic-pools.md)現在有更多更細緻的計算選項。|
| 受管理的執行個體，在 Azure 入口網站中檢視稽核記錄 | 檢視[稽核記錄的受管理的執行個體](sql-database-managed-instance-auditing.md)現在支援在 Azure 入口網站。|
| 重新命名為進階的資料安全性的進階威脅偵測功能 | 若要重新命名的進階威脅偵測功能[進階資料安全性](sql-advanced-threat-protection.md)單一資料庫、 彈性集區和受管理的執行個體。 |
| &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| 受管理的執行個體和異動複寫 | 已新增有關使用的文件[受管理的執行個體的異動複寫](replication-with-sql-database-managed-instance.md)。 |
| 已加入 Azure AD 受管理的執行個體教學課程 | 這[受管理的執行個體與 Azure AD](sql-database-managed-instance-aad-security-tutorial.md)教學課程會示範您必須設定並測試管理的執行個體安全性使用 Azure AD 登入。 |
| 使用 Transact SQL 指令碼的工作自動化的更新的內容 | 更新並釐清使用的內容[使用 Transact SQL 指令碼的作業自動化](sql-database-job-automation-overview.md)單一資料庫、 彈性集區和受管理的執行個體。 |
| 更新受管理的執行個體的安全性內容 | 更新並釐清的內容[managed 執行個體的安全性模型](sql-database-security-overview.md)，並使用單一資料庫和彈性集區的安全性模型來說。 |
| 重新整理所有的快速入門和教學課程 | 快速入門和教學課程中的所有[文件](https://docs.microsoft.com/azure/sql-database)已更新並重新整理，以符合在 Azure 入口網站中的變更。 |
| 已新增的快速入門概觀指南 | 新增的快速入門概觀指南[單一資料庫](sql-database-quickstart-guide.md)至於[受管理執行個體](sql-database-managed-instance-quickstart-guide.md)。 |
| 已新增的 SQL Database 相關術語字彙 | 這[術語字彙](sql-database-glossary-terms.md)文章提供明確的 SQL Database 的條款和說明內容中的詞彙的主要概念頁面的連結清單。 |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>參與內容的改進

Azure SQL 文件集是開放原始碼。 在開啟的工作提供數個優點：

- 開放原始碼存放庫計畫，以取得在最需要哪些 docs 的意見反應。
- 在發佈最有用的內容，在我們的第一個版本上，檢閱開放原始碼存放庫。
- 開放原始碼存放庫更新，以讓您更輕鬆地持續改善內容。

若要參與 Azure SQL Database 文件內容，請參閱[Microsoft Docs 參與者指南概觀](https://docs.microsoft.com/contribute/)。 上的使用者體驗[docs.microsoft.com](https://docs.microsoft.com/)整合[GitHub](https://github.com/)直接以更容易的工作流程。 著手[編輯您正在檢視文件](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents)。 或者，您也可以幫助[檢閱新主題](https://docs.microsoft.com/contribute/#review-open-prs)，或[建立品質問題](https://docs.microsoft.com/contribute/#create-quality-issues)。
