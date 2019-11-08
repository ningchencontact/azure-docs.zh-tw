---
title: 什麼是 Azure Synapse Analytics (先前為 SQL DW)？
description: Azure Synapse Analytics (先前為 SQL DW) 是一種無限制的分析服務，可將企業資料倉儲和巨量資料分析整合在一起。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645506"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>什麼是 Azure Synapse Analytics (先前為 SQL DW)？

Azure Synapse 是一種無限制的分析服務，可將企業資料倉儲和巨量資料分析整合在一起。 可讓您自由使用無伺服器隨選或佈建資源，隨意且大規模地查詢您的資料。 Azure Synapse 會將這兩個世界結合在一起，透過整合的經驗擷取、準備、管理和處理資料，以滿足立即的 BI 和機器學習需求

Azure Synapse 有四個元件：
- SQL Analytics：完成以 T-SQL 為基礎的分析 - 正式推出
    - SQL 集區 (依據佈建的 DWU 付費) 
    - SQL 隨選 (依據處理的 TB 量付費) – (預覽)
- Spark：深入整合的 Apache Spark (預覽) 
- 資料整合：混合式資料整合 (預覽)
- Studio：整合的使用者體驗。  (預覽)

> [!NOTE]
> 若要存取 Azure Synapse 的預覽功能，請[在此](https://aka.ms/synapsepreview)要求存取權。 Microsoft 會將所有要求分級，並儘快回應您。

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Azure Synapse 中的 SQL 分析和 SQL 集區

SQL 分析是可正式搭配 Azure Synapse 使用的企業資料倉儲功能。 

SQL 集區代表使用 SQL 分析時所佈建的分析資源集合。 SQL 集區的大小取決於資料倉儲單位 (DWU)。

使用簡單的 [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL 查詢匯入巨量資料，然後使用 MPP 功能來執行高效能分析。 當您進行整合及分析時，SQL Analytics 將會變成您企業可以信賴的真正單一版本，可為您提供更快速且更穩健的深入解析。  

## <a name="key-component-of-a-big-data-solution"></a>巨量資料解決方案的重要元件

在雲端型的端對端巨量資料解決方案中，資料倉儲是重要元件。

![資料倉儲解決方案](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

在雲端資料解決方案中，資料會內嵌至來自各種來源的巨量資料存放區。 一旦位於巨量資料存放區中，Hadoop、Spark 和機器學習演算法就會準備及定型資料。 當資料已準備好進行複雜分析時，SQL 資料倉儲會使用 PolyBase 來查詢巨量資料存放區。 PolyBase 會使用標準 T-SQL 查詢來將資料帶入 SQL Analytics 資料表。
 
SQL Analytics 會將資料儲存到具有單欄式儲存體的關聯式資料表。 這種格式會大幅減少資料儲存體成本，而且可以改善查詢效能。 儲存好資料後，您就可以大規模執行分析。 相較於傳統資料庫系統，分析查詢在數秒鐘的時間內就可完成，不用到數分鐘，或者在數小時內而不用數天。 

分析結果可以移至全球報告資料庫或應用程式。 商務分析師便可充分了解，以進行靈活的商務決策。

## <a name="next-steps"></a>後續步驟

- 探索 [Azure Synapse 架構](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- 快速[建立 SQL 集區](create-data-warehouse-portal.md)
- [載入範例資料][load sample data]。
- 探索[影片](/azure/sql-data-warehouse/sql-data-warehouse-videos)

或者，您也可以看看以下其他 Azure Synapse 資源。  
* 搜尋[部落格]
* 提交[功能要求]
* 搜尋[客戶諮詢小組部落格]
* [建立支援票證]
* 搜尋 [MSDN 論壇]
* 搜尋 [Stack Overflow 論壇]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客戶諮詢小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 論壇]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow 論壇]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
