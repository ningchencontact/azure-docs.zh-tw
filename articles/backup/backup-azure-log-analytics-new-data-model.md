---
title: Azure 備份診斷事件的資料模型
description: 此資料模型會參考將診斷事件傳送至 Log Analytics （LA）的資源特定模式。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 86416d0d74296069878aa7f33b549102a52ea488
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281073"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure 備份診斷事件的資料模型

## <a name="coreazurebackup"></a>CoreAzureBackup

下表提供核心備份實體的相關資訊，例如保存庫和備份專案。

| **欄位**                         | **資料類型** | **描述**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | 文字          | 正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼。 |
| OperationName                     | 文字          | 此欄位代表目前作業的名稱-BackupItem、BackupItemAssociation 或 ProtectedContainer。 |
| 類別                          | 文字          | 此欄位代表已推送至 Azure 監視器記錄的診斷資料類別。 例如，CoreAzureBackup。 |
| AgentVersion                      | 文字          | 代理程式備份或保護代理程式的版本號碼（如果是 SC DPM 和 MABS） |
| AzureBackupAgentVersion           | 文字          | 備份管理伺服器上 Azure 備份代理程式的版本 |
| AzureDataCenter                   | 文字          | 保存庫所在的資料中心                       |
| BackupItemAppVersion              | 文字          | 備份專案的應用程式版本                       |
| BackupItemFriendlyName            | 文字          | 備份專案的易記名稱                             |
| BackupItemName                    | 文字          | 備份專案的名稱                                      |
| BackupItemProtectionState         | 文字          | 備份專案的保護狀態                          |
| BackupItemFrontEndSize            | 文字          | 備份專案的前端大小                            |
| BackupItemType                    | 文字          | 備份專案的類型。 例如： VM、Filefolder)             |
| BackupItemUniqueId                | 文字          | 備份項目的唯一識別碼                         |
| BackupManagementServerType        | 文字          | 備份管理伺服器的類型，如 MABS，SC DPM     |
| BackupManagementServerUniqueId    | 文字          | 唯一識別備份管理伺服器的欄位      |
| BackupManagementType              | 文字          | 執行備份作業之伺服器的提供者類型。 例如，IaaSVM、Filefolder) |
| BackupManagementServerName        | 文字          | 備份管理伺服器的名稱                         |
| BackupManagementServerOSVersion   | 文字          | 備份管理伺服器的 OS 版本                   |
| BackupManagementServerVersion     | 文字          | 備份管理伺服器的版本                      |
| LatestRecoveryPointLocation       | 文字          | 備份專案的最新復原點位置    |
| LatestRecoveryPointTime           | DateTime      | 備份專案的最新復原點日期時間   |
| OldestRecoveryPointLocation       | 文字          | 備份專案的最舊復原點位置    |
| OldestRecoveryPointTime           | DateTime      | 備份專案的最新復原點日期時間   |
| PolicyUniqueId                    | 文字          | 用來識別原則的唯一識別碼                             |
| ProtectedContainerFriendlyName    | 文字          | 受保護伺服器的易記名稱                        |
| ProtectedContainerLocation        | 文字          | 受保護的容器是否位於內部部署或 Azure 中 |
| ProtectedContainerName            | 文字          | 受保護容器的名稱                            |
| ProtectedContainerOSType          | 文字          | 受保護容器的 OS 類型                          |
| ProtectedContainerOSVersion       | 文字          | 受保護容器的作業系統版本                        |
| ProtectedContainerProtectionState | 文字          | 受保護容器的保護狀態                  |
| ProtectedContainerType            | 文字          | 受保護的容器是否為伺服器或容器  |
| ProtectedContainerUniqueId        | 文字          | 唯一識別碼，用來識別受保護的容器，以及使用 DPM、MABS 備份的 Vm 除外的所有專案 |
| ProtectedContainerWorkloadType    | 文字          | 已備份的受保護容器類型。 例如，IaaSVMContainer |
| ProtectionGroupName               | 文字          | 備份專案受保護的保護組名，適用于 SC DPM 和 MABS （如果適用） |
| resourceGroupName                 | 文字          | 要收集之資料的資源群組（例如復原服務保存庫） |
| SchemaVersion                     | 文字          | 此欄位代表架構的目前版本，它是**V2** |
| SecondaryBackupProtectionState    | 文字          | 是否針對備份專案啟用次要保護  |
| State                             | 文字          | 備份專案物件的狀態。 例如，作用中、已刪除 |
| StorageReplicationType            | 文字          | 保存庫的儲存體複寫類型。 例如，異地備援 |
| SubscriptionId                    | 文字          | 收集資料的資源（例如復原服務保存庫）的訂用帳戶識別碼 |
| VaultName                         | 文字          | 保存庫名稱                                            |
| VaultTags                         | 文字          | 與保存庫資源相關聯的標記                    |
| VaultUniqueId                     | 文字          | 保存庫的唯一識別碼                             |
| SourceSystem                      | 文字          | 目前資料的來源系統-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

