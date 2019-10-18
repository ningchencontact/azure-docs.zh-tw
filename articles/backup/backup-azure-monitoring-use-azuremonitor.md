---
title: Azure 備份：使用 Azure 監視器監視 Azure 備份
description: 使用 Azure 監視器監視 Azure 備份工作負載並建立自訂警示。
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Log Analytics;Azure 備份;Alerts診斷設定;動作群組
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: dacurwin
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 2d460688392ae017c0d87ce60fa980701e5d47d3
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528187"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>使用 Azure 監視器進行大規模監視

Azure 備份在復原服務保存庫中提供內[建的監視和警示功能](backup-azure-monitoring-built-in-monitor.md)。 這些功能可供使用，而不需要任何額外的管理基礎結構。 但在下列案例中，這項內建服務會受到限制：

- 如果您在訂用帳戶之間監視多個復原服務保存庫中的資料
- 如果慣用的通知通道*不*是電子郵件
- 如果使用者想要更多案例的警示
- 如果您想要從 Azure 中的內部部署元件（例如 System Center Data Protection Manager）中查看資訊，而入口網站未顯示在 [[**備份作業**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)] 或 [[**備份警示**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)] 中

## <a name="using-log-analytics-workspace"></a>使用 Log Analytics 工作區

> [!NOTE]
> 來自 Azure VM 備份、Azure 備份代理程式、System Center Data Protection Manager、Azure Vm 中的 SQL 備份，以及 Azure 檔案儲存體共用備份的資料，會透過診斷設定抽出至 Log Analytics 工作區。

若要大規模監視/報告，您需要兩個 Azure 服務的功能。 *診斷設定*會將多個 Azure Resource Manager 資源的資料傳送至另一個資源。 *Log Analytics*會產生自訂警示，您可以在其中使用動作群組來定義其他通知通道。

下列各節將詳細說明如何使用 Log Analytics 來大規模監視 Azure 備份。

### <a name="configure-diagnostic-settings"></a>設定診斷設定

Azure Resource Manager 資源（例如復原服務保存庫）會記錄排程作業和使用者觸發作業的相關資訊，做為診斷資料。

在 [監視] 區段中，選取 [**診斷設定**]，並指定復原服務保存庫診斷資料的目標。

