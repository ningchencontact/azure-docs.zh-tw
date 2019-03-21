---
title: 管理 Azure Log Analytics 的使用量和成本 | Microsoft Docs
description: 了解如何在 Azure 中變更 Log Analytics 工作區的定價方案，以及管理資料量和保留原則。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 5a8bd836322ae005b426707e0994bfdc19701fd8
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295669"
---
# <a name="manage-usage-and-costs-for-log-analytics"></a>管理 Log Analytics 的使用量和成本

> [!NOTE]
> 本文說明如何藉由設定資料保留期間來控制 Log Analytics 的成本。  請參閱下列文章以了解相關資訊。
> - [分析 Log Analytics 中的資料使用量](manage-cost-storage.md)說明如何分析資料使用量及提出警示。
> - [監視使用量和估計成本](usage-estimated-costs.md)說明如何針對不同的定價模型，檢視多項 Azure 監視功能的使用量和估計成本。 其中也會說明如何變更定價模型。

Log Analytics 是為縮放及支援每日大量資料的收集、索引編製和儲存而設計，這些資料來源可能位於您企業內部或部署於 Azure 中。  雖然這可能是您組織的主要推動力，但成本效率終究是最基本的推動力。 為此，務必了解 Log Analytics 工作區的成本不只是以收集的資料量為基礎，也取決於選取的方案，以及您為連線來源所產生的資料選擇了多長的儲存時間。  

在本文中，我們會探討您可以如何積極地監視資料量和儲存體成長狀況，並定義限制來控制這些相關成本。 

資料成本是否會變得可觀取決於下列因素： 

- 所產生的資料量和內嵌至工作區的資料量 
    - 已啟用的管理解決方案數量
    - 受監視的系統數量
    - 從每個受監視資源收集的資料類型 
- 您決定要保留資料的時間長度 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>了解工作區的使用量和估計成本
Log Analytics 可讓您根據最近的使用模式來輕鬆地了解可能的成本。  若要執行此動作，請使用 Log Analytics [使用量和估計成本] 來檢閱及分析資料使用量。 這能顯示每個解決方案所收集的資料量、所保留的資料量，以及根據所擷取的資料量及超出所包含量之任何額外保留資料量所進行的成本評估。

