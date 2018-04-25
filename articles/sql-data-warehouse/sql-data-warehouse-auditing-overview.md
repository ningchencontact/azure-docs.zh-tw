---
title: Azure SQL 資料倉儲中的稽核 | Microsoft Docs
description: 了解稽核，以及如何在 Azure SQL 資料倉儲中設定稽核。
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: rortloff
ms.reviewer: jrj
ms.openlocfilehash: 7bce59114dcfef7f9faa7f112c62dbb88044f2ab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲中的稽核

了解稽核，以及如何在 Azure SQL 資料倉儲中設定稽核。

## <a name="what-is-auditing"></a>稽核是什麼？
SQL 資料倉儲稽核可讓您將資料庫中的事件記錄到 Azure 儲存體帳戶中的稽核記錄。 稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。 SQL 資料倉儲稽核也整合了 Microsoft Power BI，具備報告和分析的功能。

稽核工具啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 信任中心</a>。

## <a id="subheading-1"></a>稽核的基本概念
SQL 資料倉儲資料庫稽核可讓您：

* **保留** 所選事件的稽核記錄。 您可以定義要稽核的資料庫動作類別。
* **報告** 資料庫活動。 您可以使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
* **分析** 報告。 您可以尋找可疑事件、異常活動及趨勢。

您可以設定下列事件類別的稽核：

**一般 SQL** 和**參數化 SQL**，且為它們所收集的稽核記錄歸類為  

* **資料存取**
* **結構描述變更 (DDL)**
* **資料變更 (DML)**
* **帳戶、角色和權限 (DCL)**
* **預存程序**、**登入**以及**交易管理**。

針對每個事件類別，分別設定**成功**和**失敗**作業的稽核。

如需已稽核之活動和事件的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">稽核記錄格式參考 (doc 檔案下載)</a>。

稽核記錄會儲存在 Azure 儲存體帳戶。 您可以定義稽核記錄保留期間。

您可以針對特定資料庫定義稽核原則，或將稽核原則定義為預設伺服器原則。 預設伺服器稽核原則會套用至伺服器上的所有資料庫，其中該伺服器並沒有定義特定覆寫的資料庫稽核原則。

在設定稽核之前，請檢查您是否正在使用[「下層用戶端」](sql-data-warehouse-auditing-downlevel-clients.md)。

## <a id="subheading-2"></a>設定資料庫的稽核
1. 啟動 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。
2. 移至您想要進行稽核之 SQL 資料倉儲的 [設定]。 選取 [稽核與威脅偵測]。
   
    ![][1]
3. 接下來，按一下 [開啟]  按鈕，來啟用稽核。
   
    ![][3]
4. 在 [稽核組態] 面板中，選取 [儲存體詳細資料]，以開啟 [稽核記錄儲存體] 面板。 選取記錄檔的 Azure 儲存體帳戶和保留期限。 
>[!TIP]
>在所有稽核的資料庫中使用相同的儲存體帳戶，以充分利用預先設定的報告範本。
   
    ![][4]
5. 按一下 [確定]  按鈕，以儲存儲存體的詳細資料組態。
6. 在 [依事件記錄] 下，按一下 [成功] 和 [失敗] 以記錄所有事件，或選擇個別的事件類別。
7. 如果您正在為資料庫設定稽核，您可能需要變更用戶端的連接字串，以確保系統會正確擷取資料稽核。 請參閱 [修改連接字串中的伺服器 FDQN](sql-data-warehouse-auditing-downlevel-clients.md) 主題，以取得下層用戶端連線。
8. 按一下 [確定]。

## <a id="subheading-3"></a>分析稽核記錄和報告
在安裝期間，會在所選擇的 Azure 儲存體帳戶之具有 **SQLDBAuditLogs** 首碼的 [儲存資料表] 集合中彙總稽核記錄。 您可以使用工具 (例如 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure 儲存體總管</a>) 來檢視記錄檔。

預先設定的儀表板報告範本會以<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">可下載的 Excel 試算表</a>形式提供，以協助您快速分析記錄資料。 若要在稽核記錄上使用範本，您需要 Excel 2013 (或更新版本) 和 Power Query (您可從<a href="http://www.microsoft.com/download/details.aspx?id=39379">此處</a>下載)。

範本中包含虛構的範例資料，您可以設定 Power Query 直接從 Azure 儲存體帳戶匯入稽核記錄。

## <a id="subheading-4"></a>儲存體金鑰重新產生
在生產中，您可能會定期重新整理儲存體金鑰。 重新整理金鑰時，您需要儲存該原則。 程序如下：

1. 在 [稽核組態] 面板 (已在先前的設定稽核一節中說明) 中，將 [儲存體存取金鑰] 從 [主要] 變更為 [次要]，然後 [儲存]。

   ![][4]
2. 移至 [儲存體組態] 面板，並**重新產生**「主要存取金鑰」。
3. 返回 [稽核組態] 面板， 
4. 將 [儲存體存取金鑰] 從 [次要] 切換回 [主要]，並按 [儲存]。
4. 返回儲存體 UI 並**重新產生**「次要存取金鑰」 (為了下一個金鑰重新整理週期做準備)。

## <a id="subheading-5"></a>自動化 (PowerShell/REST API)
您也可以使用下列自動化工具在 Azure SQL 資料倉儲中設定稽核：

* **PowerShell Cmdlet**：

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>下層用戶端支援稽核與動態資料遮罩
稽核可與支援 TDS 重新導向的 SQL 用戶端搭配使用。

實作 TDS 7.4 的任何用戶端應該也支援重新導向。 例外包括其中未完全支援重新導向功能的 JDBC 4.0，和其中未實作重新導向的 Tedious for Node.JS。

針對支援 TDS 版本 7.3 與更舊版本的「下層用戶端」，請修改連接字串中的伺服器 FQDN，如下所示：

- 連接字串中的原始伺服器 FQDN：<*伺服器名稱*>.database.windows.net
- 連接字串中已修改的伺服器 FQDN：<*伺服器名稱*>.database.**secure**.windows.net

「舊版用戶端」的部分清單包括：

* .NET 4.0 和以下版本，
* ODBC 10.0 和以下版本。
* JDBC (雖然 JDBC 支援 TDS 7.4，但並未完整支援 TDS 重新導向功能)
* Tedious (適用於 Node.JS)

**備註：** 前述伺服器 FDQN 修改可能會對於套用 SQL Server 層級稽核原則有所助益，不需要每個資料庫中的組態步驟 (暫存緩和)。     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


