---
title: "修正 SQL 連接錯誤、暫時性錯誤 |Microsoft Docs"
description: "了解如何在 Azure SQL Database 中排解、診斷和防止 SQL 連接錯誤或暫時性錯誤。"
keywords: "sql 連接, 連接字串, 連接問題, 暫時性錯誤, 連接錯誤"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: efb35451-3fed-4264-bf86-72b350f67d50
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/29/2017
ms.author: daleche
ms.openlocfilehash: 7d393cd08ef5c20ef680e4e1ab3aded191abe932
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>排解、診斷和防止 SQL Database 的 SQL 連接錯誤和暫時性錯誤
本文描述如何防止、排解、診斷和減少您的用戶端應用程式在與 Azure SQL Database 互動時發生的連接錯誤和暫時性錯誤。 了解如何設定重試邏輯、建置連接字串和調整其他連接設定。

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>暫時性錯誤 (暫時性故障)
暫時性錯誤，也就是暫時性的錯誤，有很快就將自行解決根本原因。 當 Azure 系統快速地將硬體資源轉移到負載平衡更好的各種工作負載時，偶爾會發生暫時性錯誤。 在少於 60 秒內完成大部分的這些重新組態事件。 在此重新設定時間範圍內，您可能需要 SQL Database 連線問題。 建置連接到 SQL Database 的應用程式時，應該要預期這些暫時性錯誤。 若要處理它們，請而不是為應用程式錯誤的使用者面對這些程式碼中實作重試邏輯。

如果您的用戶端程式使用 ADO.NET，程式就會通知有關暫時性錯誤所擲回**SqlException**。 比較**數目**屬性位於最上方的發行項的暫時性錯誤的清單進行比對[SQL 資料庫用戶端應用程式的 SQL 錯誤代碼，](sql-database-develop-error-messages.md)。

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>與命令的連接
SQL 連接重試一次或一次，根據下列建立：

* **連接嘗試期間，發生暫時性錯誤**： 延遲之後的幾秒，重試連線。
* **SQL 查詢命令期間，發生暫時性錯誤**： 不立即重試命令。 相反地的延遲之後，剛建立的連線。 然後重試命令。


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>暫時性錯誤的重試邏輯
用戶端程式若包含重試邏輯，在偶爾遇到暫時性錯誤時就會更可靠。

程式通訊時使用 SQL Database 透過協力廠商的中介軟體，請詢問廠商是否中介軟體中包含的暫時性錯誤重試邏輯。

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>重試原則
* 如果錯誤是暫時性的重試開啟的連接。
* 請勿直接重試失敗，發生暫時性錯誤的 SQL SELECT 陳述式。
  * 而是建立全新的連線，然後重試 SELECT。
* 當 SQL UPDATE 陳述式失敗，發生暫時性的錯誤時，建立全新的連線之前重試更新。
  * 整個資料庫交易完成，或整個交易都回復，則必須確保重試邏輯。

### <a name="other-considerations-for-retry"></a>其他重試考量
* 批次程式會自動啟動上班時間之後和早上可以負擔會非常長的時間間隔之間的重試次數的病患之前完成。
* 使用者介面程式應該說明了人類將在長時間等候後放棄的傾向。
  * 方案必須不會重試每幾秒鐘，因為該原則可以填滿系統的要求。

### <a name="interval-increase-between-retries"></a>增加重試之間的間隔
我們建議您在您第一次重試之前等候 5 秒。 在少於 5 秒的延遲後重試，雲端服務會有超過負荷的風險。 每個後續的重試延遲應該成長以指數方式，最大值為 60 秒。

