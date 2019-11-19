---
title: Power BI 資料模型
description: 本文將討論 Azure 備份報告的 Power BI 資料模型詳細資料。
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: a2f06da16280070448d7b42dc5e1dcfc46354cfa
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172809"
---
# <a name="data-model-for-azure-backup-reports"></a>Azure 備份報告的資料模型

本文說明用於建立 Azure 備份報告的 Power BI 資料模型。 透過此資料模型，您就可以根據相關欄位來篩選現有報告，而且更重要的是，可以在模型中使用資料表和欄位來建立您自己的報告。

## <a name="creating-new-reports-in-power-bi"></a>在 Power BI 中建立新的報告

Power BI 提供了自訂功能，以供您[使用資料模型來建立報告](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)。

## <a name="using-azure-backup-data-model"></a>使用 Azure 備份資料模型

您可以使用下列提供作為資料模型一部分的欄位，來建立報告及自訂現有報告。

### <a name="alert"></a>警示

下表提供基本欄位和各種警示相關欄位的匯總。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| #AlertsCreatedInPeriod |整數 |所選時段內建立的警示數目 |
| %ActiveAlertsCreatedInPeriod |百分比 |所選時段內作用中警示的百分比 |
| %CriticalAlertsCreatedInPeriod |百分比 |所選時段內重大警示的百分比 |
| AlertOccurrenceDate |Date |警示的建立日期 |
| AlertSeverity |文字 |警示的嚴重性。 例如，重大 |
| AlertStatus |文字 |警示的狀態。 例如，Active |
| AlertType |文字 |產生的警示類型。 例如，備份 |
| AlertUniqueId |文字 |所產生警示的唯一識別碼 |
| AsOnDateTime |日期/時間 |所選資料列的最新重新整理時間 |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |十進位數字 |所選時段內的警示解析平均時間 (以分鐘為單位) |
| EntityState |文字 |警示物件的目前狀態。 例如，作用中、已刪除 |

### <a name="backup-item"></a>備份項目

下表提供基本欄位和各種備份項目相關欄位的彙總。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| #BackupItems |整數 |備份項目數目 |
| #UnprotectedBackupItems |整數 |已停止保護或已設定要備份卻未開始備份的備份項目數目|
| AsOnDateTime |日期/時間 |所選資料列的最新重新整理時間 |
| BackupItemFriendlyName |文字 |備份項目的易記名稱 |
| BackupItemId |文字 |備份專案的識別碼 |
| BackupItemName |文字 |備份項目名稱 |
| BackupItemType |文字 |備份專案的類型。 例如，VM、Filefolder) |
| EntityState |文字 |備份專案物件的目前狀態。 例如，作用中、已刪除 |
| LastBackupDateTime |日期/時間 |所選備份項目的最後一次備份時間 |
| LastBackupState |文字 |所選備份專案的上次備份狀態。 例如，成功、失敗 |
| LastSuccessfulBackupDateTime |日期/時間 |所選備份項目最後一次成功備份的時間 |
| ProtectionState |文字 |備份專案的目前保護狀態。 例如，Protected、Protectionstopped) |

### <a name="calendar"></a>行事曆

下表提供行事曆相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| Date |Date |所選用於篩選資料的日期 |
| DateKey |文字 |每個日期項目的唯一索引鍵 |
| DayDiff |十進位數字 |篩選資料的日差。 例如，0表示當天的資料，-1 表示前一天的資料，0和-1 表示目前和前一天的資料  |
| 月 |文字 |一年中所選用於篩選資料的月份，月份的計算會從第一天開始，到第 31 天結束 |
| MonthDate | Date |當月結束時，該月份所選用於篩選資料的日期 |
| MonthDiff |十進位數字 |篩選資料的月份差異。 例如，0表示當月的資料，-1 表示上個月的資料，0和-1 表示目前和上個月的資料 |
| 週 |文字 |所選用於篩選資料的週別，一週的計算會從星期日開始，到星期六結束 |
| WeekDate |Date |當週結束時，該週所選用於篩選資料的日期 |
| WeekDiff |十進位數字 |篩選資料的周間差異。 例如，0表示目前的周資料，-1 表示上星期的資料，0和-1 表示目前和上周的資料 |
| Year |文字 |所選用於篩選資料的日曆年度 |
| YearDate |Date |當年度結束時，該年度所選用於篩選資料的日期 |

