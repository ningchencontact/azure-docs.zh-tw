---
title: 針對 Microsoft Azure SQL Database 的連線問題進行疑難排解 |Microsoft Docs
description: 針對 Microsoft Azure SQL Database 的連線問題進行疑難排解
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 3717b36e37f7d9cca206b5a9e8e3cded50393748
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974418"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>針對 Microsoft Azure SQL Database 的連線問題進行疑難排解

Azure SQL Database 的連線失敗時，您會收到錯誤訊息。 連線問題可能起因於 SQL Azure 資料庫重新設定、防火牆設定、連線逾時或不正確的登入資訊。 此外，如果達到某些 Azure SQL Database 資源的最大限制，您就無法連接到 Azure SQL Database。

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>錯誤 40613：伺服器 < y > 上的資料庫 < x > 目前無法使用

**詳細錯誤**

``40613: Database <DBname> on server <server name> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '<Tracing ID>'.``

若要解決此問題︰

1. 檢查[Microsoft Azure 服務儀表板](https://status.azure.com/status)是否有任何已知的中斷。 
2. 如果沒有任何已知的中斷，請前往[Microsoft Azure 支援網站](http://azure.microsoft.com/support/options)開啟支援案例。

如需詳細資訊，請參閱[疑難排解「伺服器上的資料庫目前無法使用」錯誤](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors)。

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>建立連接到 SQL Server 時發生網路相關或實例特定的錯誤

發生此問題的原因是應用程式無法連接到伺服器。

若要解決此問題，請嘗試下一節中的步驟（依序），其標題為**修正常見連接問題的步驟**。

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>找不到或無法存取伺服器（錯誤26、40、10053）

### <a name="error-26-error-locating-serverinstance-specified"></a>錯誤26：尋找指定的伺服器/實例時發生錯誤

**詳細錯誤**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>錯誤40：無法開啟 SQL Server 連線

**詳細錯誤**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>錯誤10053：從伺服器接收結果時發生傳輸層級錯誤。

**詳細錯誤**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

這些問題發生的原因是應用程式無法連接到伺服器。

若要解決此問題，請嘗試下一節中的步驟（依序），其標題為**修正常見連接問題的步驟**。

## <a name="cannot-connect-to--servername--due-to-firewall-issues"></a>因為防火牆問題，所以無法連線至 < servername >

### <a name="error-40615-cannot-connect-to--servername-"></a>錯誤 40615：無法連接到 < servername >

若要解決此問題，請[使用 Azure 入口網站在 SQL Database 上設定防火牆設定。](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>錯誤5：無法連接到 < servername >

若要解決此問題，請確定在用戶端與網際網路之間的所有防火牆上，埠1433已開放供輸出連線之用。 

如需詳細資訊，請參閱[設定 Windows 防火牆以允許 SQL Server 存取](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。

## <a name="unable-to-login-to-the-server-errors-18456-40531"></a>無法登入伺服器（錯誤18456、40531）

### <a name="login-failed-for-user--user-name-"></a>使用者 ' < 使用者名稱 > ' 的登入失敗

**詳細錯誤**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

若要解決此問題，請洽詢您的服務系統管理員，為您提供有效的 SQL 使用者名稱和密碼。

一般而言，服務系統管理員可以使用下列步驟新增登入：

1. 使用 SQL Server Management Studio （SSMS）登入伺服器。
2. 使用下列 SQL 查詢檢查登入名稱是否停用：

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. 如果對應的名稱已停用，使用下列陳述式啟用它： 

   ```
   Alter login <User name> enable
   ```

4. 如果 SQL 登入使用者名稱不存在，請使用 SSMS 建立它：

   1. 按兩下 [安全性] 將它展開。 
   2. 以滑鼠右鍵按一下 [登入]，然後選取 [新增登入]。 
   3. 在產生的指令碼中有預留位置，您可以編輯並執行下列 SQL 查詢：
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. 按兩下 [資料庫]。 
6. 選取您要授與使用者權限的資料庫。
7. 按兩下 [安全性]。 
8. 以滑鼠右鍵按一下 [登入]，然後選取 [新增使用者]。 
9. 在產生的指令碼中有預留位置，您可以編輯並執行下列 SQL 查詢： 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > 您也可以使用 `sp_addrolemember`，將特定使用者對應至特定的資料庫角色。 

如需詳細資訊，請參閱 [管理 Azure SQL Database 中的資料庫和登入](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)。

## <a name="connection-timeout-expired-errors"></a>連接逾時過期錯誤

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>SqlClient. SqlException （0x80131904）：連接逾時時間已過期。

**詳細錯誤**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>SqlClient. SqlException （0x80131904）：過期時間。

**詳細錯誤**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.web. EntityException：基礎提供者開啟時失敗。

**詳細錯誤**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>無法連接到 < 伺服器名稱 >。 ' '

**詳細錯誤**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

這些例外狀況可能是因為連接或查詢問題所造成。 若要確認此錯誤是因為連線問題所造成，請檢查下面的一節，**確認錯誤是否為連線問題所致**：

由於應用程式無法連接到伺服器，因此會發生連接逾時。 若要解決此問題，請嘗試下一節中的步驟（依序），其標題為**修正常見連接問題的步驟**。

## <a name="transient-errors-errors-40197-40545"></a>暫時性錯誤（錯誤40197、40545）

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>錯誤40197：服務處理您的要求時發生錯誤。 請再試一次。 錯誤碼 < 程式碼 >

發生此問題的原因是因為在後端進行重新設定/容錯移轉期間發生暫時性錯誤。

若要解決此問題，請稍待片刻後再試一次。 除非問題持續發生，否則不需要任何支援案例。

最佳做法是確保重試邏輯已就緒。 如需重試邏輯的詳細資訊，請參閱針對[暫時性錯誤和連接錯誤進行疑難排解以 SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)。

## <a name="connection-terminated-due-to-hitting-some-system-defined-limit"></a>因為達到一些系統定義的限制，所以連接已終止

### <a name="error-10928-resource-id-d"></a>錯誤 10928：資源識別碼：%d。

**詳細錯誤**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

若要解決此問題，請嘗試下列其中一種方法：

* 確認是否有長時間執行的查詢：

  > [!NOTE]
  > 這是極簡的方法，可能不一定能解決問題。

  1. 藉由執行下列 SQL 查詢，檢查[_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)視圖以查看任何封鎖的要求：

     ```
     SELECT * FROM dm_exec_requests
     ```

  2. 判斷 head 封鎖程式的**輸入緩衝區**。
  3. 調整 [head 封鎖器] 查詢。

    如需深入的疑難排解程式，請參閱[我的查詢在雲端中是否正常運作？](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

* 如果資料庫在解決封鎖和長時間執行的查詢時一直達到其限制，請考慮升級至其中一個新的預覽版本（例如[Standard 或 Premium edition](https://azure.microsoft.com/pricing/details/sql-database/)）。

如需 SQL Database 定價選項審查[Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)的詳細資訊。

如需動態管理檢視的詳細資訊，請參閱[系統動態管理檢視](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)。

如需有關此錯誤訊息的詳細資訊，請參閱[Azure SQL Database 伺服器 SQL Database 資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)。

### <a name="error-10929-resource-id-1"></a>錯誤10929：資源識別碼：1.

**詳細錯誤**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

如需有關此錯誤的詳細資訊，請參閱[SQL Database 用戶端程式的錯誤訊息](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)

### <a name="error-40501-the-service-is-currently-busy"></a>錯誤40501：服務目前忙碌中

**詳細錯誤**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

這是引擎節流錯誤，指出已超過資源限制。

如需資源限制的詳細資訊，請參閱[資料庫伺服器資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)。

### <a name="error-40544--the-database-has-reached-its-size-quota"></a>錯誤40544：資料庫已達到其大小配額

**詳細錯誤**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

當資料庫已達到其大小配額時，就會發生此錯誤。

下列步驟可協助您解決問題，或提供您可以考慮的其他選項。

1. 使用 Azure 入口網站中的 儀表板，檢查資料庫的目前大小。

   > [!NOTE]
   > 若要找出哪些資料表耗用最多空間和潛在的清除候選項目，您可以使用下列 SQL 查詢：

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. 如果目前的大小不超過您的版本支援的大小上限，您可以使用 ALTER DATABASE 來增加 MAXSIZE 設定。 
3. 如果資料庫大小已經超過您的版本支援的大小上限，您可以採取下列其中一個步驟：
   1. 執行一般資料庫清除活動（使用截斷/刪除等來清除不必要的資料，或使用 SSIS、bcp 等來移動資料）
   2. 資料分割或刪除資料、卸除索引，或參閱可能解決方式的文件。 
   
   *  如需資料庫調整，請參閱[調整單一資料庫資源](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale)和[調整彈性集區資源](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)。

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>錯誤40549：工作階段已終止，因為您有長時間執行的交易。

**詳細錯誤**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

如果您重複遇到此錯誤訊息，請嘗試下列步驟來解決此問題： 

1. 藉由執行下列 SQL 腳本，檢查 _exec_requests view 來查看任何具有 total_elapsed_time 資料行值高的已開啟會話：

   ```
   SELECT * FROM dm_exec_requests
   ```
2. 判斷長時間執行之查詢的輸入緩衝區。 
3. 調整查詢。

也請考慮批次處理您的查詢。 如需批次處理的詳細資訊，請參閱[如何使用批次處理來改善 SQL Database 應用程式效能](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)。

如需深入的疑難排解程式，請參閱[我的查詢在雲端中是否正常運作？](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>錯誤40551：會話已終止，因為過度使用 TEMPDB。

**詳細錯誤**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

若要解決此問題，請遵循下列步驟：

1. 變更查詢以減少臨時表空間的使用量。 
2. 在不再需要暫存物件之後，將其卸載。 
3. 截斷資料表或移除未使用的資料表。

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>錯誤40552：已終止工作階段，因為過度使用交易記錄檔空間。

**詳細錯誤**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

若要解決此問題，請遵循下列方法： 

* 此問題發生的原因是插入、更新或刪除作業。 嘗試藉由執行批次處理或分割成多個較小的交易，以減少立即操作的資料列數目。
* 發生此問題的原因是索引重建作業。 請確定您遵守下列公式：資料表中受影響的資料列數目 * （以位元組為單位更新的平均欄位大小 + 80） < 2 GB

  > [!NOTE]
  > 對於索引重建，已更新之欄位的平均大小應該由平均索引大小來取代。

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>錯誤40553：已終止工作階段，因為過度使用記憶體。

**詳細錯誤**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

若要解決此問題，請嘗試優化查詢。

如需深入的疑難排解程式，請參閱[我的查詢在雲端中是否正常運作？](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>無法開啟登入所要求的資料庫「主要」。 登入失敗。

發生此問題的原因是該帳戶沒有 master 資料庫的存取權限。 不過，根據預設，SQL Server Management Studio （SSMS）會嘗試連接到 master 資料庫。

若要解決此問題，請依照下列步驟執行︰

1. 在 SSMS 的登入畫面上，按一下 [**選項**]，然後按一下 [**連接屬性**]。 
2. 在 **[連接到資料庫]** 中，輸入使用者的預設資料庫名稱做為預設登入資料庫，然後按一下 **[連接]** 。

   ![cannot-open-database-master .png](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-due-to-a-connectivity-issue"></a>確認錯誤是否因連線問題所致

若要確認錯誤是否是因為連線問題所造成，請針對顯示呼叫以開啟連接的畫面格（請注意**SqlConnection**類別的參考），檢查堆疊追蹤：

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

因查詢問題而發生例外狀況時，您會注意到類似下列的呼叫堆疊（請注意**SqlCommand**類別的參考）。 在這些情況下，請[調整您的查詢](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
如需微調效能的其他指引，請參閱下列內容：

* [如何維護 Azure SQL 索引和統計資料](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Azure SQL Database 中的手動微調查詢效能](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [使用動態管理檢視監視效能 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [操作 Azure SQL Database 中的查詢存放區](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>修正常見連接問題的步驟

1. 請確定 TCP IP 已啟用為應用程式伺服器上的用戶端通訊協定。 如需詳細資訊，請參閱[設定用戶端通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols)。 在未安裝 SQL Server 工具的應用程式伺服器上，執行**cliconfg.exe** （SQL Server 用戶端網路公用程式），檢查是否已啟用 TCP IP。 
2. 請檢查應用程式的連接字串，確定已正確設定。 例如，請確定連接字串指定了正確的埠（1433）和完整的伺服器名稱。
請參閱[取得 SQL Server 連接資訊](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information)。
3. 請嘗試增加連接**超時**。 Microsoft 建議使用至少30秒的連接逾時。 
4. 使用[SQL Server Management Studio （SSMS）](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)、UDL 檔案、ping 和 telnet，測試應用程式伺服器與 Azure SQL 資料庫之間的連線能力。 如需詳細資訊，請參閱[疑難排解 SQL Server 連線問題](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)和連線[問題的診斷](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)。

   > [!NOTE]
   > 做為疑難排解步驟，您也可以嘗試在不同的用戶端電腦上測試連線能力。

5. 最佳做法是確保重試邏輯已就緒。 如需重試邏輯的詳細資訊，請參閱針對[暫時性錯誤和連接錯誤進行疑難排解以 SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)。

如果先前的步驟無法解決您的問題，請嘗試收集更多資料和連絡人支援。 如果您的應用程式是雲端服務，請啟用記錄功能。 此步驟會傳回失敗的 UTC 時間戳記。 此外，SQL Azure 會傳回追蹤識別碼。 [Microsoft 客戶支援服務](http://azure.microsoft.com/support/options/)可以使用此資訊。 

如需有關如何啟用記錄的詳細資訊，請參閱[在 Azure App Service 中啟用 web 應用程式的診斷記錄](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)。

**相關檔**

* [Azure SQL 連線架構](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Azure SQL Database 和資料倉儲網路存取控制](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)