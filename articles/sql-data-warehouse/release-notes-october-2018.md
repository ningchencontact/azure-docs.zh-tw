---
title: 2018 年 10 月 Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 9160a5f4e3a452682787ff500199e43e7fad0c77
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213684"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Azure SQL 資料倉儲有哪些最新功能？ 2018 年 10 月
Azure SQL 資料倉儲會持續改進。 本文說明 2018 年 10 月導入的新功能和變更。

## <a name="devops-for-data-warehousing"></a>資料倉儲的 DevOps
SQL 資料倉儲 (SQL DW) 高度要求的功能目前為預覽狀態，Visual Studio 中支援資料工具 (SSDT)！ 開發人員小組現在可以透過單一的版本控制程式碼基底執行協同作業，並快速地將變更部署到全球任何執行個體。 有興趣加入嗎？ 此功能目前可供預覽！ 您可以透過造訪 [SQL 資料倉儲 Visual Studio SQL Server Data Tools (SSDT) - 預覽註冊表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)進行註冊。 鑑於需求量很大，我們正在對預覽進行驗收，以確保為客戶提供最佳體驗。 註冊後，我們的目標是在七個工作日內確認您的狀態。

## <a name="row-level-security-generally-available"></a>資料列層級安全性全面上市
Azure SQL 資料倉儲 (SQL DW) 現在支援資料列層級安全性 (RLS)，增加了保護機密資料的強大功能。 藉由引進 RLS，您可以實作安全性原則來控制對資料表中資料列的存取，例如誰可以存取哪些資料列。 RLS 支援這種更細緻的存取控制，而不必重新設計您的資料倉儲。 RLS 簡化了整體安全性模型，因為存取限制邏輯位於資料庫層本身，而不是遠離另一個應用程式中的資料。 RLS 還消除了導入檢視以篩選掉用於存取控制管理之資料列的需要。 我們為所有客戶提供此企業級安全性功能，無需任何額外的費用。

## <a name="advanced-advisors"></a>進階的建議程式
Azure SQL 資料倉儲 (SQL DW) 的進階微調透過額外的資料倉儲相關建議和計量變得更加簡單。 Azure Advisor 還提供其他進階的效能建議，包括：
1.  自適性快取 – 取得何時調整規模以發揮最佳快取使用率的建議。
2.  資料表散發 – 判斷何時複寫資料表以減少資料移動並提升工作負載效能。 
3.  Tempdb – 了解何時調整和設定資源類別，以減少 tempdb 競爭。

資料倉儲計量與 [Azure 監視器](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)的整合更加深入，包括功能更強的可自訂監視圖表，能在概觀刀鋒視窗中提供近乎即時的計量。 當您監視使用量，或是驗證並套用資料倉儲建議時，不再需要離開資料倉儲概觀刀鋒視窗，即可存取 Azure 監視器計量。 此外，也提供新的計量 (例如 tempdb 及自適性快取使用率)，作為您效能建議的補充。

## <a name="advanced-tuning-with-integrated-advisors"></a>使用整合式建議程式進行進階微調
Azure SQL 資料倉儲 (SQL DW) 的進階微調透過額外的資料倉儲相關建議和計量以及入口網站概觀刀鋒視窗的重新設計變得更加簡單，該刀鋒視窗提供了 Azure Advisor 和 Azure 監視器的整合式體驗。

## <a name="accelerated-database-recovery-adr"></a>加速資料庫復原 (ADR)
Azure SQL 資料倉儲加速資料庫復原 (ADR) 現在處於公開預覽狀態。 ADR 是新的 SQL 資料庫引擎功能，藉由完整重新設計目前的復原程序，來大幅改善資料庫可用性 (特別是在有長時間執行的交易時)。 ADR 的主要優點是快速且一致的資料庫復原，以及即時的交易回復。

## <a name="azure-monitor-diagnostics-logs"></a>Azure 監視器診斷記錄檔
藉由直接與 Azure 監視器診斷記錄整合，SQL 資料倉儲 (SQL DW) 現在提供更強的分析工作負載見解。 這項新功能可讓開發人員分析更長一段時間內的工作負載行為，並在充分了解資訊的情況下，做出查詢最佳化或容量管理決策。 我們現在透過 [Azure 監視器診斷記錄檔](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)引進了外部的記錄處理程序，這些記錄檔提供了有關資料倉儲工作負載的額外見解。 只要按一下按鈕，您就可以使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) 為歷程查詢效能疑難排解功能設定診斷記錄檔。 Azure 監視器診斷記錄透過將記錄儲存至儲存體帳戶以供稽核，來支援可自訂的保留期間；也支援將記錄串流至事件中樞，以獲得近乎即時的遙測深入解析功能；以及使用具有記錄查詢之 Log Analytics 來分析記錄的功能。 診斷記錄是由您資料倉儲的遙測檢視所組成，其等同於 SQL 資料倉儲最常使用的效能疑難排解 DMV。 我們已為此初始版本啟用下列系統動態管理檢視的檢視：

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>資料行存放區記憶體管理
隨著壓縮的資料行存放區資料列群組數目增加，管理這些資料列群組的內部資料行區段中繼資料所需的記憶體會隨之增加。  因此，查詢效能和針對某些資料行存放區動態管理檢視 (Dmv) 所執行的查詢可能會降級。  此版本已進行改善，讓這些情況的內部中繼資料大小達到最佳化，進而改善這類查詢的體驗和效能。 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Azure Data Lake Storage Gen2 整合 (GA)
Azure SQL 資料倉儲 (SQL DW) 現在已與 Azure Data Lake Storage Gen2 原生整合。 客戶現在可以使用 ABFS 中的外部資料表將資料載入至 SQL DW。 這項功能可讓客戶與其 Data Lake Storage Gen2 中的 Data Lake 整合。 

## <a name="bug-fixes"></a>錯誤修正

| 標題 | 說明 |
|:---|:---|
| **在 DW2000 等等的資料倉儲上的小型資源類別中，CETAS to Parquet 失敗** | 此修正程式正確地識別出 Create External Table As to Parquet 程式碼路徑中的 null 參考。 |
|**在某些 CTAS 作業中，識別資料行值可能會遺失** | 當 CTASed 到另一個資料表時，可能不會保留識別資料行的值。 在部落格中報告：[https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)。 |
| **在查詢仍在執行時終止工作階段的某些情況下發生內部失敗** | 如果在查詢仍在執行時終止工作階段，則此修正程序會觸發 InvalidOperationException。 |
| **(已在 2018 年 11 月部署) 當客戶嘗試使用 PolyBase 從 ADLS (Gen1) 載入多個小型檔案時，會有次佳的效能體驗。** | 系統效能會在 AAD 安全性權杖驗證期間受到阻礙。 效能問題已藉由啟用安全性權杖的快取來降低影響。 |


## <a name="next-steps"></a>後續步驟
現在您已稍微了解 SQL 資料倉儲，請了解如何快速[建立 SQL 資料倉儲][create a SQL Data Warehouse]。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙][Azure glossary]在您遇到新術語時很有幫助。 或者，也可以看一下其中一些其他 SQL 資料倉儲資源。  

* [客戶成功案例]
* [部落格]
* [功能要求]
* [影片]
* [客戶諮詢小組部落格]
* [Stack Overflow 論壇]
* [Twitter]


[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客戶諮詢小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[客戶成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 論壇]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[影片]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
