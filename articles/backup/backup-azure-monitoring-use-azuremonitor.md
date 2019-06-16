---
title: Azure 備份：使用 Azure 監視器監視 Azure 備份
description: 監視 Azure 備份的工作負載，並建立使用 Azure 監視器的自訂警示
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics;Azure 備份;警示;診斷設定。動作群組
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1e85b633024b5a3e85874707ae9a1f068e7a328d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808517"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>監視大規模使用 Azure 監視器

[內建的監視與警示文章](backup-azure-monitoring-built-in-monitor.md)所列的監視和警示的單一復原服務保存庫和其中會提供不需要任何額外的管理基礎結構中的功能。 不過在下列案例中的內建的服務會受到限制。

- 從訂用帳戶之間的多個 RS 保存庫的監視資料
- 如果電子郵件不是慣用的通知通道
- 如果使用者想要收到警示的更多案例
- 如果您想要檢視從內部部署元件，例如 System Center DPM (SC DPM) 的資訊在 Azure 中，不會顯示在它[備份作業](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)或是[備份警示](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)入口網站中。

## <a name="using-log-analytics-workspace"></a>使用 Log Analytics 工作區

> [!NOTE]
> 從 Azure VM 備份，MAB 代理程式、 System Center DPM (SC-DPM)，在 Azure Vm 中的 SQL 備份資料會被抽出透過診斷設定 Log Analytics 工作區。 支援 Azure 檔案共用備份，Microsoft Azure 備份伺服器 (MABS) 即將推出。

