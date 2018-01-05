---
title: "Azure Analysis Services 的 Diganostic 記錄 |Microsoft 文件"
description: "深入了解 Azure Analysis Services 的診斷記錄設定。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/29/2017
ms.author: owend
ms.openlocfilehash: 02c25de980b399812676285ad3f87f60af93265f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="setup-diagnostic-logging"></a>安裝程式的診斷記錄

任何 Analysis Services 方案的重要部分會監視您的伺服器執行的方式。 與[Azure 資源的診斷記錄檔](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)，您可以監視，並將日誌傳送至[Azure 儲存體](https://azure.microsoft.com/services/storage/)，它們以進行串流處理[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，並將它們以匯出[記錄檔分析](https://azure.microsoft.com/services/log-analytics/)，屬於[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)。 

![透過 Log Analytics 將診斷記錄送至儲存體、事件中樞或 Operations Management Suite](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>什麼被記錄？

您可以選取**引擎**，**服務**，和**度量**類別。

### <a name="engine"></a>引擎

選取**引擎**記錄所有[xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events)。 您無法選取個別事件。 

|XEvent 類別 |事件名稱  |
|---------|---------|
|安全性稽核    |   稽核登入      |
|安全性稽核    |   稽核登出      |
|安全性稽核    |   Audit Server Starts And Stops      |
|進度報表     |   進度報表開始      |
|進度報表     |   進度報表結束      |
|進度報表     |   進度報表目前事件      |
|查詢     |  查詢開始       |
|查詢     |   查詢結束      |
|命令     |  命令開始       |
|命令     |  命令結束       |
|錯誤和警告     |   Error      |
|探索     |   探索結束      |
|通知     |    通知     |
|工作階段     |  工作階段初始化       |
|鎖定    |  死結       |
|查詢處理     |   VertiPaq SE 查詢開始      |
|查詢處理     |   VertiPaq SE 查詢結束      |
|查詢處理     |   VertiPaq SE 查詢快取比對      |
|查詢處理     |   直接查詢開始      |
|查詢處理     |  直接查詢結束       |

### <a name="service"></a>服務

|作業名稱  |發生時  |
|---------|---------|
|CreateGateway     |   使用者在伺服器上設定閘道      |
|ResumeServer     |    繼續伺服器     |
|SuspendServer    |   暫停伺服器      |
|DeleteServer     |    刪除伺服器     |
|RestartServer    |     使用者重新啟動伺服器，以透過 SSMS 或 PowerShell    |
|GetServerLogFiles    |    使用者匯出透過 PowerShell 的伺服器記錄檔     |
|ExportModel     |   使用者入口網站中的模型會使用匯出 Visual Studio 中開啟     |

### <a name="all-metrics"></a>所有計量

度量類別記錄相同[伺服器度量](analysis-services-monitor.md#server-metrics)顯示的度量。

## <a name="setup-diagnostics-logging"></a>設定診斷記錄

### <a name="azure-portal"></a>Azure 入口網站

1. 在[Azure 入口網站](https://portal.azure.com)> 伺服器上，按一下 **診斷記錄**在左方的導覽中，然後按一下**開啟診斷**。

    ![在 Azure 入口網站中開啟 Azure Cosmos DB 的診斷記錄](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. 在**診斷設定**，執行下列動作： 

    * **名稱**。 輸入要建立之記錄的名稱。

    * **封存至儲存體帳戶**。 若要使用此選項，您需要可以連接的現有儲存體帳戶。 請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md)。 請依照下列指示來建立資源管理員]、 [一般用途帳戶。 然後返回入口網站的此頁面選取您的儲存體帳戶。 新建立的儲存體帳戶可能在數分鐘後才會出現在下拉式功能表中。
    * **串流處理至事件中樞**。 若要使用此選項，您需要可以連接的現有事件中樞命名空間和事件中樞。 若要建立事件中樞命名空間，請參閱[使用 Azure 入口網站建立事件中樞命名空間和事件中樞](../event-hubs/event-hubs-create.md)。 然後返回入口網站的此頁面選取事件中樞命名空間和原則名稱。
    * **傳送至 Log Analytics**。 若要使用此選項，請使用現有的工作區，或是在入口網站中依照[建立新的工作區](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)的步驟建立新的 Log Analytics 工作區。 如需有關如何檢視 Log Analytics 記錄的詳細資訊，請參閱[檢視 Log Analytics 中的記錄](#view-in-loganalytics)。

    * **引擎**。 選取此選項可記錄 xEvents。 如果您要封存至儲存體帳戶，您可以選取的診斷記錄的保留期限。 保留期限過後，就會自動刪除記錄。
    * **服務**。 選取此選項，將服務層級的事件記錄。 如果您要封存至儲存體帳戶，可以為診斷記錄選取保留期限。 保留期限過後，就會自動刪除記錄。
    * **度量**。 選取此選項，詳細資料儲存在[度量](analysis-services-monitor.md#server-metrics)。 如果您要封存至儲存體帳戶，可以為診斷記錄選取保留期限。 保留期限過後，就會自動刪除記錄。

3. 按一下 [檔案] 。

    如果您收到錯誤，指出「無法更新 \<工作區名稱> 的診斷。 訂用帳戶 \<訂用帳戶識別碼> 未註冊為使用 microsoft.insights」， 請遵循[針對 Azure 診斷進行疑難排解](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)的指示註冊帳戶，然後重試此程序。

    如果您想要變更您的診斷記錄檔儲存的方式在任何時間點以後，您可以回到此頁面來修改設定。

### <a name="powershell"></a>PowerShell

以下是將引導您基本的命令。 如果您想使用 PowerShell 來設定記錄到儲存體帳戶的逐步說明，請參閱本文稍後的教學課程。

若要使用 Powershell 啟用計量和診斷記錄功能，請使用下列 Cmdlet：

- 若要啟用儲存體帳戶中診斷記錄的儲存體，請使用下列命令：

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   儲存體帳戶識別碼是您要傳送記錄之目標儲存體帳戶的資源識別碼。

- 若要將診斷記錄串流至事件中樞，請使用下列命令：

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure 服務匯流排規則識別碼是此格式的字串︰

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 若要將診斷記錄傳送到 Log Analytics 工作區，請使用下列命令：

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 您可以使用下列命令取得 Log Analytics 工作區的資源識別碼：

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

您可以結合這些參數讓多個輸出選項。

### <a name="rest-api"></a>REST API

深入了解如何[使用 Azure 監視 REST API 來變更診斷設定](https://msdn.microsoft.com/library/azure/dn931931.aspx)。 

### <a name="resource-manager-template"></a>Resource Manager 範本

深入了解如何[啟用診斷設定，在建立資源時使用的資源管理員範本](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)。 

## <a name="manage-your-logs"></a>管理您的記錄檔

記錄檔可用通常設定記錄的幾個小時內。 儲存體帳戶中的記錄由您全權管理：

* 請使用標準的 Azure 存取控制方法限制可存取記錄的人員，藉此來保護記錄。
* 刪除不想繼續保留在儲存體帳戶中的記錄。
* 請務必設定保留期限，以便從儲存體帳戶刪除舊的記錄檔。

## <a name="view-logs-in-log-analytics"></a>檢視 Log Analytics 中的記錄

度量和伺服器事件會與 xEvents 記錄分析中的並行分析整合。 記錄分析也可以設定在您的架構提供的診斷記錄資料的整體檢視其他 Azure 服務從接收事件。

若要檢視記錄分析診斷資料，如下所示，從左的窗格中或管理區域中，開啟 [記錄搜尋] 頁面。

![Azure 入口網站中的記錄搜尋選項](./media/analysis-services-logging/aas-logging-open-log-search.png)

既然您已啟用資料收集中**記錄搜尋**，按一下 **所有收集的資料**。

在**類型**，按一下  **AzureDiagnostics**，然後按一下 **套用**。 AzureDiagnostics 包括引擎和服務的事件。 請注意上快速建立記錄分析查詢。 EventClass\_的欄位包含 xEvent 名稱，可能看起來很熟悉，如果您曾經使用 xEvents 內部部署記錄。

按一下**EventClass\_s**或其中一個事件名稱和記錄分析會繼續建構查詢。 請務必儲存您的查詢，可供日後重複使用。

請務必要簽出 Operations Management Suite 網站提供強化的查詢、 發送，且記錄分析資料警示功能。

### <a name="queries"></a>查詢

有數百個您可以使用的查詢。 以下是一些可協助您開始。
若要了解使用新的記錄搜尋查詢語言的詳細資訊，請參閱[了解記錄中記錄分析會搜尋](../log-analytics/log-analytics-log-search-new.md)。 

* 查詢傳回查詢提交到 Azure Analysis Services 完成所花費超過五分鐘 （300,000 毫秒為單位）。

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* 識別向外延展複本。

    ```
    search * | summarize count() by ServerName_s
    ```
    當使用向外延展，您就可以識別唯讀複本，因為 ServerName\_的欄位值必須附加至名稱的複本執行個體數目。 資源欄位包含 Azure 資源的名稱，以符合使用者看到的伺服器名稱。 IsQueryScaleoutReadonlyInstance_s 欄位等於 true 的複本。



> [!TIP]
> 有很棒的記錄分析查詢，您想要共用嗎？ 如果您有 GitHub 帳戶時，您可以將它加入此文件。 只要按一下**編輯**在右上方的此頁面。


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>教學課程-關閉記錄功能，使用 PowerShell
本快速教學課程中，您可以建立的相同訂用帳戶和資源群組的儲存體帳戶作為 Analysis Service 伺服器。 然後，您會使用 Set AzureRmDiagnosticSetting 開啟診斷記錄、 將輸出傳送至新的儲存體帳戶。

### <a name="prerequisites"></a>必要條件
若要完成本教學課程，您必須具備下列資源：

* 現有 Azure Analysis Services 伺服器。 如需建立伺服器資源的指示，請參閱[Azure 入口網站中建立伺服器](analysis-services-create-server.md)，或[使用 PowerShell 建立 Azure Analysis Services 伺服器](analysis-services-create-powershell.md)。

### <a name="aconnect-to-your-subscriptions"></a></a>連接到您的訂閱

開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：  

```powershell
Login-AzureRmAccount
```

在快顯瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱與密碼。 Azure PowerShell 會取得與此帳戶相關聯的所有訂用帳戶，並依預設使用第一個訂用帳戶。

如果您有多個訂用帳戶，您可能必須指定用來建立 Azure 金鑰保存庫的那一個特定訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzureRmSubscription
```

然後，若要指定與所記錄的 Azure Analysis Services 帳戶相關聯的訂用帳戶，請輸入：

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 如果您有多個與您的帳戶相關聯的訂用帳戶，務必指定訂用帳戶。
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>建立新的儲存體帳戶記錄

前提是它與您的伺服器相同的訂用帳戶中，您可以為您的記錄檔中，使用現有的儲存體帳戶。 此教學課程中您建立新的儲存體帳戶專用的 Analysis Services 記錄檔。 若要可讓您輕鬆，正在將儲存體帳戶詳細資料儲存在名為的變數**sa**。

您也可以使用相同的資源群組與包含 Analysis Services 伺服器。 替代值`awsales_resgroup`， `awsaleslogs`，和`West Central US`以您自己的值：

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>識別記錄的伺服器帳戶

將帳戶名稱設定為變數，名為**帳戶**ResourceName 所在的帳戶名稱。

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>啟用記錄

若要啟用記錄，使用組 AzureRmDiagnosticSetting cmdlet 變數以及新的儲存體帳戶、 伺服器帳戶，和類別目錄。 執行下列命令，將 **-Enabled** 旗標設為 **$true**：

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

輸出應該會看起來像這樣：

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

這可確認目前的伺服器，將資訊儲存到儲存體帳戶啟用記錄。

您也可以設定記錄保留原則，因此會自動刪除較舊的記錄檔。 例如，將設定保留原則使用**-RetentionEnabled**旗標設為**$true**，並設定**-RetentionInDays**參數**90**。 會自動刪除超過 90 天的記錄檔。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>後續步驟

深入了解[Azure 資源的診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)。

請參閱[組 AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) PowerShell 說明 中。