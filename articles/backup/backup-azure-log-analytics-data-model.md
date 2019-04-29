---
title: Azure 備份的 azure 監視器記錄檔資料模型
description: 本文說明 Azure 監視器記錄 Azure 備份資料的資料模型詳細的資料。
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61234909"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>適用於 Azure 備份資料的 Log Analytics 資料模型

使用 Log Analytics 資料模型來建立自訂警示從 Log Analytics。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>使用 Azure 備份資料模型

若要根據您的需求建立視覺效果、自訂查詢及儀表板，您可以使用下列提供作為資料模型一部分的欄位。

### <a name="alert"></a>警报

下表提供警示相關欄位的詳細資料。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| AlertUniqueId_s |Text |所產生警示的唯一識別碼 |
| AlertType_s |Text |警示的類型，例如備份 |
| AlertStatus_s |Text |警示狀態 (例如，作用中) |
| AlertOccurrenceDateTime_s |日期/時間 |建立警示的日期和時間 |
| AlertSeverity_s |Text |警示嚴重性 (例如，重大) |
|AlertTimeToResolveInMinutes_s    | 數字        |若要解決警示所花費的時間。 空白的作用中警示。         |
|AlertConsolidationStatus_s   |Text         |識別警示是否彙總的警示         |
|CountOfAlertsConsolidated_s     |數字         |如果是合併的警示彙總的警示數目          |
|AlertRaisedOn_s     |Text         |型別之實體發出的警示         |
|AlertCode_s     |Text         |唯一識別警示類型的程式碼         |
|RecommendedAction_s   |Text         |若要解決警示的建議動作         |
| EventName_s |Text |活動的名稱。 一律為 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |與警示相關聯的備份項目所具備的唯一識別碼 |
| SchemaVersion_s |Text |目前版本的結構描述，例如**V2** |
| State_s |Text |警示物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |用於執行本警示所屬之備份的提供者類型 (例如，IaaSVM、FileFolder) |
| OperationName |Text |目前作業的名稱，例如 Alert |
| Category |Text |類別的診斷資料推送至 Azure 監視器記錄檔。 一律為 Always AzureBackupReport |
| Resource |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedServerUniqueId_s |Text |與警示相關聯的受保護伺服器所具備的唯一識別碼 |
| VaultUniqueId_s |Text |與警示相關聯的受保護保存庫所具備的唯一識別碼 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| resourceId |Text |要收集其相關資料的資源所具備的唯一識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="backupitem"></a>BackupItem

下表提供備份項目相關欄位的詳細資料。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |活動的名稱。 一律為 AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |備份項目的唯一識別碼 |
| BackupItemId_s |Text |備份項目的識別碼 |
| BackupItemName_s |Text |備份項目名稱 |
| BackupItemFriendlyName_s |Text |備份項目的易記名稱 |
| BackupItemType_s |文字 |備份項目類型 (例如，VM、FileFolder) |
| ProtectionState_s |Text |備份項目的目前保護狀態 (例如，受保護、ProtectionStopped) |
| ProtectionGroupName_s |Text | 保護群組 備份項目名稱中受到保護，SC DPM 和 MABS，如果適用的話|
| SecondaryBackupProtectionState_s |Text |備份的項目是否啟用次要保護|
| SchemaVersion_s |Text |結構描述版本，例如**V2** |
| State_s |Text |備份項目物件的狀態，例如使用中、已刪除 |
| BackupManagementType_s |Text |用於執行本備份項目所屬之備份的提供者類型 (例如，IaaSVM、FileFolder) |
| OperationName |Text |作業的名稱，例如 BackupItem |
| Category |Text |類別的診斷資料推送至 Azure 監視器記錄檔。 一律為 Always AzureBackupReport |
| Resource |Text |要收集其資料的資源，例如復原服務保存庫名稱 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| resourceId |Text |正在收集資料的資源識別碼，例如復原服務保存庫資源識別碼 |
| SubscriptionId |Text |正在收集資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |正在收集資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |正在收集資料的資源提供者，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集資料的資源類型，例如保存庫 |

### <a name="backupitemassociation"></a>BackupItemAssociation

下表提供與各種實體關聯的備份項目相關詳細資料。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |備份項目的唯一 ID |
| SchemaVersion_s |Text |此欄位代表目前版本的結構描述，它是**V2** |
| State_s |Text |備份項目關聯物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| BackupItemSourceSize_s |Text | 前端的大小，備份的項目 |
| BackupManagementServerUniqueId_s |Text | 如果適用的話，將受到保護，透過的欄位來唯一識別備份管理伺服器備份項目 |
| Category |Text |此欄位代表推送到 Log Analytics 的診斷資料類別，則是 AzureBackupReport |
| OperationName |Text |此欄位代表目前作業的名稱 - BackupItemAssociation |
| Resource |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| PolicyUniqueId_g |Text |與備份項目相關聯的原則所具備的唯一識別碼 |
| ProtectedServerUniqueId_s |Text |與備份項目相關聯的受保護伺服器所具備的唯一識別碼 |
| VaultUniqueId_s |Text |包含備份項目的保存庫所具備的唯一識別碼 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| resourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text |正在收集資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |正在收集資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |正在收集資料的資源提供者，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集資料的資源類型，例如保存庫 |