下表提供警示相關欄位的詳細資料。

| **欄位**                      | **資料類型** | **描述**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文字          | 要收集其相關資料的資源所具備的唯一識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                  | 文字          | 目前作業的名稱。 例如，Alert            |
| 類別                       | 文字          | 推送至 Azure 監視器記錄的診斷資料類別-AddonAzureBackupAlerts |
| AlertCode                      | 文字          | 唯一識別警示類型的程式碼                     |
| AlertConsolidationStatus       | 文字          | 識別警示是否為合併警示         |
| AlertOccurrenceDateTime        | DateTime      | 建立警示的日期和時間                     |
| AlertRaisedOn                  | 文字          | 引發警示之實體的類型                        |
| AlertSeverity                  | 文字          | 警示的嚴重性。 例如，重大                 |
| AlertStatus                    | 文字          | 警示的狀態。 例如，Active                     |
| AlertTimeToResolveInMinutes    | 數字        | 解決警示所花費的時間。 適用于作用中警示的空白。     |
| AlertType                      | 文字          | 警示的類型。 例如，備份                           |
| AlertUniqueId                  | 文字          | 所產生警示的唯一識別碼                    |
| BackupItemUniqueId             | 文字          | 與警示相關聯之備份專案的唯一識別碼 |
| BackupManagementServerUniqueId | 文字          | 用來唯一識別備份專案的欄位（如果適用的話） |
| BackupManagementType           | 文字          | 執行備份作業之伺服器的提供者類型，例如 IaaSVM、Filefolder) |
| CountOfAlertsConsolidated      | 數字        | 如果是合併警示，則會合並的警示數目  |
| ProtectedContainerUniqueId     | 文字          | 與警示相關聯之受保護伺服器的唯一識別碼 |
| RecommendedAction              | 文字          | 建議的動作以解決警示                      |
| SchemaVersion                  | 文字          | 架構的目前版本，例如**V2**            |
| State                          | 文字          | 警示物件的目前狀態 (例如，作用中、已刪除) |
| StorageUniqueId                | 文字          | 用來識別儲存體實體的唯一識別碼                |
| VaultUniqueId                  | 文字          | 用來識別與警示相關之保存庫的唯一識別碼    |
| SourceSystem                   | 文字          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

下表提供基本的受保護實例相關欄位。

| **欄位**                      | **資料類型** | **描述**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文字          | 要收集資料之資源的唯一識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                  | 文字          | 作業的名稱，例如 ProtectedInstance         |
| 類別                       | 文字          | 推送至 Azure 監視器記錄的診斷資料類別-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | 文字          | 備份專案的唯一識別碼                                 |
| BackupManagementServerUniqueId | 文字          | 用來唯一識別備份專案的欄位（如果適用的話） |
| BackupManagementType           | 文字          | 執行備份作業之伺服器的提供者類型，例如 IaaSVM、Filefolder) |
| ProtectedContainerUniqueId     | 文字          | 用來識別執行作業之受保護容器的唯一識別碼 |
| ProtectedInstanceCount         | 文字          | 該日期時間內相關聯的備份專案或受保護容器的受保護實例計數 |
| SchemaVersion                  | 文字          | 架構的目前版本，例如**V2**            |
| State                          | 文字          | 備份專案物件的狀態，例如 Active、Deleted |
| VaultUniqueId                  | 文字          | 與受保護的實例相關聯之受保護保存庫的唯一識別碼 |
| SourceSystem                   | 文字          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

下表提供作業相關欄位的詳細資料。

