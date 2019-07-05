---
title: 適用於 Azure Database 稽核記錄檔，適用於 MariaDB
description: 描述可用的 Azure 資料庫中的稽核記錄 MariaDB，並啟用記錄層級的可用參數。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 13ea60c62283db35ce4bf9fde6c3b36ba7f88013
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439210"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>稽核適用於 MariaDB 的 Azure 資料庫中的記錄檔

適用於 MariaDB 的 Azure 資料庫中的稽核記錄檔是提供給使用者。 稽核記錄檔可用來追蹤資料庫層級的活動，並常用於合規性。

> [!IMPORTANT]
> 稽核記錄功能目前為預覽狀態。

## <a name="configure-audit-logging"></a>設定稽核記錄

預設會停用稽核記錄檔。 若要啟用它，請設定`audit_log_enabled`設為 ON。

您可以調整的其他參數包含：

- `audit_log_events`： 控制要記錄的事件。 請參閱下表針對特定的稽核事件。
- `audit_log_exclude_users`:若要從記錄中排除的 MariaDB 使用者。 允許最多有四個使用者。 參數的最大長度為 256 個字元。

| **Event** | **說明** |
|---|---|
| `CONNECTION` | -連線初始化 （成功或失敗） <br> 使用不同的使用者/密碼的工作階段的使用者重新驗證 <br> -連接終止 |
| `DML_SELECT`| SELECT 查詢 |
| `DML_NONSELECT` | 插入/刪除/更新查詢 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 例如 「 卸除資料庫 」 的查詢 |
| `DCL` | 「 授與權限 」 類似的查詢 |
| `ADMIN` | 「 顯示狀態 」 類似的查詢 |
| `GENERAL` | 全都放在 DML_SELECT、 DML_NONSELECT、 DML、 DDL、 DCL 和系統管理員 |

## <a name="access-audit-logs"></a>存取稽核記錄

稽核記錄檔會與 Azure 監視器的診斷記錄檔整合。 一旦您 MariaDB 伺服器上啟用稽核記錄檔，您可以發出這些 Azure 監視器記錄檔、 事件中樞或 Azure 儲存體。 若要深入了解如何啟用 Azure 入口網站中的診斷記錄檔，請參閱[稽核記錄檔入口網站的文章](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)。

## <a name="diagnostic-logs-schemas"></a>診斷記錄結構描述

下列各節會描述什麼是由 MariaDB 稽核記錄，根據事件類型的輸出。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

### <a name="connection"></a>連線

| **屬性** | **說明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`、 `DISCONNECT` |
| `connection_id_d` | MariaDB 所產生的唯一連接識別碼 |
| `host_s` | 空白 |
| `ip_s` | 連線到 MariaDB 的用戶端 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `db_s` | 若要連接的資料庫名稱 |
| `\_ResourceId` | 資源 URI |

### <a name="general"></a>一般

下列的結構描述適用於一般、 DML_SELECT、 DML_NONSELECT、 DML、 DDL、 DCL 和系統管理員的事件類型。

| **屬性** | **說明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | 查詢中的 UNIX 時間戳記開始秒 |
| `error_code_d` | 如果查詢失敗，出現錯誤代碼。 `0` 表示沒有任何錯誤 |
| `thread_id_d` | 執行查詢的執行緒識別碼 |
| `host_s` | 空白 |
| `ip_s` | 連線到 MariaDB 的用戶端 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `sql_text_s` | 完整的查詢文字 |
| `\_ResourceId` | 資源 URI |

### <a name="table-access"></a>資料表存取權

| **屬性** | **說明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ``INSERT`， `UPDATE`，或 `DELETE` |
| `connection_id_d` | MariaDB 所產生的唯一連接識別碼 |
| `db_s` | 存取資料庫的名稱 |
| `table_s` | 存取的資料表名稱 |
| `sql_text_s` | 完整的查詢文字 |
| `\_ResourceId` | 資源 URI |

## <a name="next-steps"></a>後續步驟

- [如何在 Azure 入口網站中設定稽核記錄檔](howto-configure-audit-logs-portal.md)