### <a name="backupmanagementserver"></a>BackupManagementServer

下表提供與各種實體關聯的備份項目相關詳細資料。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |備份管理伺服器的名稱        |
|AzureBackupAgentVersion_s     |Text         |備份管理伺服器上的 Azure 備份代理程式版本          |
|BackupManagementServerVersion_s     |Text         |備份管理伺服器的版本|
|BackupManagementServerOSVersion_s    |Text            |備份管理伺服器的 OS 版本|
|BackupManagementServerType_s     |Text         |備份管理伺服器，與 MABS、 SC DPM 的類型|
|BackupManagementServerUniqueId_s     |Text         |可唯一識別備份管理伺服器的欄位       |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| resourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text |正在收集資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |正在收集資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |正在收集資料的資源提供者，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集資料的資源類型，例如保存庫 |

### <a name="job"></a>工作 (Job)

下表提供作業相關欄位的詳細資料。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |活動的名稱。 一律為 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |備份項目的唯一識別碼 |
| SchemaVersion_s |Text |結構描述版本，例如**V2** |
| State_s |Text |作業物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text |此欄位代表目前作業的名稱 - Job |
| Category |Text |此欄位代表推送至 Azure 監視器記錄檔的診斷資料的類別目錄，則是 AzureBackupReport |
| Resource |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedServerUniqueId_s |Text |與作業相關聯的受保護伺服器所具備的唯一識別碼 |
| ProtectedContainerUniqueId_s |Text | 用來識別受保護的容器執行作業的唯一識別碼 |
| VaultUniqueId_s |Text |受保護保存庫的唯一識別碼 |
| JobOperation_s |Text |執行作業的操作 (例如，備份、還原、設定備份) |
| JobStatus_s |Text |已完成之作業的狀態 (例如，已完成、失敗) |
| JobFailureCode_s |Text |由於發生作業失敗而產生的失敗碼字串 |
| JobStartDateTime_s |日期/時間 |開始執行作業的日期和時間 |
| BackupStorageDestination_s |Text |備份儲存體的目的地 (例如，雲端、磁碟)  |
| AdHocOrScheduledJob_s |Text | 若要指定作業是否為臨機操作或排程的欄位 |
| JobDurationInSecs_s | 數字 |總作業持續時間 (以秒為單位) |
| DataTransferredInMB_s | 數字 |此工作的資料轉送 (以 MB 為單位)|
| JobUniqueId_g |Text |用來識別作業的唯一識別碼 |
| RecoveryJobDestination_s |Text | 目的端的復原工作，會在復原資料 |
| RecoveryJobRPDateTime_s |DateTime | 日期，也就是要復原的復原點建立時的時間 |
| RecoveryJobRPLocation_s |Text | 正在復原的復原點已儲存的位置|
| SourceSystem |Text |目前資料的來源系統 - Azure |
| resourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼|
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="policy"></a>原則

下表提供原則相關欄位的詳細資料。