如需使用 ADO.NET 的用戶端的封鎖期間內的討論，請參閱[SQL Server 連接共用 (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)。

您也可以設定程式自我結束之前的重試次數上限。

### <a name="code-samples-with-retry-logic"></a>具有重試邏輯的程式碼範例
使用重試邏輯的程式碼範例位於：

- [使用 ADO.NET 復原連線 SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [使用 PHP 復原連線 SQL][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>測試您的重試邏輯
若要測試重試邏輯，您必須模擬，或造成可以解決您的程式仍在執行時的錯誤。

#### <a name="test-by-disconnecting-from-the-network"></a>中斷與網路連接以進行測試
您可以測試重試邏輯的方法，就是在程式執行時中斷用戶端電腦與網路的連接。 錯誤為：

* **SqlException.Number** = 11001
* 訊息：「未知的主機」

第一次重試嘗試的一部分，您的程式可以更正拼字錯誤，並嘗試連線。

若要讓這項測試實際，拔除從網路電腦，才能啟動您的程式。 然後您的程式會辨識會使程式執行階段參數：

* 暫時將 11001 加入至其錯誤清單，視為暫時性。
* 如往常般嘗試其第一個連接。
* 在攔截到錯誤之後，請從清單中移除 11001。
* 顯示一則訊息，告知使用者，若要將電腦連線到網路。
   * 使用 **Console.ReadLine** 方法或含 [確定] 按鈕的對話方塊，暫停進一步執行。 電腦已插入網路之後，使用者按下 Enter 鍵。
* 重新嘗試連接，預期成功。

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>連接時拼錯資料庫名稱以進行測試
在第一次連接嘗試之前，您的程式可以故意拼錯使用者名稱。 錯誤為：

* **SqlException.Number** = 18456
* 錯誤將為：「使用者 'WRONG_MyUserName' 登入失敗。」

第一次重試嘗試的一部分，您的程式可以更正拼字錯誤，並嘗試連線。

若要讓實際這項測試，您的程式會辨識會使程式執行階段參數：

* 暫時將 18456 加入至其錯誤清單，視為暫時性。
* 故意將 'WRONG_' 加入至使用者名稱。
* 在攔截到錯誤之後，請從清單中移除 18456。
* 從使用者名稱中移除 'WRONG_'。
* 重新嘗試連接，預期成功。


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>進行連線重試的.NET SqlConnection 參數
如果您的用戶端程式會連接至 SQL 資料庫使用的.NET Framework 類別**System.Data.SqlClient.SqlConnection**，使用.NET 4.6.1 或更新版本 （或.NET 核心），讓您可以使用其連接重試功能。 如需有關此功能的詳細資訊，請參閱[此網頁](http://go.microsoft.com/fwlink/?linkid=393996)。

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

當您建置[連接字串](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx)如您**SqlConnection**物件、 協調的值之間的下列參數：

* **ConnectRetryCount**:&nbsp;&nbsp;預設值為 1。 範圍是 0 到 255。
* **ConnectRetryInterval**:&nbsp;&nbsp;預設值是 1 秒。 範圍是 1 到 60。
* **連接逾時**:&nbsp;&nbsp;預設為 15 秒。 範圍是 0 到 2147483647。

具體來說，您選擇的值應該會讓下列等式成立：

Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

比方說，如果計數等於 3，且間隔等於 10 秒，只有 29 秒的逾時無法提供系統足夠的時間讓其第三個和最後重試連接： 29 < 3 * 10。

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>與命令的連接
**ConnectRetryCount** 和 **ConnectRetryInterval** 參數讓您的 **SqlConnection** 物件可重試連接作業，而不需告知或中斷您的程式，例如將控制權傳回您的程式。 可能會在以下情況中發生重試：

* mySqlConnection.Open 方法呼叫
* mySqlConnection.Execute 方法呼叫

有一些微妙的差異。 如果發生暫時性錯誤時您*查詢*正在執行，您**SqlConnection**物件不會重試連線作業。 它一定不會重試您的查詢。 不過，在傳送您的查詢以供執行之前， **SqlConnection** 會先快速檢查連接。 如果快速檢查偵測到連接問題， **SqlConnection** 會重試連接作業。 如果重試成功，您的查詢會傳送給執行中。

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>是否應該將 ConnectRetryCount 與應用程式重試邏輯結合？
假設您的應用程式有健全的自訂重試邏輯。 它可能會重試連線作業四次。 如果您將 **ConnectRetryInterval** 和 **ConnectRetryCount** =3 加入到連接字串，您會將重試次數增加為 4 * 3 = 12 次重試。 您可能不會想要這麼多的重試次數。


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>SQL 資料庫的連接
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>連接：連接字串
需要連接到 SQL Database 的連接字串會稍有不同於用來連接到 SQL Server 的字串。 您可以從 [Azure 入口網站](https://portal.azure.com/)複製資料庫的連接字串。

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>連接：IP 位址
您必須設定 SQL Database 伺服器，以接受來自裝載您的用戶端程式之電腦的通訊。 若要設定此設定，編輯 防火牆設定，透過[Azure 入口網站](https://portal.azure.com/)。

如果您忘了設定的 IP 位址，您的程式將會很方便的錯誤訊息，指出所需的 IP 位址失敗。

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

如需詳細資訊，請參閱[SQL 資料庫上設定防火牆設定](sql-database-configure-firewall-settings.md)。
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>連接：連接埠
一般來說，您必須確保只有通訊埠 1433年供裝載您的用戶端程式的電腦上的連出通訊開啟。

例如，當用戶端程式裝載在 Windows 電腦上，您可以使用 Windows 防火牆在主機上開啟連接埠 1433年。

1. 開啟控制台。

2. 選取**所有控制控制台項目** > **Windows 防火牆** > **進階設定** > **輸出規則**  > **動作** > **新規則**。

如果您的用戶端程式裝載於 Azure 虛擬機器 (VM) 時，讀取[ADO.NET 4.5 和 SQL Database 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。

如需設定背景資訊的連接埠及 IP 位址，請參閱[Azure SQL Database 防火牆](sql-database-firewall-configure.md)。

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>連接：ADO.NET 4.6.1
如果您的程式使用 ADO.NET 類別，例如**System.Data.SqlClient.SqlConnection**連接到 SQL 資料庫，我們建議您先使用.NET Framework 4.6.1 版或更新版本。

ADO.NET 4.6.1：

* 藉由開啟連接時，可靠性改進 SQL database **SqlConnection.Open**方法。 **開啟**方法現已加入暫時性錯誤的回應中的特定錯誤的最佳方式的重試機制連接逾時期限內。
* 連接共用支援，其中包含有效的驗證之連接物件，它可讓您的程式正常運作。

當您從連接集區使用的連接物件時，我們建議您的程式暫時關閉連線時立即使用中。 不是高度耗費資源來重新開啟連線，但它是建立新的連接。

如果您使用 ADO.NET 4.0 或更早版本，我們建議您升級至最新的 ADO.NET。 從 2015 年 11 月開始，您可以 [下載 ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx)。

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>診斷
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>診斷：測試公用程式是否可以連接
如果您的程式無法連接到 SQL Database，一個診斷的選項是嘗試連接的公用程式。 在理想情況下，此公用程式會使用連接您的程式使用的相同文件庫。

您可以在任何 Windows 電腦上，嘗試這些公用程式：

* SQL Server Management Studio (ssms.exe) 中，使用 ADO.NET 連接
* sqlcmd.exe，會使用連接[ODBC](http://msdn.microsoft.com/library/jj730308.aspx)

連接您的程式之後，測試是否簡短的 SQL SELECT 查詢運作。

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>診斷：檢查開啟的連接埠
如果您懷疑連接嘗試失敗，因為連接埠問題，您可以在報表設定連接埠的電腦上執行公用程式。

在 Linux 上的下列公用程式可能很有幫助：

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * 範例將值變更為您的 IP 位址。

在 Windows 中， [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148)公用程式可能會很有幫助。 以下是範例執行的查詢 SQL 資料庫伺服器上的連接埠情況和膝上型電腦上已執行：

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>診斷：記錄您的錯誤
有時診斷間歇問題的最好方式，就是數天或數週偵測一般模式。

您的用戶端可以記錄其遇到的所有錯誤來協助診斷。 您可能會是與 SQL 資料庫記錄檔本身內部的錯誤資料相互關聯的記錄項目。

Enterprise Library 6 (EntLib60) 提供用來協助記錄的.NET managed 類別。 如需詳細資訊，請參閱[5-簡單，只要下降關閉記錄： 使用記錄應用程式區塊](http://msdn.microsoft.com/library/dn440731.aspx)。

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>診斷：檢查系統記錄找出錯誤
以下是一些 TRANSACT-SQL SELECT 陳述式查詢錯誤記錄檔和其他資訊。

| 記錄查詢 | 說明 |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx)檢視提供有關個別事件，其中包括某些可能會導致暫時性錯誤或連線失敗。<br/><br/>在理想情況下，您可以相互關聯**start_time**或**end_time**當用戶端程式發生問題的相關資訊的值。<br/><br/>您必須連接到*主要*資料庫以執行此查詢。 |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx)檢視提供的事件類型的其他診斷彙總計的數。<br/><br/>您必須連接到*主要*資料庫以執行此查詢。 |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>診斷：在 SQL Database 記錄中搜尋問題事件
您可以搜尋有關問題的事件在 SQL 資料庫記錄檔中的項目。 在 *master* 資料庫中嘗試下列 Transact-SQL SELECT 陳述式：

```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>數個從 sys.fn_xe_telemetry_blob_target_read_file 傳回的資料列
下列範例顯示傳回的資料列可能的外觀。 顯示的 null 值通常在其他資料列不是 null。

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
Enterprise Library 6 (EntLib60) 是的架構的.NET 類別，可協助您實作強固的用戶端的雲端服務，其中是 SQL Database 服務。 若要尋找主題專門用於 EntLib60 可幫助每個區域，請參閱[Enterprise Library 6-年 4 月 2013年](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)。

處理暫時性錯誤重試邏輯是一個區域 EntLib60 可幫助。 如需詳細資訊，請參閱[4-堅持不懈，密碼的所有勝利： 使用暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)。

> [!NOTE]
> EntLib60 的程式碼是可從公用下載[下載中心](http://go.microsoft.com/fwlink/p/?LinkID=290898)。 Microsoft 沒有計劃進一步更新或維護 EntLib 的功能。
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>用於暫時性錯誤和重試的 EntLib60 類別
下列 EntLib60 類別特別有助於重試邏輯。 所有這些類別或命名空間之下，找到**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**。

命名空間中**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

* **RetryPolicy** 類別
  
  * **ExecuteAction** 方法
* **ExponentialBackoff** 類別
* **SqlDatabaseTransientErrorDetectionStrategy** 類別
* **ReliableSqlConnection** 類別
  
  * **ExecuteCommand** 方法

在命名空間 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**中：

* **AlwaysTransientErrorDetectionStrategy** 類別
* **NeverTransientErrorDetectionStrategy** 類別

以下是一些 EntLib60 相關資訊的連結：

* 下載免費的通訊錄： [Microsoft 企業程式庫，第 2 版的開發人員指南](http://www.microsoft.com/download/details.aspx?id=41145)。
* 最佳作法： [重試一般指引](../best-practices-retry-general.md) 深入探討重試邏輯。
* NuGet 下載： [Enterprise Library-暫時性錯誤處理應用程式區塊 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)。

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60：記錄區塊
* 記錄區塊會是高彈性和可設定的解決方案，您可以使用：
  
  * 建立記錄訊息，並儲存在各種不同的位置中。
  * 分類與篩選訊息。
  * 收集有助於偵錯和追蹤的內容資訊，以及用於稽核和一般記錄需求的內容資訊。
* 記錄區塊會擷取記錄目的地的記錄功能，使應用程式程式碼一致，無論目標記錄存放區的類型與位置。

如需詳細資訊，請參閱[5-簡單，只要下降關閉記錄： 使用記錄應用程式區塊](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)。

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient 方法的原始程式碼
接下來，**IsTransient** 方法的 C# 原始程式碼來自 **SqlDatabaseTransientErrorDetectionStrategy** 類別。 原始程式碼中，將釐清哪些錯誤視為暫時性和重試 」，於 2013 年 4 月的。

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>後續步驟
* 如需其他常見的 SQL Database 連線問題疑難排解的詳細資訊，請參閱[疑難排解連線問題到 Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md)。
* [SQL Database 和 SQL Server 的連線庫](sql-database-libraries.md)
* [SQL Server 連接共用 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*正在重試*是一般用途的重試一次程式庫，以撰寫的 Python 的 Apache 2.0 授權](https://pypi.python.org/pypi/retrying)來簡化重試行加入幾乎任何項目中的工作。


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

