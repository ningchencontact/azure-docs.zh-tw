---
title: SQL Database Audit 記錄檔格式 |Microsoft Docs
description: 瞭解如何將 SQL Database 的 audit 記錄結構化。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 334d277370bb8d6678679c887f6a2b89d65652c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569479"
---
# <a name="sql-database-audit-log-format"></a>SQL Database Audit 記錄格式

[Azure SQL Database 審核](sql-database-auditing.md)會追蹤資料庫事件, 並將它們寫入您 Azure 儲存體帳戶中的 audit 記錄, 或將它們傳送到事件中樞或 log Analytics, 以進行下游處理和分析。

## <a name="naming-conventions"></a>命名慣例

### <a name="blob-audit"></a>Blob Audit

儲存在 Blob 儲存體中的 Audit 記錄會儲存在 Azure 儲存體`sqldbauditlogs`帳戶中名為的容器中。 容器內的目錄階層的格式`<ServerName>/<DatabaseName>/<AuditName>/<Date>/`為。 Blob `<CreationTime>_<FileNumberInSession>.xel`檔案名格式為, 其中`CreationTime`是 UTC `hh_mm_ss_ms`格式, 而`FileNumberInSession`是在多個 Blob 檔案之間進行會話記錄時的執行中索引。

例如, 下列的資料庫`Database1` `Server1`可能是有效的路徑:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>事件中樞