![使用量和估計成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更詳細地探索您的資料，請在 [使用量和估計成本] 頁面上，按一下位於任一圖表右上方的圖示。 現在您可以使用此查詢來探索更詳細的使用量。  

![[Logs] 檢視](media/manage-cost-storage/logs.png)

您可以從 [使用量和估計成本] 頁面檢閱當月的資料量。 這之中包括 Log Analytics 工作區中接收和保留的所有資料。  按一下頁面頂端的 [使用量詳細資料]可檢視使用量儀表板，其中的資訊包含以來源、電腦及供應項目為依據的資料量趨勢。 若要檢視和設定每日上限或修改保留期限，請按一下 [資料量管理]。
 
Log Analytics 費用會新增到您的 Azure 帳單中。 您可以在 Azure 入口網站中的 [帳務] 區段下，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。  

## <a name="daily-cap"></a>每日上限
您可以為工作區設定每日上限和限制每日擷取，但請謹慎使用，因為您的目標是不要達到每日限制。  否則，您會遺失當天其餘時間的資料，這可能會影響其功能取決於工作區中一直在提供的最新資料的其他 Azure 服務和解決方案。  如此一來，您在資源健全狀況支援 IT 服務時觀察和接收警示的功能會受到影響。  每日上限旨在用來管理受控資源中未預期的資料量增加，以及保持在您的限制範圍內，或是純粹用於限制意外產生的工作區費用。  

若達到每日限制，將停止收集當天剩餘時間的需計費資料類型。 在所選取的 Log Analytics 工作區中，頁面頂端會出現警告橫幅，且系統會將作業事件傳送至 **LogManagement** 類別下的「Operation」 資料表。 一旦過了「每日限制的設定時間」下定義的重設時間後，資料收集就會繼續執行。 我們建議您根據此作業事件定義警示規則，設定為在達到每日資料限制時發出通知。 

### <a name="identify-what-daily-data-limit-to-define"></a>識別要定義的每日資料限制 
檢閱 [Log Analytics 使用量和估計成本](usage-estimated-costs.md)，以了解資料擷取趨勢及要定義的每日資料量上限。 此謹慎考量，因為達到限制之後，您將無法監視您的資源。 

### <a name="manage-the-maximum-daily-data-volume"></a>管理每日資料量上限 
下列步驟說明如何設定限制，以管理 Log Analytics 每日擷取的資料量。  

1. 在工作區中，從左側窗格中選取 [使用量和估計成本]。
2. 在所選工作區的 [使用量和估計成本] 頁面上，按一下頁面頂端的 [資料量管理]。 
3. 每日上限預設為 [關閉] – 請按一下 [開啟] 來加以啟用，然後設定資料量限制 (GB/天)。<br><br> ![Log Analytics 設定資料限制](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>已達每日上限時發出警示
雖然我們會在您達到資料限制閾值時，於 Azure 入口網站中顯示視覺提示，但對於需要立刻處理的作業問題，此行為並不一定與您的管理方式一致。  若要接收警示通知，您可以在 Azure 監視器中建立新的警示規則。  若要進一步了解，請參閱[如何建立、檢視及管理警示](alerts-metric.md)。      

為協助您開始使用，以下是警示的建議設定：

* 目標：選取您的 Log Analytics 資源
* 準則： 
   * 訊號名稱：自訂記錄搜尋
   * 搜尋查詢：Operation | where Detail has 'OverQuota'
   * 依據：結果數目
   * 條件：大於
   * 閾值：0
   * 期間：5 (分鐘)
   * 頻率：5 (分鐘)
* 警示規則名稱：已達到每日資料限制
* 嚴重性：警告 (嚴重性 1)

一旦定義警示且達到限制後，警示就會觸發，並執行動作群組中定義的回應。 其可以透過電子郵件和文字簡訊通知您的小組，或使用 Webhook、自動化 Runbook 或[與外部 ITSM 方案整合](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)來自動採取動作。 

## <a name="change-the-data-retention-period"></a>變更資料保留期 
下列步驟說明如何設定您工作區中的記錄資料保留時間。
 
1. 在工作區中，從左側窗格中選取 [使用量和估計成本]。
2. 在 [使用量和估計成本] 頁面上，按一下頁面頂端的 [資料量管理]。
5. 在窗格上，移動滑桿來增加或減少天數，然後按一下 [確定]。  如果您位於「免費」層，將無法修改資料保留期，必須升級至付費層，才能控制此設定。<br><br> ![變更工作區資料保留期設定](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>舊版定價層

在 2018 年 7 月 1 日前簽署 Enterprise 合約的客戶，或已在訂用帳戶中建立 Log Analytics 工作區的客戶，仍然可以存取「免費」方案。 如果訂用帳戶未繫結至現有的 EA 註冊，當您在 2018 年 4 月 2 日之後於新的訂用帳戶中建立工作區時，不適用「免費」層。  「免費」層的資料保留期僅限 7 天。  針對舊版「獨立」或「每個節點」層以及目前的 2018 年單一定價層，都會提供過去 31 天所收集的資料。 「免費」層有每日 500 MB 的擷取限制，如果您發現一直超出所允許的數量，您可以將工作區變更為另一個方案來收集超出此限制的資料。 

> [!NOTE]
> 若要使用來自購買 OMS E1套件、OMS E2 套件或 OMS Add-On for System Center 的權利，請選擇 Log Analytics [每個節點] 定價層。

## <a name="changing-pricing-tier"></a>正在變更定價層

如果您的 Log Analytics 工作區可以存取舊版的定價層，則可以在舊版的定價層之間進行變更：

1. 在 Azure 入口網站的 [Log Analytics 訂用帳戶] 窗格中，選取工作區。

2. 在 [工作區] 窗格的 [一般] 下方，選取 [定價層]。  

3. 在 [定價層] 下方選取定價層，然後按一下 [選取]。  
    ![選取的定價方案](media/manage-cost-storage/workspace-pricing-tier-info.png)

如果您想要將工作區移至目前的定價層，則需要[在 Azure 監視器中變更訂用帳戶的監視定價模型](usage-estimated-costs.md#moving-to-the-new-pricing-model)，這將變更該訂用帳戶中所有工作區的定價層。

> [!NOTE]
> 如果您的工作區連結到自動化帳戶，必須先刪除任何**自動化和控制**解決方案以及取消連結自動化帳戶，才可以選取 [獨立 (每 GB)] 定價層。 在 [工作區] 刀鋒視窗的 [一般] 之下，按一下 [解決方案] 以查看和刪除解決方案。 若要取消連結自動化帳戶，請按一下 [定價層] 刀鋒視窗上的自動化帳戶名稱。

> [!NOTE]
> 您可以深入了解[設定定價層透過 ARM](template-workspace-configuration.md#create-a-log-analytics-workspace)以及如何確保您的 ARM 部署將會成功不論是否在訂用帳戶在傳統或新的定價模型。 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>針對 Log Analytics 為什麼不再收集資料的問題進行疑難排解
如果您處於舊版「免費」定價層並在某日傳送了超過 500 MB 的資料，就會停止收集當日的其餘資料。 達到每日限制是 Log Analytics 停止收集資料或資料似乎遺失的常見原因。  當資料收集開始及停止時，Log Analytics 會建立 Operation 類型的事件。 在搜尋中執行下列查詢，即可檢查您是否達到每日限制並遺失資料： 

`Operation | where OperationCategory == 'Data Collection Status' `

當資料收集停止時，OperationStatus 為 Warning。 當資料收集開始時，OperationStatus 為 Succeeded。 下表描述資料收集停止的原因，並建議為繼續資料收集所要採取的動作：  

|收集停止的原因| 解決方法| 
|-----------------------|---------|
|已達舊版免費定價層的每日限制 |請等到隔天自動重新開始收集，或變更為付費定價層。|
|已達您工作區的每日上限|等到自動重新開始收集或提高每日資料量限制，如「管理每日資料量上限」中所述。 每日上限重設時間會顯示於 [資料量管理] 頁面上。 |
|Azure 訂用帳戶處於暫停狀態，原因如下：<br> 免費試用已結束<br> Azure Pass 已過期<br> 已達每月消費限制 (例如 MSDN 或 Visual Studio 訂閱)|轉換成付費訂閱<br> 移除限制，或等到限制重設|

若要在資料收集停止時收到通知，請使用「建立每日資料上限」警示中所述的步驟，並使用「將動作新增至警示規則」中所述的步驟，來設定警示規則的電子郵件、Webhook 或 Runbook 動作。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>針對使用量高於預期的原因進行疑難排解
較高的使用量是由下列一個或兩個原因所造成：
- 比預期更多的節點將資料傳送到 Log Analytics
- 傳送到 Log Analytics 的資料比預期更多

下一步 的區段總管

## <a name="understanding-nodes-sending-data"></a>了解節點傳送資料

若要了解在上個月每一天回報資料的電腦 (節點) 數目，請使用

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart`

若要取得傳送**計費資料類型**的電腦清單 (某些資料類型是免費的)，請使用 [_IsBillable](log-standard-properties.md#_isbillable) 屬性：

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName`

請謹慎使用這些 `union withsource = tt *` 查詢，因為執行跨資料類型掃描的費用相當高昂。 此查詢會取代舊有的查詢使用資料類型的每個電腦資訊的方式。  

這可以延伸到傳回的每小時傳送的電腦計數計費資料類型 （這是 Log Analytics 如何計算可計費的節點舊版每節點定價層）：

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc`

## <a name="understanding-ingested-data-volume"></a>了解內嵌的資料量 

在 [使用量和估計成本] 頁面上，[每個解決方案的資料擷取] 圖表會顯示所傳送的資料總量，以及每個解決方案所傳送的資料量。 這可讓您判斷各種趨勢，例如整體資料使用量 (或特定解決方案的使用量) 是否正在增長、穩定不變或正在減少。 用來產生此內容的查詢為：

`Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

請注意，子句 "where IsBillable = true" 會篩選掉來自無擷取成本之特定解決方案的資料類型。 

您可以進一步向下鑽研，以查看特定資料類型的資料趨勢，例如假設您想研究基於 IIS 記錄的資料：

`Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="data-volume-by-computer"></a>資料量 (依電腦)

若要查看**大小**的可計費的事件，擷取每一部電腦，使用`_BilledSize`屬性 ([記錄檔-標準-屬性 #_billedsize.md](learn more)) 提供以位元組為單位的大小：

`union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last `

`_IsBillable`屬性會指定是否將內嵌的資料將會產生費用 ([記錄檔-標準-properties.md #_isbillable](Learn more)。)

若要查看每部電腦所擷取的事件**計數**，請使用：

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

若要查看每部電腦所擷取之可計費事件的計數，請使用： 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

如果您想要查看有哪些可計費資料類型的計數正在傳送資料到特定的電腦，請使用：

`union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure 資源、 資源群組或訂用帳戶的資料量

對於裝載在 Azure 中的節點中的資料，您可以取得**大小**內嵌的可計費事件的__每部電腦__，使用`_ResourceId`屬性可提供資源的完整路徑 ([記錄檔-標準-properties.md #_resourceid](learn more)):

`union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last `

對於裝載在 Azure 中的節點中的資料，您可以取得**大小**的可計費的事件內嵌__每個 Azure 訂用帳戶__，剖析`_ResourceId`屬性設為：

`union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last `

變更`subscriptionId`至`resourceGroup`會顯示依 Azure resouurce 群組的可計費的內嵌的資料磁碟區。 


> [!NOTE]
> [使用量] 資料類型的部分欄位雖然仍位於結構描述中，但皆已過時，且系統將不再填入其值。 這包括 [Computer]，以及其他與擷取相關的欄位 ([TotalBatches]、[BatchesWithinSla]、[BatchesOutsideSla]、[BatchesCapped]，以及 [AverageProcessingTimeMs])。

### <a name="querying-for-common-data-types"></a>常見的資料類型的查詢

若要更深入挖掘特定資料類型的資料來源，以下是一些實用的範例查詢：

+ **安全性**解決方案
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **記錄管理**解決方案
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** 資料類型
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event** 資料類型
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** 資料類型
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** 資料類型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>降低成本的訣竅

降低所收集記錄量的一些建議包括：

| 高資料量的來源 | 如何縮減資料量 |
| -------------------------- | ------------------------- |
| 安全性事件            | 選取[一般或最小安全性事件](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> 變更安全性稽核原則為只收集所需事件。 特別檢閱下列原則是否需要收集事件： <br> - [a稽核篩選平台](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [稽核登錄](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [稽核檔案系統](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [稽核核心物件](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [稽核控制代碼操作](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 稽核抽取式存放裝置 |
| 效能計數器       | 變更[效能計數器組態](data-sources-performance-counters.md)以： <br> - 減少收集頻率 <br> - 減少效能計數器的數目 |
| 事件記錄檔                 | 變更[事件記錄組態](data-sources-windows-events.md)以： <br> - 減少所收集的事件記錄數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」層級事件 |
| syslog                     | 變更 [Syslog 組態](data-sources-syslog.md)以： <br> - 減少所收集的設施數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」和「偵錯」層級事件 |
| AzureDiagnostics           | 變更資源記錄集合： <br> - 減少會將記錄傳送至 Log Analytics 的資源數目 <br> - 只收集必要的記錄 |
| 電腦中不需要解決方案的方案資料 | 使用[方案目標](../insights/solution-targeting.md)，只從必要的電腦群組收集資料。 |

### <a name="getting-security-and-automation-node-counts"></a>取得安全性和自動化的節點計數 

如果您是處於「每節點 (OMS)」定價層，則系統會根據您使用的節點和解決方案數目來向您收取費用，您需支付費用的見解與分析節點數目將會顯示在 [使用量和估計成本] 頁面上的資料表中。  

若要查看不同安全性節點的數目，您可以使用此查詢：

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

若要查看不同自動化節點的數目，請使用此查詢：

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>當資料收集高於預期時建立警示

本節說明在下列情況下如何建立警示：
- 資料量超出指定的數量。
- 資料量預計會超出指定的數量。

Azure 警示支援使用搜尋查詢的[記錄警示](alerts-unified-log.md)。 

在過去 24 小時收集超過 100GB 的資料時，下列查詢的結果：

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

下列查詢會使用簡單的公式來預測在一天中何時會傳送超過 100GB 的資料： 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

若要針對不同的資料量顯示警示，請將查詢中的 100 變更為您要顯示警示的 GB 數。

使用[建立新記錄警示](alerts-metric.md)中所述的步驟，可在資料收集高於預期時收到通知。

建立第一個查詢的警示時 - 在 24 小時內有超過 100GB 的資料時，請：  

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **訊號名稱**：選取 [自訂記錄搜尋]
   - 將 [搜尋查詢] 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - [警示邏輯] 為 [根據結果數目]，而 [條件] 為 [大於臨界值 0]
   - [時間週期] 為 1440 分鐘，而 [警示頻率] 設定為 60 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱] 設定為「在 24 小時內大於 100GB 的資料量」
   - 將 [嚴重性] 設定為「警告」

指定現有或建立新的[動作群組](action-groups.md)，以便在記錄警示符合準則時收到通知。

建立第二個查詢的警示時 - 預計在 24 小時內會有超過 100GB 的資料時，請：

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **訊號名稱**：選取 [自訂記錄搜尋]
   - 將 [搜尋查詢] 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - [警示邏輯] 為 [根據結果數目]，而 [條件] 為 [大於臨界值 0]
   - [時間週期] 為 180 分鐘，而 [警示頻率] 設定為 60 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱] 設定為「預計在 24 小時內大於 100GB 的資料量」
   - 將 [嚴重性] 設定為「警告」

指定現有或建立新的[動作群組](action-groups.md)，以便在記錄警示符合準則時收到通知。

當您收到警示時，請使用下一節中的步驟，針對使用量高於預期的原因進行疑難排解。

## <a name="next-steps"></a>後續步驟
* 請參閱 [Log Analytics 中的記錄搜尋](../log-query/log-query-overview.md)，以了解如何使用搜尋語言。 您可以使用搜尋查詢，對使用量資料執行額外的分析。
* 使用[建立新的記錄警示](alerts-metric.md)中所述的步驟，可在符合搜尋條件時收到通知。
* 使用[方案目標](../insights/solution-targeting.md)，只從必要的電腦群組收集資料。
* 若要設定有效的事件收集原則，請檢閱 [Azure 資訊安全中心篩選原則](../../security-center/security-center-enable-data-collection.md)。
* 變更[效能計數器組態](data-sources-performance-counters.md)。
* 若要修改事件收集設定，請檢閱[事件記錄組態](data-sources-windows-events.md)。
* 若要修改 syslog 收集設定，請檢閱 [Syslog 組態](data-sources-syslog.md)。


