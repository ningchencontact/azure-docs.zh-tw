---
title: Azure SQL 数据库发行说明 | Microsoft Docs
description: 了解 Azure SQL 数据库服务和 Azure SQL 数据库文档中的新功能和改进
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: carlrab
ms.openlocfilehash: 923e475cd690902c61c2f89578c2c62effe4cd86
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406573"
---
# <a name="sql-database-release-notes"></a>SQL 数据库发行说明

本文列出了 SQL 数据库服务和 SQL 数据库文档中的新功能和改进。 SQL Database 服務的增強功能，請參閱[SQL Database 服務更新](https://azure.microsoft.com/updates/?product=sql-database)。 其他 Azure 服務的增強功能，請參閱 <<c0> [ 服務更新](https://azure.microsoft.com/updates)。

## <a name="features-in-public-preview"></a>在公開預覽中的功能

| 功能 | 詳細資料 |
| ---| --- |
| 無伺服器計算層級 | 如需資訊，請參閱[SQL Database 無伺服器 （預覽）](sql-database-serverless.md)。|
| 彈性資料庫工作 | 如需資訊，請參閱[建立、 設定和管理彈性工作](elastic-jobs-overview.md) |
| 彈性交易 | [跨雲端資料庫的分散式交易](sql-database-elastic-transactions-overview.md) |
| 彈性查詢 | 如需資訊，請參閱[彈性查詢概觀](sql-database-elastic-query-overview.md) |
| 受管理的執行個體的複寫 |如需資訊，請參閱[在 Azure SQL Database 受控執行個體資料庫中設定複寫](replication-with-sql-database-managed-instance.md)|
| 受管理的執行個體的執行個體定序 |如需資訊，請參閱[使用 PowerShell 與 Azure Resource Manager 範本建立 Azure SQL Database 中的受管理的執行個體](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| R services/機器學習服務與單一資料庫和彈性集區 |如需資訊，請參閱[Azure SQL Database 中的 Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)|
| 使用單一資料庫和彈性集區的加速的資料庫復原 | 如需資訊，請參閱[加速資料庫復原](sql-database-accelerated-database-recovery.md)|
| 資料探索與分類  |如需資訊，請參閱[Azure SQL Database 和 SQL 資料倉儲的資料探索與分類](sql-database-data-discovery-and-classification.md)|
| 透明資料加密 (TDE 與攜帶您自己金鑰 (BYOK) 與受管理的執行個體) |如需資訊，請參閱[Azure SQL 透明資料加密與 Azure Key Vault 中客戶管理的金鑰：攜帶您自己的金鑰支援](transparent-data-encryption-byok-azure-sql.md)|
| 重新建立受管理的執行個體卸除的資料庫 |如需資訊，請參閱[重新建立 Azure SQL 受控執行個體中卸除資料庫](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)|
| 受管理的執行個體的威脅偵測 |如需資訊，請參閱[設定威脅偵測，在 Azure SQL Database 受控執行個體](sql-database-managed-instance-threat-detection.md)|
| 使用單一資料庫的超大規模服務層 |如需資訊，請參閱[達 100 TB 的超大規模服務層](sql-database-service-tier-hyperscale.md)|
| 在 Azure 入口網站中的查詢編輯器 |如需資訊，請參閱[使用 Azure 入口網站的 SQL 查詢編輯器進行連線及查詢資料](sql-database-connect-query-portal.md)|
|相異的近似計數|如需資訊，請參閱[近似 Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)|
|（在 相容性層級 150） 下的資料列存放區上的批次模式|如需資訊，請參閱[資料列存放區上的批次模式](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)|
|（在 相容性層級 150） 下的記憶體授與回饋 （資料列模式）|如需資訊，請參閱[記憶體授與意見反應 （資料列模式）](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)|
|資料表變數延後編譯 （相容性層級 150）|如需資訊，請參閱[資料表變數延後編譯](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)|
|SQL Analytics|如需資訊，請參閱[Azure SQL 分析](../azure-monitor/insights/azure-sql.md)|
| 受管理的執行個體的時區支援|如需詳細資訊，請參閱[Azure SQL Database 受控執行個體的時區](sql-database-managed-instance-timezone.md)|
|||

## <a name="may-2019"></a>2019 年

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|超大規模資料庫服務層級| 如需詳細資訊，請參閱 <<c0> [ 最多 100 TB 的超大規模服務層](sql-database-service-tier-hyperscale.md)。|
|無伺服器計算層級| 如需詳細資訊，請參閱 < [SQL Database 無伺服器 （預覽）](sql-database-serverless.md)。|


## <a name="april-2019"></a>2019 年 4 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| 受控執行個體的公用端點 | 如需詳細資訊，請參閱[使用 Azure SQL Database 受控執行個體，安全地使用公用端點](sql-database-managed-instance-public-endpoint-securely.md)
| 受控執行個體的時區支援 | 如需詳細資訊，請參閱[Azure SQL Database 受控執行個體 （預覽） 的時區](sql-database-managed-instance-timezone.md)

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| 受控執行個體的公用端點 | 如需詳細資訊，請參閱[使用 Azure SQL Database 受控執行個體，安全地使用公用端點](sql-database-managed-instance-public-endpoint-securely.md)
| 受控執行個體的時區支援 | 如需詳細資訊，請參閱[Azure SQL Database 受控執行個體 （預覽） 的時區](sql-database-managed-instance-timezone.md)

## <a name="march-2019"></a>2019 年 3 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| 正式運作：Azure SQL Database 的讀取縮放支援 | 如需詳細資訊，請參閱[讀取相應放大](sql-database-read-scale-out.md)|
| &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| 受管理的執行個體的時區支援|如需詳細資訊，請參閱[Azure SQL Database 受控執行個體的時區](sql-database-managed-instance-timezone.md)|
| 添加了单一数据库的日志限制|有关详细信息，请参阅[单一数据库 vCore 资源限制](sql-database-vcore-resource-limits-single-databases.md)。|
| 添加了弹性池和共用数据库的日志限制|有关详细信息，请参阅[弹性池 vCore 资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。|
| 添加了事务日志速率调控| 为[事务日志速率调控](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)添加了新内容。|
| 更新了单一数据库和弹性池的 PowerShell 示例，使其能够使用 az.sql 模块 | 有关详细信息，请参阅[单一数据库和弹性池的 PowerShell 示例](sql-database-powershell-samples.md#single-database-and-elastic-pools)。|
| &nbsp; |

## <a name="february-2019"></a>2019 年 2 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|建立可繼續的線上索引現已全面上市| 如需詳細資訊，請參閱 < [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)。|
|改善的路由表的受管理的執行個體支援| 如需詳細資訊，請參閱 <<c0> [ 網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。|
|托管实例支持数据库重命名 | 有关更多详细信息，请参阅 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) 和 [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) 语法。|
|SQL 数据库充当流分析的参考数据源。 | 有关详细信息，请参阅[流分析](https://azure.microsoft.com/services/stream-analytics/)。|
|資料移轉小幫手加入受管理的執行個體的支援。 |如需詳細資訊，請參閱 <<c0> [ 在 DMA 中最新消息](https://docs.microsoft.com/sql/dma/dma-whatsnew)。|
|SQL Server 移轉小幫手加入支援目標受管理的執行個體的整備程度評估。 | 如需詳細資訊，請參閱 < [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant)。
|資料移轉服務支援從 Amazon RDS 移轉至受控執行個體 | 如需詳細資訊，請參閱[教學課程：將 RDS SQL Server 移轉到 Azure SQL Database 或 Azure SQL Database 受控執行個體上線使用 DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md)。|
| &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
|正在新增受控執行個體部署選項說明|更新許多文章，以釐清適用於單一資料庫、 彈性集區，以及受管理的執行個體的部署選項。 |
|更新了基于 DTU 的购买模型的 tempdb 大小 | 有关详细信息，请参阅 [SQL 数据库中的 Tempdb 数据库](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。|
|已更新的匯入和匯出 bacpac 檔案的受管理的執行個體的支援| 如需詳細資訊，請參閱 <<c0> [ 從 BACPAC 匯入](sql-database-import.md)並[匯出至 BACPAC](sql-database-export.md)。 |
| &nbsp; |


## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| 计算资源的其他粒度选项 | [单一数据库](sql-database-vcore-resource-limits-single-databases.md)和[弹性池](sql-database-vcore-resource-limits-elastic-pools.md)的“常规用途”和“业务关键”服务层现在有更多细致的计算选项。|
| 受管理的執行個體，在 Azure 入口網站中檢視稽核記錄 | 檢視[稽核記錄的受管理的執行個體](sql-database-managed-instance-auditing.md)現在支援在 Azure 入口網站。|
| 高级威胁检测功能已重命名为“高级数据安全” | 若要重新命名的進階威脅偵測功能[進階資料安全性](sql-advanced-threat-protection.md)單一資料庫、 彈性集區和受管理的執行個體。 |
| &nbsp; |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| 受管理的執行個體和異動複寫 | 已新增有關使用的文件[受管理的執行個體的異動複寫](replication-with-sql-database-managed-instance.md) |
| 已加入 Azure AD 受管理的執行個體教學課程 | 這[受管理的執行個體與 Azure AD](sql-database-managed-instance-aad-security-tutorial.md)教學課程會示範您必須設定並測試管理的執行個體安全性使用 Azure AD 登入。 |
| 更新了使用 Transact-SQL 脚本进行的作业自动化的内容 | 更新並釐清使用的內容[使用 Transact SQL 指令碼的作業自動化](sql-database-job-automation-overview.md)單一資料庫、 彈性集區和受管理的執行個體 |
| 更新受管理的執行個體的安全性內容 | 更新並釐清的內容[managed 執行個體的安全性模型](sql-database-security-overview.md)，和來說，使用單一資料庫和彈性集區的安全性模型 |
| 刷新了所有快速入门和教程 | [文档](https://docs.microsoft.com/azure/sql-database)中的所有快速入门和教程都已根据 Azure 门户中的更改进行更新和刷新 |
| 添加了快速入门概述指南 | 新增的快速入門概觀指南[單一資料庫](sql-database-quickstart-guide.md)至於[受管理執行個體](sql-database-managed-instance-quickstart-guide.md) |
| 添加了 SQL 数据库术语表 | 此[术语表](sql-database-glossary-terms.md)文章提供了一个最终列表，其中包含 SQL 数据库术语以及在上下文中解释术语的主要概念页的链接。 |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>参与内容改进

Azure SQL 文档集是开源的。 采用开源方式有几项优势：

- 开源存储库公开进行计划，可以通过反馈了解用户最需要什么样的文档。
- 开源存储库公开进行评审，可以在初次发布时就发布最有用的内容。
- 开源存储库公开进行更新，更容易持续改进内容。

若要參與 Azure SQL Database 文件內容，請參閱[Microsoft Docs 參與者指南概觀](https://docs.microsoft.com/contribute/)。 上的使用者體驗[docs.microsoft.com](https://docs.microsoft.com/)整合[GitHub](https://github.com/)直接以更容易的工作流程。 著手[編輯您正在檢視文件](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents)。 或者，您也可以幫助[檢閱新主題](https://docs.microsoft.com/contribute/#review-open-prs)，或[建立品質問題](https://docs.microsoft.com/contribute/#create-quality-issues)。