Audit 事件會寫入至在審核設定期間定義的命名空間和事件中樞, 並在[Apache Avro](https://avro.apache.org/)事件的主體中加以捕獲, 並使用具有 utf-8 編碼的 JSON 格式加以儲存。 若要讀取稽核記錄，您可以使用 [Avro Tools](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) 或類似工具來處理這種格式。

### <a name="log-analytics"></a>Log Analytics

Audit 事件會寫入至在審核設定期間定義的`AzureDiagnostics` Log Analytics 工作區, 到具有該類別`SQLSecurityAuditEvents`目錄的資料表。 如需 Log Analytics 搜尋語言和命令的其他實用資訊，請參閱 [Log Analytics 搜尋參考](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search)。

## <a id="subheading-1"></a>Audit 記錄欄位

| 名稱 (Blob) | 名稱 (事件中樞/Log Analytics) | 描述 | Blob 類型 | 事件中樞/Log Analytics 類型 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 動作的識別碼 | Varchar (4) | string |
| action_name | action_name_s | 動作的名稱 | N/A | string |
| additional_information | additional_information_s | 與事件相關的任何其他資訊, 儲存為 XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | 受查詢影響的資料列數目 | Bigint | ssNoversion |
| application_name | application_name_s| 用戶端應用程式的名稱 | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | 一律為1 | ssNoversion | ssNoversion |
| class_type | class_type_s | 發生 audit 的可審核實體類型 | Varchar (2) | string |
| class_type_desc | class_type_description_s | 進行 audit 的可審核實體描述 | N/A | string |
| client_ip | client_ip_s | 用戶端應用程式的來源 IP | nvarchar(128) | string |
| connection_id | N/A | 伺服器中的連接識別碼 | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | 由經過審核的查詢所傳回的資訊類型和敏感度標籤, 是根據資料庫中的分類資料行。 深入瞭解[Azure SQL Database 資料探索與分類](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | 發生動作的資料庫內容 | sysname | string |
| database_principal_id | database_principal_id_d | 動作執行所在之資料庫使用者內容的識別碼 | ssNoversion | ssNoversion |
| database_principal_name | database_principal_name_s | 執行動作所在的資料庫使用者內容名稱 | sysname | string |
| duration_milliseconds | duration_milliseconds_d | 查詢執行持續時間 (毫秒) | Bigint | ssNoversion |
| event_time | event_time_t | 可審核動作的引發日期和時間 | 日期時間2 | datetime |
| host_name | N/A | 用戶端主機名稱 | string | N/A |
| is_column_permission | is_column_permission_s | 表示這是否為數據行層級許可權的旗標。 1 = true, 0 = false | bit | string |
| N/A | is_server_level_audit_s | 表示此 audit 是否在伺服器層級的旗標 | N/A | string |
| object_ 識別碼 | object_id_d | 發生 audit 之實體的識別碼。 這包括: 伺服器物件、資料庫、資料庫物件和架構物件。 如果實體是伺服器本身, 或如果不是在物件層級執行, 則為0 | ssNoversion | ssNoversion |
| object_name | object_name_s | 發生 audit 之實體的名稱。 這包括: 伺服器物件、資料庫、資料庫物件和架構物件。 如果實體是伺服器本身, 或如果不是在物件層級執行, 則為0 | sysname | string |
| permission_bitmask | permission_bitmask_s | 適用時, 會顯示已授與、拒絕或撤銷的許可權 | Varbinary (16) | string |
| response_rows | response_rows_d | 結果集中傳回的資料列數目 | Bigint | ssNoversion |
| schema_name | schema_name_s | 發生動作的架構內容。 若為在架構外發生的審核, 則為 Null | sysname | string |
| N/A | securable_class_type_s | 對應至所要審核之 class_type 的安全物件 | N/A | string |
| sequence_group_id | sequence_group_id_g | 唯一識別碼 | varbinary | GUID |
| sequence_number | sequence_number_d | 追蹤單一 audit 記錄中太大而無法納入進行審核的寫入緩衝區中的記錄順序 | ssNoversion | ssNoversion |
| server_instance_name | server_instance_name_s | 發生 audit 之伺服器實例的名稱 | sysname | string |
| server_principal_id | server_principal_id_d | 執行動作之登入內容的識別碼 | ssNoversion | ssNoversion |
| server_principal_name | server_principal_name_s | 目前的登入 | sysname | string |
| server_principal_sid | server_principal_sid_s | 目前的登入 SID | varbinary | string |
| session_id | session_id_d | 發生事件之會話的識別碼 | smallint | ssNoversion |
| session_server_principal_name | session_server_principal_name_s | 會話的伺服器主體 | sysname | string |
| 陳述式 | statement_s | 已執行的 t-sql 語句 (如果有的話) | nvarchar(4000) | string |
| 成功 | succeeded_s | 指出觸發事件的動作是否成功。 對於登入和批次以外的事件, 這只會報告許可權檢查成功或失敗, 而不是作業。 1 = 成功, 0 = 失敗 | bit | string |
| target_database_principal_id | target_database_principal_id_d | 授與/拒絕/撤銷作業執行所在的資料庫主體。 0 (如果不適用) | ssNoversion | ssNoversion |
| target_database_principal_name | target_database_principal_name_s | 動作的目標使用者。 Null (如果不適用) | string | string |
| target_server_principal_id | target_server_principal_id_d | 授與/拒絕/撤銷作業執行所在的伺服器主體。 如果不適用, 則傳回0 | ssNoversion | ssNoversion |
| target_server_principal_name | target_server_principal_name_s | 動作的目標登入。 Null (如果不適用) | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | 目標登入的 SID。 Null (如果不適用) | varbinary | string |
| transaction_id | transaction_id_d | 僅限 SQL Server (從2016開始)-0 (針對 Azure SQL DB) | Bigint | ssNoversion |
| user_defined_event_id | user_defined_event_id_d | 使用者定義的事件識別碼, 以引數形式傳遞給 sp_audit_write。 對於系統事件 (預設值) 為 Null, 使用者定義事件則為非零。 如需詳細資訊, 請參閱[sp_audit_write (transact-sql)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) 。 | smallint | ssNoversion |
| user_defined_information | user_defined_information_s | 將使用者定義的資訊當做引數傳遞給 sp_audit_write。 對於系統事件 (預設值) 為 Null, 使用者定義事件則為非零。 如需詳細資訊, 請參閱[sp_audit_write (transact-sql)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) 。 | nvarchar(4000) | string |

## <a name="next-steps"></a>後續步驟

深入瞭解[Azure SQL Database 的審核](sql-database-auditing.md)。