![復原服務保存庫的診斷設定，以 Log Analytics 為目標](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

您可以將來自另一個訂用帳戶的 Log Analytics 工作區作為目標。 若要在單一位置監視跨訂用帳戶的保存庫，請為多個復原服務保存庫選取相同的 Log Analytics 工作區。 若要將 Azure 備份與 Log Analytics 工作區相關的所有資訊通道，請選取 [ **AzureBackupReport** ] 作為記錄。

> [!IMPORTANT]
> 完成設定之後，您應該等待24小時，初始資料推送才能完成。 在初始資料推送之後，所有事件都會依照本文稍後的 [[頻率] 區段](#diagnostic-data-update-frequency)中的說明推送。

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>將解決方案部署到 Log Analytics 工作區

> [!IMPORTANT]
> 我們已發行更新的多重視圖[範本](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/)，可在 Azure 備份中進行以 LA 為基礎的監視和報告。 請注意，使用[先前解決方案](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/)的使用者，即使在部署新的解決方案之後，仍會在其工作區中繼續看到它。 不過，舊的解決方案可能會因為一些次要的架構變更而提供不正確的結果。 因此，使用者必須部署新的範本。

當資料位於 Log Analytics 工作區內之後，請將[GitHub 範本部署](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/)到 log analytics 以將資料視覺化。 若要正確地識別工作區，請確定您為其指定相同的資源群組、工作區名稱和工作區位置。 然後在工作區上安裝此範本。

### <a name="view-azure-backup-data-by-using-log-analytics"></a>使用 Log Analytics 來查看 Azure 備份資料

部署範本之後，Azure 備份中用於監視和報告的解決方案會顯示在工作區摘要區域中。 若要移至摘要，請遵循下列其中一個路徑：

- **Azure 監視器**：在 [**深入**解析] 區段中，選取 [**更多**]，然後選擇相關的工作區。
- **Log Analytics 工作區**：選取相關的工作區，然後在 **[一般**] 底下，選取 [**工作區摘要**]。

![Log Analytics 監視和報告磚](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

當您選取任何 [總覽] 磚時，可以查看進一步的資訊。 以下是您將會看到的一些報告：

- 非記錄備份作業

   ![備份作業的 Log Analytics 圖形](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- 來自 Azure 資源備份的警示

   ![還原作業的 Log Analytics 圖形](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

同樣地，只要按一下其他圖格，您就可以查看有關還原作業、雲端儲存體、備份專案、來自內部部署資源備份的警示，以及記錄備份作業的報告。

這些圖表會隨範本一起提供。 如有需要，您可以編輯圖形或加入更多圖形。

### <a name="create-alerts-by-using-log-analytics"></a>使用 Log Analytics 建立警示

在 Azure 監視器中，您可以在 Log Analytics 工作區中建立自己的警示。 在工作區中，您可以使用*Azure 動作群組*來選取您慣用的通知機制。

> [!IMPORTANT]
> 如需建立此查詢之成本的相關資訊，請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。

選取任何圖形以開啟 Log Analytics 工作區的 [**記錄**] 區段。 在 [**記錄**] 區段中，編輯查詢並對其建立警示。

![在 Log Analytics 工作區中建立警示](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

當您選取 [**新增警示規則**] 時，[Azure 監視器警示建立] 頁面隨即開啟，如下圖所示。 這裡的資源已標示為 Log Analytics 工作區，並提供動作群組整合。

![Log Analytics 警示-建立頁面](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>警示條件

警示的定義特性是它的觸發條件。 選取 [**條件**]，以在 [**記錄**] 頁面上自動載入 Kusto 查詢，如下圖所示。 您可以在這裡編輯條件，以符合您的需求。 如需詳細資訊，請參閱[範例 Kusto 查詢](#sample-kusto-queries)。

![設定警示條件](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

如有必要，您可以編輯 Kusto 查詢。 選擇 [閾值]、[期間] 和 [頻率]。 閾值會決定何時會引發警示。 期間是執行查詢的時間範圍。 例如，如果臨界值大於0，則期間為5分鐘，而頻率為5分鐘，則規則會每隔5分鐘執行一次查詢，並檢查前5分鐘。 如果結果數目大於0，您就會收到所選動作群組的通知。

#### <a name="alert-action-groups"></a>警示動作群組

使用動作群組來指定通知通道。 若要查看可用的通知機制，請在 [**動作群組**] 底下，選取 [**新建**]。

![[新增動作群組] 視窗中的可用通知機制](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

您可以單獨滿足 Log Analytics 中的所有警示和監視需求，也可以使用 Log Analytics 來補充內建通知。

如需詳細資訊，請參閱[使用 Azure 監視器建立、查看和記錄管理警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)和[在 Azure 入口網站中建立和管理動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。

### <a name="sample-kusto-queries"></a>範例 Kusto 查詢

預設圖形會提供您可在其中建立警示之基本案例的 Kusto 查詢。 您也可以修改查詢，以取得您想要收到警示的資料。 在 [**記錄**] 頁面中貼上下列範例 Kusto 查詢，然後在查詢上建立警示：

- 所有成功的備份作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- 所有失敗的備份作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- 所有成功的 Azure VM 備份作業

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

- 所有成功的 SQL 記錄備份作業

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

- 所有成功的 Azure 備份代理程式作業

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

保存庫中的診斷資料會抽出至 Log Analytics 工作區，並有一些延遲。 每個事件從復原服務保存庫推送後的*20 到30分鐘*都會抵達 Log Analytics 工作區。 以下是延遲的進一步詳細資料：

- 在所有解決方案中，備份服務的內建警示會在建立後立即推送。 因此，它們通常會在20到30分鐘後出現在 Log Analytics 工作區中。
- 在所有解決方案中，臨機操作備份作業和還原作業會在*完成*時立即推送。
- 對於 SQL 備份以外的所有解決方案，排程的備份作業會在*完成*時立即推送。
- 針對 SQL 備份，因為記錄備份可能每隔15分鐘發生一次，所以所有已完成的排程備份作業（包括記錄）的資訊每隔6小時會進行批次處理和推送。
- 在所有解決方案中，其他資訊（例如備份專案、原則、復原點、儲存體等等）都會一次推送至少*一次。*
- 備份設定的變更（例如變更原則或編輯原則）會觸發所有相關備份資訊的推送。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>使用復原服務保存庫的活動記錄

> [!CAUTION]
> 下列步驟只適用于*AZURE VM 備份。* 您無法將這些步驟用於 Azure 備份代理程式、Azure 中的 SQL 備份或 Azure 檔案儲存體等解決方案。

您也可以使用活動記錄來取得事件的通知，例如備份成功。 若要開始，請遵循下列步驟：

1. 登入 Azure 入口網站。
1. 開啟相關的復原服務保存庫。
1. 在保存庫的 [屬性] 中，開啟 [**活動記錄**] 區段。

若要識別適當的記錄並建立警示：

1. 藉由套用下圖所示的篩選器，確認您收到成功備份的活動記錄。 視需要變更**Timespan**值，以查看記錄。

   ![篩選以尋找 Azure VM 備份的活動記錄](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 選取作業名稱以查看相關的詳細資料。
1. 選取 [**新增警示規則**] 以開啟 [**建立規則**] 頁面。
1. 遵循[使用 Azure 監視器建立、查看和管理活動記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)中的步驟來建立警示。

   ![新增警示規則](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

這裡的資源是復原服務保存庫本身。 針對您想要透過活動記錄收到通知的所有保存庫，重複相同的步驟。 條件不會有閾值、期間或頻率，因為此警示是以事件為基礎。 一旦產生相關的活動記錄檔，就會引發警示。

## <a name="using-log-analytics-to-monitor-at-scale"></a>使用 Log Analytics 大規模監視

您可以在 Azure 監視器中，從活動記錄和 Log Analytics 工作區中查看建立的所有警示。 只要開啟左側的 [**警示**] 窗格即可。

雖然您可以透過活動記錄取得通知，但我們強烈建議使用 Log Analytics 而非活動記錄大規模進行監視。 原因如下：

- **有限案例**：透過活動記錄的通知僅適用于 Azure VM 備份。 必須為每個復原服務保存庫設定通知。
- **定義符合**：排程的備份活動不符合最新的活動記錄定義。 相反地，它會與[資源記錄](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace)對齊。 當流經活動記錄通道的資料變更時，這種對齊方式會導致非預期的效果。
- **活動記錄通道的問題**：在復原服務保存庫中，從 Azure 備份抽出的活動記錄會遵循新的模型。 可惜的是，這項變更會影響 Azure Government、Azure 德國和 Azure 中國世紀的活動記錄產生。 如果這些雲端服務的使用者在 Azure 監視器中，從活動記錄建立或設定任何警示，則不會觸發警示。 此外，在所有 Azure 公用區域中，如果使用者將復原[服務活動記錄收集到 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)，這些記錄就不會出現。

針對 Azure 備份保護的所有工作負載，使用 Log Analytics 工作區進行大規模監視和警示。

## <a name="next-steps"></a>後續步驟

若要建立自訂查詢，請參閱[Log Analytics 資料模型](backup-azure-log-analytics-data-model.md)。