### <a name="job"></a>作業

下表提供基本欄位和各種作業相關欄位的彙總。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| #JobsCreatedInPeriod |整數 |所選時段內建立的作業數目 |
| %FailuresForJobsCreatedInPeriod |百分比 |所選時段內整體作業失敗百分比 |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |十進位數字 |在所選時段內建立的**備份**作業中，其傳輸之資料 (以 MB 為單位) 的第 80 個百分位數值 |
| AsOnDateTime |日期/時間 |所選資料列的最新重新整理時間 |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |十進位數字 |所選時段內建立之**備份作業已完成者**的平均完成時間 (以分鐘為單位) |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |十進位數字 |所選時段內建立之**還原作業已完成者**的平均完成時間 (以分鐘為單位) |
| BackupStorageDestination |文字 |備份儲存體的目的地。 例如，雲端、磁片  |
| EntityState |文字 |工作物件的目前狀態。 例如，作用中、已刪除 |
| JobFailureCode |文字 |由於發生作業失敗而產生的失敗碼字串 |
| JobOperation |文字 |執行工作的作業。 例如，備份、還原、設定備份 |
| JobStartDate |Date |開始執行作業的日期 |
| JobStartTime |時間 |開始執行作業的時間 |
| JobStatus |文字 |已完成工作的狀態。 例如，Completed、Failed |
| JobUniqueId |文字 |用來識別作業的唯一識別碼 |

### <a name="policy"></a>原則

下表提供基本欄位和各種原則相關欄位的彙總。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| #Policies |整數 |系統中存在的備份原則數目 |
| #PoliciesInUse |整數 |目前正用於設定備份的原則數目 |
| AsOnDateTime |日期/時間 |所選資料列的最新重新整理時間 |
| BackupDaysOfTheWeek |文字 |一週中已排定要備份的日期 |
| BackupFrequency |文字 |執行備份的頻率。 例如，每日、每週 |
| BackupTimes |文字 |排定要備份的日期和時間 |
| DailyRetentionDuration |整數 |所設定備份的總保留期間 (以天為單位) |
| DailyRetentionTimes |文字 |每日保留期的設定日期和時間 |
| EntityState |文字 |原則物件的目前狀態。 例如，作用中、已刪除 |
| MonthlyRetentionDaysOfTheMonth |文字 |一個月中所選用於每月保留期的日期 |
| MonthlyRetentionDaysOfTheWeek |文字 |一週中所選用於每月保留期的日期 |
| MonthlyRetentionDuration |十進位數字 |所設定備份的總保留期間 (以月為單位) |
| MonthlyRetentionFormat |文字 |每月保留期的設定類型。 例如，每日以日為基礎，每週以周為基礎 |
| MonthlyRetentionTimes |文字 |每月保留期的設定日期和時間 |
| MonthlyRetentionWeeksOfTheMonth |文字 |設定每月保留期的月份周數。 例如，First、Last 等等。 |
| PolicyName |文字 |所定義原則的名稱 |
| PolicyUniqueId |文字 |用來識別原則的唯一識別碼 |
| RetentionType |文字 |保留原則的類型。 例如，每日、每週、每月、每年 |
| WeeklyRetentionDaysOfTheWeek |文字 |一週中所選用於每週保留期的日期 |
| WeeklyRetentionDuration |十進位數字 |所設定備份的每週保留總期間 (以週為單位) |
| WeeklyRetentionTimes |文字 |每週保留期的設定日期和時間 |
| YearlyRetentionDaysOfTheMonth |文字 |一個月中所選用於每年保留期的日期 |
| YearlyRetentionDaysOfTheWeek |文字 |一週中所選用於每年保留期的日期 |
| YearlyRetentionDuration |十進位數字 |所設定備份的總保留期間 (以年為單位) |
| YearlyRetentionFormat |文字 |年度保留期的設定類型。 例如，每日以日為基礎，每週以周為基礎 |
| YearlyRetentionMonthsOfTheYear |文字 |一年中所選用於每年保留期的月數 |
| YearlyRetentionTimes |文字 |每年保留期的設定日期和時間 |
| YearlyRetentionWeeksOfTheMonth |文字 |設定每年保留期的月份周數。 例如，First、Last 等等。 |