我們會利用兩個 Azure 服務層的能力**diagnostic-settings** （若要將資料從多個 Azure Resource Manager 資源傳送至另一個資源） 和**Log Analytics** (LA-產生您可以在其中定義使用動作群組的其他通知通道設定自訂警示） 用於大規模監視。 下列各節詳細說明如何使用 LA 大規模監視 Azure 備份。

### <a name="configuring-diagnostic-settings"></a>設定診斷設定

Azure Resource Manager 資源，例如 Azure 復原服務保存庫記錄的所有可能資訊已排程的作業和使用者觸發作業做為診斷資料。 按一下 監視 區段中的 診斷設定 並指定 RS 保存庫的診斷資料的目標。

![RS 保存庫診斷設定 LA 做為目標](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

您可以選取 LA 工作區，做為目標的其他訂用帳戶。 *藉由選取多個 RS 保存庫相同的 LA 工作區，您可以跨訂用帳戶，在單一位置監視保存庫。* 選取 'AzureBackupReport' 與記錄檔為聲道所有 Azure 備份相關的資訊 LA 工作區。

> [!IMPORTANT]
> 您已完成設定之後，您應該等待 24 小時來完成初始資料推送。 之後所有事件都會推送中所述[頻率區段](#diagnostic-data-update-frequency)。

### <a name="deploying-solution-to-log-analytics-workspace"></a>將解決方案部署到 Log Analytics 工作區

一旦資料位於 LA 工作區中，[部署的 GitHub 範本](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/)到 LA 將資料視覺化。 請確定您提供相同的資源群組、 工作區名稱，以及工作區位置，適當地識別工作區，並在其上安裝此範本。

### <a name="view-azure-backup-data-using-log-analytics-la"></a>檢視使用 Log Analytics (LA) 的 Azure 備份資料

一旦部署範本，來監視 Azure 備份解決方案會顯示在工作區摘要區域中。 您可以透過周遊

- Azure 監視器]-> [Insights] 區段下 [「 多 」，然後選擇相關的工作區或
- Log Analytics 工作區]-> [選取相關的工作區-> [一般] 區段下的 '工作區摘要'。

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

按一下圖格，設計工具的範本會開啟一系列有關這類的 Azure 備份的基本監視資料的圖形

#### <a name="all-backup-jobs"></a>所有的備份作業

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>還原作業

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>適用於 Azure 資源的內建的 Azure 備份警示

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>在內部部署資源的內建的 Azure 備份警示

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>作用中的資料來源

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>RS 保存庫的雲端儲存體

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

上面的圖形所隨附的範本，而且客戶可以自由地編輯/新增更多的圖形。

> [!IMPORTANT]
> 當您部署範本時，它基本上會建立唯讀鎖定，而且您需要加以移除，以編輯範本，並儲存。 若要移除鎖定，請查看在 Log Analytics 工作區的 [設定] 區段下的 [鎖定] 窗格中。

### <a name="create-alerts-using-log-analytics"></a>建立使用 Log Analytics 的警示

Azure 監視器 」 可讓使用者從 LA 工作區中，您可以建立自己的警示*運用 Azure 動作群組，以選取您慣用的通知機制*。 按一下任何圖表以開啟 LA 工作區的 [記錄] 區段上方***您可以在其中編輯查詢並建立它們之上的警示***。

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

按一下 [新增警示規則] 如上所示，將會開啟 Azure 監視器警示建立螢幕。

如您所見下面，資源已標示為 LA 工作區，並提供動作群組整合。

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> 請注意，提供建立查詢的相關影響定價[此處](https://azure.microsoft.com/pricing/details/monitor/)。

#### <a name="alert-condition"></a>警示條件

重要的層面是觸發警示的條件。 按一下 'Condition' 會自動載入 Kusto 查詢在 'Logs' 畫面如下所示，您可以加以編輯以符合您的案例。 中提供一些範例 Kusto 查詢[一節](#sample-kusto-queries)。

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

編輯 Kusto 查詢，如有必要，請選取適當的臨界值 （這會決定何時將會引發警示）、 右的期間 （時間範圍內執行查詢時），和頻率。 例如: 如果臨界值是大於 0 的期間是 5 分鐘，頻率為 5 分鐘，然後規則就會轉譯為 「 過去 5 分鐘內每隔 5 分鐘執行查詢和結果數目大於 0，如果選取的動作群組透過通知我 」

#### <a name="action-group-integration"></a>動作群組整合

動作群組會指定使用者可使用的通知通道。 按一下 [新建] 在 [動作群組] 區段會顯示可用的通知機制的清單。

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

深入了解[從 LA 工作區的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)和 關於[動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)從 Azure 監視器文件。

因此，您可以滿足所有的警示和監視從 LA 單獨的需求，或使用它作為內建的通知機制的增補技術。

### <a name="sample-kusto-queries"></a>Kusto 查詢範例

預設圖形可讓您 Kusto 查詢基本的情況下，您可以在其上建立警示。 您也可以修改，以取得您想要取得警示的資料。 這裡我們提供一些範例 Kusto 查詢您 'Logs'] 視窗中貼上，然後再建立的查詢上的 [警示。

#### <a name="all-successful-backup-jobs"></a>所有成功的備份作業

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>所有失敗的備份作業

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>所有成功的 Azure VM 備份作業

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-sql-log-backup-jobs"></a>所有成功的 SQL 記錄檔備份作業

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-mab-agent-backup-jobs"></a>所有成功的 MAB 代理程式備份工作

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

### <a name="diagnostic-data-update-frequency"></a>診斷資料更新頻率

從保存庫的診斷資料被抽出某些延隔時間的洛杉磯工作區。 每個事件抵達 LA 工作區***與推入從 RS 保存庫之後的 20-30 分鐘的延遲。***

- 只要在建立推送 （跨越所有解決方案） 內建備份服務警示。 這表示它們通常會出現在 LA 工作區之後的 20-30 分鐘延遲。
- 臨機操作備份工作和 （跨越所有解決方案） 的還原作業會盡速以它們推送***完成***。
- 排定的備份工作，從所有的解決方案 （除了 SQL 備份） 會盡速以它們推送***完成***。
- 進行 SQL 備份，因為我們可以有記錄備份每隔 15 分鐘，針對所有已完成排定備份工作，包括記錄檔，資訊會批次處理，並推送每隔 6 小時。
- 所有其他資訊設定，例如備份的項目、 原則、 復原點、 儲存體等跨所有解決方案都會推送**至少一天一次。**
- 備份設定，例如變更原則，編輯原則等的變更會觸發備份的所有相關資訊的推播。

## <a name="using-rs-vaults-activity-logs"></a>使用 RS 保存庫的活動記錄

您也可以使用活動記錄檔取得事件，例如備份成功的通知。

> [!CAUTION]
> **請注意這僅適用於 Azure VM 備份。** 您無法使用這個對話方塊為其他方案 Azure 備份代理程式，例如 SQL 備份，在 Azure 中，Azure 檔案等。

### <a name="sign-in-into-azure-portal"></a>登入 Azure 入口網站

登入 Azure 入口網站並前往 相關的 Azure 復原服務保存庫，按一下屬性中的 活動記錄 區段。

### <a name="identify-appropriate-log-and-create-alert"></a>找出適當的記錄檔，並建立警示

套用下圖中顯示的篩選，以確認您是否正在接收成功備份的活動記錄。 據此變更時間範圍以檢視記錄。

![對於 Azure VM 備份的活動記錄](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

按一下該作業名稱，它會顯示作業和相關的詳細資料。

![新增警示規則](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

按一下 [**新的警示規則**以開啟**建立規則**] 畫面中，您可以建立警示中所述步驟[文章](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)。

以下資源是復原服務保存庫本身，因此您必須針對您想在其中透過活動記錄檔通知的所有保存庫重複相同的動作。 條件不會有任何臨界值、 句點、 頻率由於這是以事件為基礎的警示。 只要產生相關的活動記錄檔時，會在引發警示。

## <a name="recommendation"></a>建議

***建立活動記錄檔中的所有警示和 LA 工作區可以在左側的 [警示] 窗格中檢視 Azure 監視器中。***

雖然您可以使用透過活動記錄檔的通知，***強烈建議要用於 LA 基於下列原因規模和不活動記錄檔監視的 Azure 備份服務***。

- **有限的情況：** 只適用於 Azure VM 備份，並且應該重複的每個 RS 保存庫。
- **定義符合：** 排定的備份活動的活動記錄檔的最新的定義不符合，並配合[診斷記錄](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs)。 這會導致非預期的影響，當透過活動記錄檔通道中提取的資料已變更為指向下方。
- **活動記錄檔通道的問題：** 我們已從復原服務保存庫上的 Azure 備份移至提取活動記錄的新模型。 不幸的是，移動的影響 Azure Sovereign 雲端中的活動記錄檔的產生。 如果 Azure Sovereign 雲端使用者建立或設定與透過 Azure 監視器活動記錄的任何警示，它們不會觸發。 此外，在所有 Azure 公用區域中，如果使用者要將復原服務活動記錄收集到 Log Analytics 工作區 (如[此處](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)所述)，則這些記錄也不會出現。

因此強烈建議使用記錄分析工作區來監視和警示大規模您所有的 Azure 備份保護工作負載。

## <a name="next-steps"></a>後續步驟

- 請參閱[Log analytics 資料模型](backup-azure-log-analytics-data-model.md)來建立自訂的查詢。