| **欄位**                      | **資料類型** | **描述**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文字          | 正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                  | 文字          | 此欄位代表目前作業的名稱 - Job    |
| 類別                       | 文字          | 此欄位代表已推送至 Azure 監視器記錄的診斷資料分類-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | 文字          | 指定作業為臨機操作或已排程的欄位           |
| BackupItemUniqueId             | 文字          | 唯一識別碼，用來識別與儲存體實體相關的備份專案 |
| BackupManagementServerUniqueId | 文字          | 唯一識別碼，用來識別與儲存體實體相關的備份管理伺服器 |
| BackupManagementType           | 文字          | 執行備份的提供者類型，例如，IaaSVM、此警示所屬的 Filefolder) |
| DataTransferredInMB            | 數字        | 此工作的資料轉送 (以 MB 為單位)                          |
| JobDurationInSecs              | 數字        | 總作業持續時間 (以秒為單位)                                |
| JobFailureCode                 | 文字          | 由於發生作業失敗而產生的失敗碼字串    |
| JobOperation                   | 文字          | 執行工作的作業，例如備份、還原、設定備份 |
| JobOperationSubType            | 文字          | 工作作業的子類型。 例如，記錄備份作業的情況下為 ' Log ' |
| JobStartDateTime               | DateTime      | 開始執行作業的日期和時間                       |
| JobStatus                      | 文字          | 已完成之作業的狀態 (例如，已完成、失敗)   |
| JobUniqueId                    | 文字          | 用來識別作業的唯一識別碼                                |
| ProtectedContainerUniqueId     | 文字          | 與警示相關聯之受保護伺服器的唯一識別碼 |
| RecoveryJobDestination         | 文字          | 復原工作的目的地，資料會在其中復原   |
| RecoveryJobRPDateTime          | DateTime      | 要復原之復原點的建立日期和時間 |
| RecoveryJobLocation            | 文字          | 要復原之復原點的儲存位置 |
| RecoveryLocationType           | 文字          | 復原位置的類型                                |
| SchemaVersion                  | 文字          | 架構的目前版本，例如**V2**            |
| State                          | 文字          | 警示物件的目前狀態（例如，作用中、已刪除） |
| VaultUniqueId                  | 文字          | 與警示相關聯之受保護保存庫的唯一識別碼 |
| SourceSystem                   | 文字          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

下表提供原則相關欄位的詳細資料。

| **欄位**                       | **資料類型**  | **描述**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | 文字           | 要收集資料之資源的唯一識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                   | 文字           | 作業的名稱，例如 [原則] 或 [PolicyAssociation] |
| 類別                        | 文字           | 推送至 Azure 監視器記錄的診斷資料類別-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | 文字           | 一週中已排定要備份的日期            |
| BackupFrequency                 | 文字           | 執行備份的頻率。 例如，每日、每週 |
| BackupManagementType            | 文字           | 執行備份作業之伺服器的提供者類型。 例如，IaaSVM、Filefolder) |
| BackupManagementServerUniqueId  | 文字           | 用來唯一識別備份專案的欄位（如果適用的話） |
| BackupTimes                     | 文字           | 排定要備份的日期和時間                     |
| DailyRetentionDuration          | 整數   | 所設定備份的總保留期間 (以天為單位)      |
| DailyRetentionTimes             | 文字           | 每日保留期的設定日期和時間            |
| DiffBackupDaysOfTheWeek         | 文字           | Azure VM 備份中 SQL 的差異備份周中的天數 |
| DiffBackupFormat                | 文字           | Azure VM 備份中 SQL 的差異備份格式   |
| DiffBackupRetentionDuration     | 十進位數字 | Azure VM 備份中 SQL 的差異備份保留期間 |
| DiffBackupTime                  | 時間           | Azure VM 備份中 SQL 的差異備份時間     |
| LogBackupFrequency              | 十進位數字 | SQL 記錄備份的頻率                            |
| LogBackupRetentionDuration      | 十進位數字 | Azure VM 備份中 SQL 記錄備份的保留期間 |
| MonthlyRetentionDaysOfTheMonth  | 文字           | 設定每月保留期的月份周數。  例如，First、Last 等等。 |
| MonthlyRetentionDaysOfTheWeek   | 文字           | 一週中所選用於每月保留期的日期              |
| MonthlyRetentionDuration        | 文字           | 所設定備份的總保留期間 (以月為單位)    |
| MonthlyRetentionFormat          | 文字           | 每月保留期的設定類型。 例如，每日以日為基礎，每週以周為基礎 |
| MonthlyRetentionTimes           | 文字           | 每月保留期的設定日期和時間           |
| MonthlyRetentionWeeksOfTheMonth | 文字           | 設定每月保留期的月份周數。   例如，First、Last 等等。 |
| PolicyName                      | 文字           | 所定義原則的名稱                                   |
| PolicyUniqueId                  | 文字           | 用來識別原則的唯一識別碼                             |
| PolicyTimeZone                  | 文字           | 在記錄中指定原則時間欄位的時區 |
| RetentionDuration               | 文字           | 所設定備份的保留期間                    |
| RetentionType                   | 文字           | 保留類型                                            |
| SchemaVersion                   | 文字           | 此欄位代表架構的目前版本，它是**V2** |
| State                           | 文字           | 原則物件的目前狀態。 例如，作用中、已刪除 |
| SynchronisationFrequencyPerDay  | 整數   | 在一天中，SC DPM 和 MABS 的檔案備份已同步處理的次數 |
| VaultUniqueId                   | 文字           | 此原則所屬之保存庫的唯一識別碼          |
| WeeklyRetentionDaysOfTheWeek    | 文字           | 一週中所選用於每週保留期的日期               |
| WeeklyRetentionDuration         | 十進位數字 | 已設定備份的每週保留持續時間總計（以周為單位） |
| WeeklyRetentionTimes            | 文字           | 每週保留期的設定日期和時間            |
| YearlyRetentionDaysOfTheMonth   | 文字           | 一個月中所選用於每年保留期的日期             |
| YearlyRetentionDaysOfTheWeek    | 文字           | 一週中所選用於每年保留期的日期               |
| YearlyRetentionDuration         | 十進位數字 | 所設定備份的總保留期間 (以年為單位)     |
| YearlyRetentionFormat           | 文字           | 每年保留的設定類型（例如，每日以日為基礎，以每週為基礎） |
| YearlyRetentionMonthsOfTheYear  | 文字           | 一年中所選用於每年保留期的月數             |
| YearlyRetentionTimes            | 文字           | 每年保留期的設定日期和時間            |
| YearlyRetentionWeeksOfTheMonth  | 文字           | 為每年保留選取的月份周數             |
| SourceSystem                    | 文字           | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