| 欄位 | 数据类型 | 適用版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |
| SchemaVersion_s |Text ||此欄位代表目前版本的結構描述，它是**V2** |
| State_s |Text ||原則物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text ||執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此欄位代表目前作業的名稱 - Policy |
| Category |Text ||此欄位代表推送至 Azure 監視器記錄檔的診斷資料的類別目錄，則是 AzureBackupReport |
| Resource |Text ||這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| PolicyUniqueId_g |Text ||用來識別原則的唯一識別碼 |
| PolicyName_s |Text ||所定義原則的名稱 |
| BackupFrequency_s |Text ||備份的執行頻率 (例如，每日、每週) |
| BackupTimes_s |Text ||排定要備份的日期和時間 |
| BackupDaysOfTheWeek_s |Text ||一週中已排定要備份的日期 |
| RetentionDuration_s |整數 ||所設定備份的保留期間 |
| DailyRetentionDuration_s |整数 ||所配置备份的总保留时间（按天算） |
| DailyRetentionTimes_s |Text ||每日保留期的設定日期和時間 |
| WeeklyRetentionDuration_s |十進位數字 ||所配置备份的每周保留总时间（按周算） |
| WeeklyRetentionTimes_s |Text ||每週保留期的設定日期和時間 |
| WeeklyRetentionDaysOfTheWeek_s |Text ||一週中所選用於每週保留期的日期 |
| MonthlyRetentionDuration_s |十進位數字 ||所配置备份的总保留时间（按月算） |
| MonthlyRetentionTimes_s |Text ||每月保留期的設定日期和時間 |
| MonthlyRetentionFormat_s |Text ||每月保留期的設定類型 (例如，以天為基礎則每日、以週為基礎則每週) |
| MonthlyRetentionDaysOfTheWeek_s |Text ||一週中所選用於每月保留期的日期 |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||一個月中每月保留期的設定週數 (例如，第一週、最後一週等等)。 |
| YearlyRetentionDuration_s |十進位數字 ||所設定備份的總保留期間 (以年為單位) |
| YearlyRetentionTimes_s |Text ||每年保留期的設定日期和時間 |
| YearlyRetentionMonthsOfTheYear_s |Text ||一年中所選用於每年保留期的月數 |
| YearlyRetentionFormat_s |Text ||每年保留期的設定類型 (例如，以天為基礎則每日、以週為基礎則每週) | |
| YearlyRetentionDaysOfTheMonth_s |Text ||一個月中所選用於每年保留期的日期 |
| SynchronisationFrequencyPerDay_s |整數 |v2|檔案備份 synchronized SC DPM 和 MABS 一天中的次數 |
| DiffBackupFormat_s |Text |v2|格式為差異備份的的 SQL Azure VM 備份的 |
| DiffBackupTime_s |時間 |v2|Sql Azure VM 備份中的差異備份的時間|
| DiffBackupRetentionDuration_s |十進位數字 |v2|Sql Azure VM 備份中的差異備份的保留期間|
| LogBackupFrequency_s |十進位數字 |v2|Sql 記錄備份的頻率|
| LogBackupRetentionDuration_s |十進位數字 |v2|Sql Azure VM 備份的記錄備份的保留期間|
| DiffBackupDaysofTheWeek_s |Text |v2|Sql Azure VM 備份中的差異備份一週天數|
| SourceSystem |Text ||目前資料的來源系統 - Azure |
| resourceId |Text ||正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text ||要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text ||要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text ||要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||要收集其資料的資源類型。 例如保存庫 |

### <a name="policyassociation"></a>PolicyAssociation

下表提供與各種實體關聯的原則相關詳細資料。

| 欄位 | 数据类型 | 適用版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |
| SchemaVersion_s |Text ||此欄位代表目前版本的結構描述，它是**V2** |
| State_s |Text ||原則物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text ||執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此欄位代表目前作業的名稱 - PolicyAssociation |
| Category |Text ||此欄位代表推送至 Azure 監視器記錄檔的診斷資料的類別目錄，則是 AzureBackupReport |
| Resource |Text ||這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| PolicyUniqueId_g |Text ||用來識別原則的唯一識別碼 |
| VaultUniqueId_s |Text ||此原則所屬之保存庫的唯一 ID |
| BackupManagementServerUniqueId_s |Text |v2 |如果適用的話，將受到保護，透過的欄位來唯一識別備份管理伺服器備份項目        |
| SourceSystem |Text ||目前資料的來源系統 - Azure |
| resourceId |Text ||正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text ||要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text ||要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text ||要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||要收集其資料的資源類型。 例如保存庫 |

### <a name="protected-container"></a>受保護的容器

下表提供基本欄位有關受保護的容器。 （將 ProtectedServer 處於 v1）

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | 欄位來唯一識別受保護的容器 |
| ProtectedContainerOSType_s |Text |受保護的容器的 OS 類型 |
| ProtectedContainerOSVersion_s |Text |OS 版本的受保護的容器 |
| AgentVersion_s |Text |版本號碼的代理程式備份或保護代理程式 （如果是 SC DPM 和 MABS） |
| BackupManagementType_s |Text |用於執行備份的提供者類型 (例如，IaaSVM、FileFolder) |
| EntityState_s |Text |受保護伺服器物件的目前狀態 (例如，作用中、已刪除) |
| ProtectedContainerFriendlyName_s |Text |受保護伺服器的易記名稱 |
| ProtectedContainerName_s |Text |受保護的容器名稱 |
| ProtectedContainerWorkloadType_s |Text |例如，IaaSVMContainer 備份的受保護的容器型別 |
| ProtectedContainerLocation_s |Text |受保護的容器是否位於的內部或在 Azure 中 |
| ProtectedContainerType_s |Text |受保護的容器是否為伺服器或容器 |

### <a name="storage"></a>儲存體