### <a name="protected-server"></a>受保護的伺服器

下表提供基本欄位和各種受保護伺服器相關欄位的彙總。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| #ProtectedServers |整數 |受保護的伺服器數目 |
| AsOnDateTime |日期/時間 |所選資料列的最新重新整理時間 |
| AzureBackupAgentOSType |文字 |Azure 備份代理程式的 OS 類型 |
| AzureBackupAgentOSVersion |文字 |Azure 備份代理程式的 OS 版本 |
| AzureBackupAgentUpdateDate |文字 |代理程式備份代理程式的更新日期 |
| AzureBackupAgentVersion |文字 |代理程式備份版本的版本號碼 |
| BackupManagementType |文字 |執行備份的提供者類型。 例如，IaaSVM、Filefolder) |
| EntityState |文字 |受保護伺服器物件的目前狀態。 例如，作用中、已刪除 |
| ProtectedServerFriendlyName |文字 |受保護伺服器的易記名稱 |
| ProtectedServerName |文字 |受保護伺服器的名稱 |
| ProtectedServerType |文字 |已備份的受保護伺服器類型。 例如，IaaSVMContainer |
| ProtectedServerName |文字 |備份項目所屬受保護伺服器的名稱 |
| RegisteredContainerId |文字 |已註冊備份的容器識別碼 |

### <a name="storage"></a>儲存體

下表提供基本欄位和各種儲存體相關欄位的彙總。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| #ProtectedInstances |十進位數字 |用於在帳單中計算前端儲存體的受保護執行個體數目，在計算時會以所選時間內的最後數值為依據 |
| AsOnDateTime |日期/時間 |所選資料列的最新重新整理時間 |
| CloudStorageInMB |十進位數字 |備份所使用的雲端備份儲存體，在計算時會以所選時間內的最後數值為依據 |
| EntityState |文字 |物件的目前狀態。 例如，作用中、已刪除 |
| LastUpdatedDate |Date |所選資料列的上次更新日期 |

### <a name="time"></a>時間

下表提供時間相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| 小時 |時間 |一天中的小時。 例如，1:00:00 PM |
| HourNumber |十進位數字 |一天中的小時數。 例如，13.00 |
| 分鐘 |十進位數字 |一小時的分鐘數 |
| PeriodOfTheDay |文字 |一天中的時間週期位置。 例如，12-3 AM |
| 時間 |時間 |一天中的時間。 例如，12:00:01 AM |
| TimeKey |文字 |用來代表時間的機碼值 |

### <a name="vault"></a>保存庫

下表提供基本欄位和各種保存庫相關欄位的彙總。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| #Vaults |整數 |保存庫數目 |
| AsOnDateTime |日期/時間 |所選資料列的最新重新整理時間 |
| AzureDataCenter |文字 |保存庫所在的資料中心 |
| EntityState |文字 |保存庫物件的目前狀態。 例如，作用中、已刪除 |
| StorageReplicationType |文字 |保存庫的儲存體複寫類型。 例如，異地備援 |
| SubscriptionId |文字 |選取要產生報表之客戶的訂用帳戶識別碼 |
| VaultName |文字 |保存庫名稱 |
| VaultTags |文字 |與保存庫相關聯的標記 |

## <a name="next-steps"></a>後續步驟

在檢閱過用於建立 Azure 備份報告的資料模型後，請參閱下列文章，以深入了解如何在 Power BI 中建立和檢視報告。

* [在 Power BI 中建立報告](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [在 Power BI 中篩選報告](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
