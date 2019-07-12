---
title: SQL 資料庫稽核記錄檔格式 |Microsoft Docs
description: 了解 SQL Database 稽核記錄檔的結構方式。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61417383"
---
# <a name="sql-database-audit-log-format"></a>SQL 資料庫稽核記錄檔格式

[Azure SQL Database 稽核](sql-database-auditing.md)會追蹤資料庫事件並將寫入的稽核記錄的 Azure 儲存體帳戶中，或將它們傳送到事件中樞或 Log Analytics 中進行下游處理和分析。

## <a name="naming-conventions"></a>命名慣例

### <a name="blob-audit"></a>Blob 稽核

Blob 儲存體中的稽核記錄檔會儲存在名為容器`sqldbauditlogs`Azure 儲存體帳戶。 在容器內的目錄階層的形式是`<ServerName>/<DatabaseName>/<AuditName>/<Date>/`。 Blob 檔案名稱格式是`<CreationTime>_<FileNumberInSession>.xel`，其中`CreationTime`utc`hh_mm_ss_ms`格式，和`FileNumberInSession`會執行編製索引，如果工作階段記錄跨多個 Blob 檔案的範圍。

例如，對於資料庫`Database1`上`Server1`以下是可能的有效路徑：

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>事件中樞

稽核事件會寫入至已定義稽核的設定期間，所擷取的主體中的命名空間和事件中樞[Apache Avro](https://avro.apache.org/)事件並使用 JSON 格式化以 utf-8 編碼方式儲存。 若要讀取稽核記錄，您可以使用 [Avro Tools](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) 或類似工具來處理這種格式。

### <a name="log-analytics"></a>Log Analytics

稽核事件會寫入至稽核的設定，期間所定義的 Log Analytics 工作區`AzureDiagnostics`類別的資料表`SQLSecurityAuditEvents`。 如需 Log Analytics 搜尋語言和命令的其他實用資訊，請參閱 [Log Analytics 搜尋參考](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search)。

## <a id="subheading-1"></a>稽核記錄檔欄位

| 名稱 (Blob) | 名稱 (事件中樞/Log Analytics) | 描述 | Blob 類型 | 事件中樞/Log Analytics 類型 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 動作的識別碼 | varchar(4) | string |
| action_name | action_name_s | 動作名稱 | N/A | string |
| additional_information | additional_information_s | 任何事件，以 XML 格式儲存的其他資訊 | nvarchar(4000) | string |
| affected_rows | affected_rows_d | 查詢所影響的資料列數目 | bigint | ssNoversion |
| application_name | application_name_s| 用戶端應用程式的名稱 | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | 永遠為 1 | ssNoversion | ssNoversion |
| class_type | class_type_s | 稽核發生所在的可稽核實體的型別 | varchar(2) | string |
| class_type_desc | class_type_description_s | 稽核發生所在的可稽核實體的描述 | N/A | string |
| client_ip | client_ip_s | 來源用戶端應用程式的 IP | nvarchar(128) | string |
| connection_id | N/A | 在伺服器中之連線識別碼 | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | 資訊類型和稽核的查詢，根據資料庫中的分類資料行所傳回的敏感度標籤。 深入了解[Azure SQL Database 的資料探索和分類](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | 動作發生時的資料庫內容 | sysname | string |
| database_principal_id | database_principal_id_d | 動作執行所在的資料庫使用者內容識別碼 | ssNoversion | ssNoversion |
| database_principal_name | database_principal_name_s | 資料庫使用者內容中執行此動作的名稱 | sysname | string |
| duration_milliseconds | duration_milliseconds_d | 查詢執行時間 （毫秒） | bigint | ssNoversion |
| event_time | event_time_t | 日期和時間時，會引發可稽核的動作 | datetime2 | datetime |
| host_name | N/A | 用戶端主機名稱 | string | N/A |
| is_column_permission | is_column_permission_s | 如果這個旗標，指出是資料行層級權限。 1 = true,0 = false | bit | string |
| N/A | is_server_level_audit_s | 表示如果這個稽核伺服器層級的旗標 | N/A | string |
| object_ 識別碼 | object_id_d | 稽核發生所在之實體的識別碼。 這包括： 伺服器物件、 資料庫、 資料庫物件和結構描述物件。 0 如果實體是伺服器本身，或是稽核並未在物件層級執行 | ssNoversion | ssNoversion |
| object_name | object_name_s | 稽核發生所在的實體名稱。 這包括： 伺服器物件、 資料庫、 資料庫物件和結構描述物件。 0 如果實體是伺服器本身，或是稽核並未在物件層級執行 | sysname | string |
| permission_bitmask | permission_bitmask_s | 適用時，會顯示所授與、 拒絕或撤銷的權限 | varbinary(16) | string |
| response_rows | response_rows_d | 在結果集中傳回的資料列數目 | bigint | ssNoversion |
| schema_name | schema_name_s | 動作發生時的結構描述內容。 稽核發生外部結構描述都是 NULL | sysname | string |
| N/A | securable_class_type_s | 對應到正在稽核之 class_type 的安全性實體物件 | N/A | string |
| sequence_group_id | sequence_group_id_g | 唯一識別碼 | varbinary | GUID |
| sequence_number | sequence_number_d | 會追蹤單一稽核記錄太大，無法納入稽核寫入緩衝區內的記錄順序 | ssNoversion | ssNoversion |
| server_instance_name | server_instance_name_s | 稽核發生所在的伺服器執行個體名稱 | sysname | string |
| server_principal_id | server_principal_id_d | 執行這個動作時的登入內容的識別碼 | ssNoversion | ssNoversion |
| server_principal_name | server_principal_name_s | 目前的登入 | sysname | string |
| server_principal_sid | server_principal_sid_s | 目前的登入 SID | varbinary | string |
| session_id | session_id_d | 發生事件之工作階段識別碼 | smallint | ssNoversion |
| session_server_principal_name | session_server_principal_name_s | 工作階段的伺服器主體 | sysname | string |
| 陳述式 | statement_s | （如果有的話） 已執行的 T-SQL 陳述式 | nvarchar(4000) | string |
| 已成功 | succeeded_s | 表示觸發事件的動作是否成功。 登入和批次以外的事件，這只會報告權限檢查成功還是失敗，not 運算。 1 = 成功，0 = 失敗 | bit | string |
| target_database_principal_id | target_database_principal_id_d | GRANT/DENY/REVOKE 作業上執行的資料庫主體。 0，如果不適用 | ssNoversion | ssNoversion |
| target_database_principal_name | target_database_principal_name_s | 動作的目標使用者。 如果不適用則為 NULL | string | string |
| target_server_principal_id | target_server_principal_id_d | GRANT/DENY/REVOKE 作業執行所在的伺服器主體。 如果不適用則傳回 0 | ssNoversion | ssNoversion |
| target_server_principal_name | target_server_principal_name_s | 動作的目標登入。 如果不適用則為 NULL | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | 目標登入的 SID。 如果不適用則為 NULL | varbinary | string |
| transaction_id | transaction_id_d | 僅限 SQL Server （從 2016年開始）-適用於 Azure SQL DB 的 0 | bigint | ssNoversion |
| user_defined_event_id | user_defined_event_id_d | 使用者定義事件識別碼做為引數傳遞至 sp_audit_write。 系統事件 （預設值） 的 NULL 和非零值則代表使用者定義的事件。 如需詳細資訊，請參閱[sp_audit_write & Amp;#40;transact-SQL&AMP;#41;](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | ssNoversion |
| user_defined_information | user_defined_information_s | 使用者定義當做引數傳遞給 sp_audit_write 的資訊。 系統事件 （預設值） 的 NULL 和非零值則代表使用者定義的事件。 如需詳細資訊，請參閱[sp_audit_write & Amp;#40;transact-SQL&AMP;#41;](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>後續步驟

深入了解[Azure SQL Database 稽核](sql-database-auditing.md)。