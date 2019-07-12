---
title: Azure 備份：使用 Azure 監視器監視 Azure 備份
description: 監視 Azure 備份的工作負載，並使用 Azure 監視器來建立自訂警示。
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics;Azure 備份;警示;診斷設定。動作群組
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786321"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>使用 Azure 監視器來監視規模

Azure 備份提供[內建的監視與警示功能](backup-azure-monitoring-built-in-monitor.md)復原服務保存庫中。 不需要任何額外的管理基礎結構提供這些功能。 不過，此內建的服務僅限於下列案例：

- 如果您可以監視從多個復原服務保存庫的資料跨訂用帳戶
- 如果慣用的通知通道*不*電子郵件
- 如果使用者要警示的更多案例
- 如果您想要檢視內部部署元件例如 System Center Data Protection Manager 中的資訊在 Azure 中，在入口網站不會出現在這[**備份作業**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)或是[ **備份警示**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>使用 Log Analytics 工作區

> [!NOTE]
> 從 Azure VM 備份，Azure 備份代理程式、 System Center Data Protection Manager、 SQL Azure Vm 中的備份和 Azure 檔案共用備份的資料是透過診斷設定 Log Analytics 工作區中抽出。 

若要監視規模，您需要兩個 Azure 服務的功能。 *診斷設定*資料從多個 Azure Resource Manager 資源傳送到另一個資源。 *Log Analytics*會產生自訂的警示，您可以使用動作群組來定義其他通知通道。 

下列各節詳細說明如何使用 Log Analytics 來大規模監視 Azure 備份。

### <a name="configure-diagnostic-settings"></a>設定診斷設定

Azure Resource Manager 資源，例如復原服務保存庫中，記錄關於已排程的作業和使用者觸發作業資訊做為診斷資料。 

在 [監視] 區段中，選取**診斷設定**並指定復原服務保存庫的診斷資料的目標。

![復原服務保存庫的診斷設定，目標 Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

您可以針對其他訂用帳戶的 Log Analytics 工作區。 若要跨訂用帳戶，在單一位置監視保存庫，請選取多個復原服務保存庫相同的 Log Analytics 工作區。 若要通道 Log Analytics 工作區與 Azure 備份的所有資訊，請選取**AzureBackupReport**與記錄檔。

> [!IMPORTANT]
> 完成設定之後，您應該等待 24 小時來完成初始資料推送。 初始資料推送之後，所有事件都會都推送中所述在本文中，稍後[頻率區段](#diagnostic-data-update-frequency)。

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>將方案部署到 Log Analytics 工作區

資料是在 Log Analytics 工作區中之後,[部署的 GitHub 範本](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/)至 Log Analytics 以將資料視覺化。 若要適當地識別工作區，請確定您提供相同的資源群組、 工作區名稱和工作區位置。 在工作區，然後安裝此範本。

> [!NOTE]
> 如果您沒有警示、 備份作業或還原作業在您的 Log Analytics 工作區中，您可能會看到入口網站中的"BadArgumentError 」 錯誤碼。 忽略此錯誤並繼續使用解決方案。 相關的資料類型啟動之後流動至工作區中，視覺效果會反映相同的與您不會再看到此錯誤。

### <a name="view-azure-backup-data-by-using-log-analytics"></a>使用 Log Analytics 檢視 Azure 備份資料

部署範本之後，監視 Azure 備份的解決方案會顯示在工作區摘要區域中。 若要移到摘要資訊，請遵循其中一個途徑：

- **Azure 監視器**：在  **Insights**區段中，選取**詳細**，然後選擇 相關的工作區。
- **Log Analytics 工作區**:選取相關的工作區，然後在 **一般**，選取**工作區摘要**。

![Log Analytics 監視圖格](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

當您選取 [監視] 圖格時，設計工具的範本會開啟從 Azure 備份的一系列的相關基本監視資料的圖形。 以下是一些您會看到圖形：

* 所有備份工作

   ![備份作業的記錄分析圖形](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* 還原作業

   ![還原作業的記錄分析圖表](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* 適用於 Azure 資源的內建 Azure 備份警示

   ![內建的 Azure 備份警示適用於 Azure 資源的記錄分析圖表](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* 在內部部署資源的內建的 Azure 備份警示

   ![在內部部署資源的內建的 Azure 備份警示的記錄分析圖表](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* 作用中的資料來源

   ![作用中的備份實體的記錄分析圖表](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* 復原服務保存庫的雲端儲存體

   ![復原服務保存庫的雲端儲存體的記錄分析圖表](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

這些圖形會提供範本。 您可以編輯圖表，或如果您需要新增更多的圖形。

> [!IMPORTANT]
> 當您部署範本時，您基本上建立唯讀鎖定。 若要編輯並儲存範本，您要移除鎖定。 您可以移除中的鎖定**設定**Log Analytics 工作區的區段，請在**鎖定**窗格。

### <a name="create-alerts-by-using-log-analytics"></a>使用 Log Analytics 中建立警示

在 Azure 監視器中，您可以建立自己的警示，Log Analytics 工作區中。 在工作區，您可以使用*Azure 動作群組*來選取您慣用的通知機制。 

> [!IMPORTANT]
> 建立此查詢的成本資訊，請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。

選取任何圖表以開啟**記錄檔**Log Analytics 工作區的區段。 在 **記錄檔**區段中，編輯查詢並在其上建立警示。

![在 Log Analytics 工作區中建立警示](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

當您選取**新的警示規則**，Azure 監視器警示建立頁面隨即開啟，如下圖所示。 以下資源已標示為 Log Analytics 工作區中，並提供動作群組整合。

![Log Analytics 警示建立頁面](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>警示條件

警示的鮮明這是其觸發條件。 選取 **條件**自動載入上的 Kusto 查詢**記錄**頁面如下圖所示。 這裡您可以編輯以符合您需求的條件。 如需詳細資訊，請參閱 <<c0> [ 範例 Kusto 查詢](#sample-kusto-queries)。

![設定警示的條件](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

如有必要，您可以編輯 Kusto 查詢。 選擇的臨界值、 期間和頻率。 臨界值會決定將會引發此警示。 期限是時間的的執行查詢時範圍。 例如，如果臨界值是大於 0 的期間是 5 分鐘、，頻率為 5 分鐘，然後執行規則查詢每隔 5 分鐘，檢閱過去 5 分鐘內。 如果大於 0 的結果數目，透過選取的動作群組被通知您。

#### <a name="alert-action-groups"></a>警示的動作群組

您可以使用動作群組來指定通知通道。 若要查看可用的通知機制，底下**動作群組**，選取**建立新**。

![在 [新增動作群組] 視窗中可用的通知機制](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

您可以滿足所有的警示和監視需求從 Log Analytics，或者您可以使用 Log Analytics 來補充內建的通知。

如需詳細資訊，請參閱 <<c0> [ 建立、 檢視及管理使用 Azure 監視器的記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)並[建立及管理在 Azure 入口網站中的動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。

### <a name="sample-kusto-queries"></a>Kusto 查詢範例

預設圖形可讓您 Kusto 查詢基本的情況下，您可以在其上建立警示。 您也可以修改您想要接獲通知的查詢取得的資料。 貼上下列 Kusto 查詢範例中的**記錄檔**頁面上，然後再建立的查詢上的 警示：

* 所有成功的備份作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* 所有失敗的備份作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* 所有成功的 Azure VM 備份作業

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

* 所有成功 SQL 記錄備份作業

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

* 所有成功的 Azure 備份代理程式作業

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

從保存庫的診斷資料被抽出某些延遲的 Log Analytics 工作區。 在 Log Analytics 工作區的每個事件抵達*20 至 30 分鐘*推送從復原服務保存庫之後。 以下是進一步延隔時間的相關詳細資料：

- 跨所有的解決方案，因為它們建立推送備份服務的內建的警示。 因此它們通常會出現在 Log Analytics 工作區在 20 至 30 分鐘後。
- 跨所有的解決方案，臨機操作備份作業和還原作業推送盡速以他們*完成*。
- SQL 備份以外的所有解決方案，排定的備份工作會盡速以它們推送*完成*。
- 進行 SQL 備份，因為記錄備份可能會發生每隔 15 分鐘，所有已完成排定備份工作，包括記錄檔，資訊是批次，而且推送每隔 6 小時。
- 跨所有的解決方案，例如備份的項目、 原則、 復原點、 儲存和等等的其他資訊推入至少*一天一次。*
- 備份組態 （例如變更原則或編輯原則） 中的變更會觸發備份的所有相關資訊的推播。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>使用復原服務保存庫的活動記錄檔

> [!CAUTION]
> 下列步驟只適用於*Azure VM 備份。* 您無法使用這些步驟的解決方案，例如 Azure 備份代理程式、 SQL Azure 或 Azure 檔案內的備份。

您也可以使用活動記錄檔取得事件，例如備份成功的通知。 若要開始，請遵循下列步驟：

1. 登入 Azure 入口網站。
1. 開啟相關的復原服務保存庫。 
1. 在保存庫的內容中，開啟**活動記錄檔**一節。

若要找出適當的記錄檔，並建立警示：

1. 確認要套用篩選，如下圖所示，以接收活動記錄為成功的備份。 變更**Timespan**檢視記錄的所需的值。

   ![若要尋找 Azure VM 備份的活動記錄篩選](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 選取 作業名稱，以查看相關的詳細資料。
1. 選取 **新的警示規則**來開啟**建立規則**頁面。 
1. 依照下列中的步驟中建立警示[建立、 檢視及管理使用 Azure 監視器的活動記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)。

   ![新增警示規則](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

以下資源是復原服務保存庫本身。 所有您要在其中透過活動記錄檔通知的保存庫，您必須重複相同的步驟。 條件不會有臨界值、 句號或頻率，因為此警示以事件為基礎。 只要產生相關的活動記錄檔時，會引發警示。

## <a name="using-log-analytics-to-monitor-at-scale"></a>使用 Log Analytics 來監視規模

您可以檢視從活動記錄 」 及 「 Azure 監視器中的 Log Analytics 工作區建立的所有警示。 只需開啟**警示**左邊的窗格。

雖然您可以取得透過活動記錄檔的通知，我們強烈建議使用 Log Analytics，而不是活動記錄檔用於大規模監視。 原因如下：

- **有限的案例**:透過活動記錄檔的通知只適用於 Azure VM 備份。 通知必須設定每個復原服務保存庫。
- **定義符合**:排定的備份活動不符合的活動記錄檔的最新定義。 相反地，使其與對齊[診斷記錄](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs)。 此一配合行為的活動流程資料記錄變更通道時，會導致未預期的影響。
- **活動記錄檔通道問題**:在 復原服務保存庫，抽出從 Azure 備份的活動記錄檔會遵循新的模型。 不幸的是，這項變更會影響在 Azure Government、 Azure 德國和 Azure 中國 21vianet 經營的活動記錄檔的產生。 如果這些雲端服務的使用者建立，或在 Azure 監視器中設定任何警示與活動記錄，未觸發警示。 此外，在所有 Azure 公用區域，如果使用者[收集到 Log Analytics 工作區的 復原服務活動記錄](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)，這些記錄不會出現。

使用 Log Analytics 工作區的監視和警示，Azure 備份所保護的所有工作負載的小數位數。

## <a name="next-steps"></a>後續步驟

若要建立自訂的查詢，請參閱[Log Analytics 資料模型](backup-azure-log-analytics-data-model.md)。