下表提供儲存體相關欄位的詳細資料。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| CloudStorageInBytes_s |十进制数 |備份所使用的雲端備份儲存體，在計算時會以最後的值為依據 |
| ProtectedInstances_s |十進位數字 |用於在帳單中計算前端儲存體的受保護執行個體數目，在計算時會以最後的值為依據 |
| EventName_s |Text |此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |
| SchemaVersion_s |Text |此欄位代表目前版本的結構描述，它是**V2** |
| State_s |Text |儲存體物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text |此欄位代表目前作業的名稱 - Storage |
| Category |Text |此欄位代表推送至 Azure 監視器記錄檔的診斷資料的類別目錄，則是 AzureBackupReport |
| Resource |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedServerUniqueId_s |Text |所計算儲存體之受保護伺服器的唯一 ID |
| VaultUniqueId_s |Text |會計算儲存體保存庫的唯一 ID |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| resourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="storageassociation"></a>StorageAssociation

下表提供連接到其他實體的 儲存體的基本 「 儲存體相關欄位。

| 欄位 | 数据类型 | 描述 |
| --- | --- |  --- |
| StorageUniqueId_s |Text |用來識別儲存體實體的唯一識別碼 |
| SchemaVersion_s |Text |此欄位代表目前版本的結構描述，它是**V2** |
| BackupItemUniqueId_s |Text |用來識別備份的項目與儲存體實體相關的唯一識別碼 |
| BackupManagementServerUniqueId_s |Text |用來識別儲存體與實體相關的備份管理伺服器的唯一識別碼|
| VaultUniqueId_s |Text |用來識別保存庫儲存體與實體相關的唯一識別碼|
| StorageConsumedInMBs_s |數字|在對應的儲存體中對應的備份項目所耗用的儲存體的大小 |
| StorageAllocatedInMBs_s |數字 |對應備份類型的項目中對應的儲存體磁碟所配置的儲存體的大小|

### <a name="vault"></a>保存庫

下表提供保存庫相關欄位的詳細資料。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |
| SchemaVersion_s |Text |此欄位代表目前版本的結構描述，它是**V2** |
| State_s |Text |保存庫物件的目前狀態 (例如，作用中、已刪除) |
| OperationName |Text |此欄位代表目前作業的名稱 - Vault |
| Category |Text |此欄位代表推送至 Azure 監視器記錄檔的診斷資料的類別目錄，則是 AzureBackupReport |
| Resource |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| VaultUniqueId_s |Text |保存庫的唯一 ID |
| VaultName_s |Text |保存庫名稱 |
| AzureDataCenter_s |Text |保存庫所在的資料中心 |
| StorageReplicationType_s |Text |保存庫的儲存體複寫類型 (例如，GeoRedundant) |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| resourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="backup-management-server"></a>備份管理伺服器

下表提供基本欄位有關備份管理伺服器。

|欄位  |数据类型  | 描述  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |備份管理伺服器的名稱        |
|AzureBackupAgentVersion_s     |Text         |備份管理伺服器上的 Azure 備份代理程式版本          |
|BackupManagmentServerVersion_s     |Text         |備份管理伺服器的版本|
|BackupManagmentServerOSVersion_s     |Text            |備份管理伺服器的 OS 版本|
|BackupManagementServerType_s     |Text         |備份管理伺服器，與 MABS、 SC DPM 的類型|
|BackupManagmentServerUniqueId_s     |Text         |可唯一識別備份管理伺服器的欄位       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

此資料表指定磁碟區是與相關聯的工作負載。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| StorageUniqueId_s |Text |用來識別儲存體實體的唯一識別碼 |
| BackupItemType_s |Text |此磁碟區的慣用儲存體工作負載|

### <a name="protectedinstance"></a>ProtectedInstance

下表提供基本的受保護執行個體相關的欄位。

| 欄位 | 数据类型 |適用版本 | 描述 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|用來識別備份的項目適用於 Vm 的唯一識別碼使用備份 DPM，MABS|
| ProtectedContainerUniqueId_s |Text |v2|用來識別受保護的容器的 Vm 以外的所有內容的唯一識別碼使用備份 DPM，MABS|
| ProtectedInstanceCount_s |Text |v2|計數的受保護執行個體相關聯的備份項目或受保護的容器，該日期時間|

### <a name="recoverypoint"></a>RecoveryPoint

下表提供基本的復原點相關的欄位。

| 欄位 | 数据类型 | 描述 |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |用來識別備份的項目適用於 Vm 的唯一識別碼使用備份 DPM，MABS|
| OldestRecoveryPointTime_s |Text |備份的項目，最舊的復原點的日期時間|
| OldestRecoveryPointLocation_s |Text |備份項目的最早的復原點的位置|
| LatestRecoveryPointTime_s |Text |備份項目的最新的復原點的日期時間|
| LatestRecoveryPointLocation_s |Text |備份項目的最新的復原點的位置|

## <a name="next-steps"></a>後續步驟

一旦您檢閱的資料模型時，就可以開始[建立自訂查詢](../azure-monitor/learn/tutorial-logs-dashboards.md)來建置您自己的儀表板的 Azure 監視器記錄檔中。
