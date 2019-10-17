---
title: 適用於 MySQL 的 Azure 資料庫的 Audit 記錄
description: 描述適用於 MySQL 的 Azure 資料庫中可用的 audit 記錄，以及可用來啟用記錄層級的參數。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 42881fcb12f29ec14bbdc0ec4942b2eef17c7312
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434413"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫中的 Audit 記錄

在適用於 MySQL 的 Azure 資料庫中，審核記錄可供使用者使用。 您可以使用 audit 記錄來追蹤資料庫層級的活動，而且通常會用於合規性。

> [!IMPORTANT]
> Audit log 功能目前為預覽狀態。

## <a name="configure-audit-logging"></a>設定審核記錄

根據預設，會停用 audit 記錄檔。 若要啟用它，請將 `audit_log_enabled` 設定為 ON。

您可以調整的其他參數包含：

- `audit_log_events`：控制要記錄的事件。 請參閱下表以取得特定的 audit 事件。
- `audit_log_include_users`：要包含在記錄中的 MySQL 使用者。 這個參數的預設值是空的，這將包含所有用於記錄的使用者。 優先順序高於 `audit_log_exclude_users`。 參數的最大長度為512個字元。
> [!Note]
> `audit_log_include_users` 的優先順序高於 `audit_log_exclude_users`，例如，如果 audit_log_include_users = `demouser` and audit_log_exclude_users = `demouser`，則會審核記錄，因為 `audit_log_include_users` 的優先順序較高。
- `audit_log_exclude_users`：要排除在記錄之外的 MySQL 使用者。 參數的最大長度為512個字元。

> [!Note]
> 若為 `sql_text`，如果記錄檔超過2048個字元，則會被截斷。

| **Event** | **說明** |
|---|---|
| `CONNECTION` | -連接起始（成功或失敗） <br> -在會話期間以不同的使用者/密碼重新驗證使用者 <br> -連接終止 |
| `DML_SELECT`| 選取查詢 |
| `DML_NONSELECT` | 插入/刪除/更新查詢 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 「卸載資料庫」之類的查詢 |
| `DCL` | 「授與許可權」之類的查詢 |
| `ADMIN` | 「顯示狀態」之類的查詢 |
| `GENERAL` | All in DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN |
| `TABLE_ACCESS` | -僅適用于 MySQL 5。7 <br> -資料表讀取語句，例如 SELECT 或 INSERT INTO .。。請 <br> -Table delete 語句，例如 DELETE 或 TRUNCATE TABLE <br> -資料表 insert 語句，例如 INSERT 或 REPLACE <br> -資料表 update 語句，例如 UPDATE |

## <a name="access-audit-logs"></a>存取稽核記錄

Audit 記錄會與 Azure 監視器診斷記錄整合。 在 MySQL 伺服器上啟用 audit 記錄檔之後，您就可以將它們發出至 Azure 監視器記錄、事件中樞或 Azure 儲存體。 若要深入瞭解如何在 Azure 入口網站中啟用診斷記錄，請參閱[audit log portal 一文](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)。

## <a name="diagnostic-logs-schemas"></a>診斷記錄架構

下列各節說明根據事件種類，MySQL audit 記錄的輸出內容。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

### <a name="connection"></a>連線

| **屬性** | **說明** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`，`DISCONNECT`，`CHANGE USER` （僅適用于 MySQL 5.7） |
| `connection_id_d` | MySQL 產生的唯一連接識別碼 |
| `host_s` | 空白 |
| `ip_s` | 連接到 MySQL 的用戶端 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `db_s` | 連接的資料庫名稱 |
| `\_ResourceId` | 資源 URI |

### <a name="general"></a>一般

以下架構適用于 GENERAL、DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN 事件種類。

| **屬性** | **說明** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`，`ERROR`，`RESULT` （僅適用于 MySQL 5.6） |
| `event_time` | 查詢開始時間（UTC 時間戳記） |
| `error_code_d` | 如果查詢失敗，則為錯誤碼。 `0` 表示沒有錯誤 |
| `thread_id_d` | 執行查詢的執行緒識別碼 |
| `host_s` | 空白 |
| `ip_s` | 連接到 MySQL 的用戶端 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `sql_text_s` | 完整查詢文字 |
| `\_ResourceId` | 資源 URI |

### <a name="table-access"></a>資料表存取

| **屬性** | **說明** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`、`INSERT`、`UPDATE` 或 `DELETE` |
| `connection_id_d` | MySQL 產生的唯一連接識別碼 |
| `db_s` | 存取的資料庫名稱 |
| `table_s` | 存取的資料表名稱 |
| `sql_text_s` | 完整查詢文字 |
| `\_ResourceId` | 資源 URI |

## <a name="next-steps"></a>後續步驟

- [如何設定 Azure 入口網站中的 audit 記錄](howto-configure-audit-logs-portal.md)