下表提供儲存體相關欄位的詳細資料。

| **欄位**                      | **資料類型** | **描述**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 文字          | 正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                  | 文字          | 此欄位代表目前作業的名稱-儲存體或 StorageAssociation |
| 類別                       | 文字          | 此欄位代表已推送至 Azure 監視器記錄的診斷資料分類-AddonAzureBackupStorage |
| BackupItemUniqueId             | 文字          | 唯一識別碼，用來識別使用 DPM （MABS）備份之 Vm 的備份專案 |
| BackupManagementServerUniqueId | 文字          | 用來唯一識別備份專案的欄位（如果適用的話） |
| BackupManagementType           | 文字          | 執行備份作業之伺服器的提供者類型。 例如，IaaSVM、Filefolder) |
| PreferredWorkloadOnVolume      | 文字          | 此磁片區為慣用儲存體的工作負載      |
| ProtectedContainerUniqueId     | 文字          | 與警示相關聯之受保護伺服器的唯一識別碼 |
| SchemaVersion                  | 文字          | 架構的版本。 例如， **V2**                   |
| State                          | 文字          | 備份專案物件的狀態。 例如，作用中、已刪除 |
| StorageAllocatedInMBs          | 數字        | 對應的備份專案在類型磁片的對應儲存體中所配置的儲存空間大小 |
| StorageConsumedInMBs           | 數字        | 對應的儲存區中對應的備份專案所使用的儲存體大小 |
| StorageName                    | 文字          | 儲存體實體的名稱。 例如，E:\                      |
| StorageTotalSizeInGBs          | 文字          | 儲存體實體所耗用的儲存體大小總計（以 GB 為單位）     |
| StorageType                    | 文字          | 儲存體類型，例如雲端、磁片區、磁片             |
| StorageUniqueId                | 文字          | 用來識別儲存體實體的唯一識別碼                |
| VaultUniqueId                  | 文字          | 用來識別與儲存體實體相關之保存庫的唯一識別碼 |
| VolumeFriendlyName             | 文字          | 存放磁片區的易記名稱                          |
| SourceSystem                   | 文字          | 目前資料的來源系統 - Azure                    |

## <a name="next-steps"></a>後續步驟

- [瞭解如何將診斷資料傳送至 Log Analytics](https://aka.ms/AA6il6r)