---
title: 如何從適用於 VM 的 Azure 監視器 (預覽) 查詢記錄 | Microsoft Docs
description: Azure 監視器 Vm 解決方案所收集的計量和記錄資料和這篇文章描述的記錄，並包含範例查詢。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: 8b6745a2b9afe8d3101585e3f7a13f2fc978c84a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492083"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>如何從適用於 VM 的 Azure 監視器 (預覽) 查詢記錄
適用於 Vm 的 azure 監視器會收集效能和連線計量、 電腦和處理程序清查資料，以及健全狀況狀態資訊，並將它轉送至 Log Analytics 工作區，在 Azure 監視器中。  這項資料可供[查詢](../../azure-monitor/log-query/log-query-overview.md)Azure 監視器中。 您可以將此資料套用至各種案例，包括移轉規劃、容量分析、探索和隨選效能疑難排解。

## <a name="map-records"></a>對應記錄
除了當處理序或電腦啟動或是上線到適用於 VM 的 Azure 監視器對應功能時所產生的記錄外，每小時還會為每個唯一的電腦和處理序產生一筆記錄。 這些記錄具有下表中的屬性。 ServiceMapComputer_CL 事件中的欄位和值對應到 ServiceMap Azure Resource Manager API 中的機器資源欄位。 ServiceMapProcess_CL 事件中的欄位和值對應到 ServiceMap Azure Resource Manager API 中的處理序資源欄位。 ResourceName_s 欄位會符合對應 Resource Manager 資源中的名稱欄位。 

有可用來識別唯一處理程序和電腦的內部產生屬性︰

- 電腦：使用 *ResourceId* 或 *ResourceName_s* 來唯一識別 Log Analytics 工作區中的電腦。
- 處理序：使用 *ResourceId* 來唯一識別 Log Analytics 工作區中的處理序。 *ResourceName_s* 在執行處理序的機器 (MachineResourceName_s) 環境中是唯一的 

因為在指定時間範圍內可以有多筆指定處理序和電腦的記錄，針對相同電腦或處理序的查詢可能會傳回多筆記錄。 若只要包含最新的記錄，請在查詢中加入 "| dedup ResourceId"。

### <a name="connections-and-ports"></a>連線和連接埠
連線計量功能導入了兩個新的資料表，在 Azure 監視器記錄檔-VMConnection 和 VMBoundPort。 這些表格會提供連接埠的開啟/主動在其上的連線 （輸入及輸出） 的機器，以及伺服器的相關資訊。 ConnectionMetrics 也會公開透過提供方法來取得特定的度量時間範圍期間的 Api。 所產生的 TCP 連線*接受*接聽的通訊端的輸入時所建立的*連線*至指定的 IP 和連接埠會輸出。 連線的方向會透過 Direction 屬性來表示，此屬性可設為 **inbound** 或 **outbound**。 

從相依性代理程式所報告的資料產生這些資料表中的記錄。 每一筆記錄表示 1 分鐘的時間間隔內觀察值。 TimeGenerated 屬性表示時間間隔的開始時間。 每筆記錄均包含資訊來識別個別的實體 (也就是連線或連接埠)，以及與該實體相關聯的計量。 目前只會報告透過 IPv4 使用 TCP 而發生的網路活動。 

#### <a name="common-fields-and-conventions"></a>常見的欄位和慣例 
下列欄位和慣例適用於 VMConnection 和 VMBoundPort: 

- 電腦：報告電腦的完整網域名稱 
- AgentID:Log Analytics 代理程式的機器唯一識別碼  
- 電腦:機器由 ServiceMap Azure Resource Manager 資源的名稱。 它是表單*m-{GUID}*，其中*GUID*是 AgentID 為相同的 GUID  
- 處理序：程序由 ServiceMap Azure Resource Manager 資源名稱。 它是表單*p-{十六進位字串}*。 程序是唯一的電腦範圍內，若要跨電腦產生唯一的處理序識別碼，將機器和處理程序的欄位組合。 
- ProcessName︰報告的程序的可執行檔的名稱。
- 所有 IP 位址都的字串格式 IPv4 標準，例如*13.107.3.160* 

為了管理成本和複雜度，連線記錄不代表個別的實體網路連線。 將多個實體網路連線群組為一個邏輯連線，其接著會反映於各自的資料表中。  這表示，*VMConnection* 資料表中的記錄代表一個邏輯群組，而非觀測到的個別實體連線。 在指定的一分鐘時間間隔內，共用下列屬性相同值的實體網路連線會彙總為 *VMConnection* 中的單一邏輯記錄。 

| 屬性 | 描述 |
|:--|:--|
|方向 |連線的方向，值為 *inbound* 或 *outbound* |
|機器 |電腦 FQDN |
|Process |處理序或處理序群組的身分識別，會起始/接受連線 |
|SourceIp |來源的 IP 位址 |
|DestinationIp |目的地的 IP 位址 |
|DestinationPort |目的地的連接埠號碼 |
|通訊協定 |用於連線的通訊協定。  值為 *tcp*。 |

為了說明群組的影響，會在記錄的下列屬性中提供群組實體連線數目的相關資訊：

| 屬性 | 描述 |
|:--|:--|
|LinksEstablished |已在報告時間範圍內建立的實體網路連線數目 |
|LinksTerminated |已在報告時間範圍內終止的實體網路連線數目 |
|LinksFailed |在報告時間範圍內失敗的實體網路連線數目。 此資訊目前僅適用於輸出連線。 |
|LinksLive |已在報告時間範圍結束時開啟的實體網路連線數目|

#### <a name="metrics"></a>指标

除了連線計數計量，在指定邏輯連線或網路連接埠上傳送與接收的資料量相關資訊也會包含於記錄的下列屬性中：

| 屬性 | 描述 |
|:--|:--|
|BytesSent |已在報告時間範圍內傳送的位元組總數 |
|BytesReceived |已在報告時間範圍內接收的位元組總數 |
|回應 |已在報告時間範圍內觀測到的回應數目。 
|ResponseTimeMax |已在報告時間範圍內觀測到的最大回應時間 (毫秒)。 如果沒有值，則此屬性為空白。|
|ResponseTimeMin |已在報告時間範圍內觀測到的最小回應時間 (毫秒)。 如果沒有值，則此屬性為空白。|
|ResponseTimeSum |已在報告時間範圍內觀測到的所有回應時間總和 (毫秒)。 如果沒有值，則此屬性為空白。|

所報告的第三個資料類型是回應時間：呼叫端需要花費多久時間來等候透過連線傳送的要求，此要求會由遠端端點來處理及回應。 所報告的回應時間是基礎應用程式通訊協定的真正回應時間估計值。 它會使用啟發學習法，根據實體網路連線來源和目的端之間資料流程的觀測計算而來的。 概念上，它是要求的最後一個位元組離開傳送端的時間，以及回應的最後一個位元組傳回給它的到達時間之間的差異。 這兩個時間戳記可用來描述指定實體連線上的要求和回應事件。 它們之間的差異代表單一要求的回應時間。 

在此功能的第一個版本中，我們的演算法是一個近似值，根據針對指定網路連線所使用的實際應用程式通訊協定，成功的程度可能各有不同。 例如，目前的方法非常適用以要求-回應為基礎的通訊協定 (例如 HTTP (S))，但不適用單向或以訊息佇列為基礎的通訊協定。

此處有一些需要考慮的重要事項：

1. 如果處理序會在同一個 IP 位址但透過多個網路介面來接受連線，則將會報告每個介面的個別記錄。 
2. 使用萬用字元 IP 的記錄將不會包含任何活動。 它們會包含在其中，以代表會針對輸入流量開啟電腦上的連接埠。
3. 若要減少詳細資訊和資料量，如果有具特定 IP 位址的相符記錄 (適用於相同的處理序、連接埠和通訊協定)，將略過具有萬用字元 IP 的記錄。 略過萬用字元 IP 記錄時，具有特定 IP 位址的 IsWildcardBind 記錄屬性將設為 "True"，以代表連接埠會透過報告機器的每個介面來公開。
4. 繫結只在特定介面的連接埠已設為 IsWildcardBind *False*。

#### <a name="naming-and-classification"></a>命名和分類
為了方便起見，RemoteIp 屬性中會包含連線遠端的 IP 位址。 如果是輸入連線，RemoteIp 相當於 SourceIp，如果是連出連線，則它相當於 DestinationIp。 RemoteDnsCanonicalNames 屬性代表適用於 RemoteIp 的機器所報告的 DNS 標準名稱。 RemoteDnsQuestions 和 RemoteClassification 屬性均會保留，以供日後使用。 

#### <a name="geolocation"></a>地理位置
*VMConnection* 也會在記錄的下列屬性中，包含每個連線記錄遠端的地理位置資訊： 

| 屬性 | 描述 |
|:--|:--|
|RemoteCountry |裝載 RemoteIp 的國家/地區名稱。  例如，*United States* |
|RemoteLatitude |地理位置緯度。 例如，*47.68* |
|RemoteLongitude |地理位置經度。 例如：*-122.12* |

#### <a name="malicious-ip"></a>惡意 IP
*VMConnection* 資料表中的每個 RemoteIp 屬性均會根據一組具有已知惡意活動的 IP 進行檢查。 如果 RemoteIp 被識別為惡意的，將在記錄的下列屬性中填入下列屬性 (如果 IP 被視為不是惡意的，則它們是空的)：

| 屬性 | 描述 |
|:--|:--|
|MaliciousIP |RemoteIp 位址 |
|IndicatorThreadType |偵測到的威脅指標是下列值之一：*殭屍網路*、*C2*、*CryptoMining*、*Darknet*、*DDos*、*MaliciousUrl*、*惡意程式碼*、*網路釣魚*、*Proxy*、*PUA*、*關注清單*。   |
|描述 |觀察到的威脅的說明。 |
|TLPLevel |號誌燈通訊協定 (TLP) 層級是已定義的值 (*白色*、*綠色*、*琥珀色*、*紅色*) 之一。 |
|信賴度 |值為 *0 – 100*。 |
|严重性 |值為 *0 – 5*，其中 *5* 為最嚴重，*0* 為根本不嚴重。 預設值為 *3*。  |
|FirstReportedDateTime |提供者第一次回報指標。 |
|LastReportedDateTime |Interflow 最後一次看到指標。 |
|IsActive |使用 *True* 或 *False* 值表示指標停用。 |
|ReportReferenceLink |與指定的可預見值相關之報告的連結。 |
|AdditionalInformation |提供有關觀察到的威脅的其他資訊 (如果適用的話)。 |

### <a name="ports"></a>連接埠 
在電腦上，主動接受連入流量，或可能無法接受流量，但在報告的時間期間，會處於閒置狀態的連接埠會寫入 VMBoundPort 資料表。  

>[!NOTE]
>適用於 Vm 的 azure 監視器不支援收集和記錄的 Log Analytics 工作區，在以下區域內的連接埠資料：  
>- 美國東部  
>- 西歐
>
> 此資料收集已啟用另[支援的區域](vminsights-onboard.md#log-analytics)適用於 Vm 的 Azure 監視。 

VMBoundPort 中的每一筆記錄識別下列欄位： 

| 屬性 | 描述 |
|:--|:--|
|Process | 處理序 （或處理程序群組） 與連接埠是與相關聯的識別。|
|IP | 連接埠 IP 位址 (可以是萬用字元 IP *0.0.0.0*) |
|Port |連接埠號碼 |
|通訊協定 | 通訊協定。  範例中， *tcp*或是*udp* (只有*tcp*目前支援)。|
 
身分識別連接埠衍生自上述的五個欄位，並會儲存在 PortId 屬性。 這個屬性可用來跨時間快速尋找特定的連接埠的記錄。 

#### <a name="metrics"></a>指标 
連接埠記錄包含計量代表與其相關聯的連線。 下列計量目前報告 （每個度量的詳細資料會在上一節中所述）： 

- BytesSent 和 BytesReceived 
- LinksEstablished，LinksTerminated，LinksLive 
- ResposeTime，ResponseTimeMin，ResponseTimeMax ResponseTimeSum 

此處有一些需要考慮的重要事項：

- 如果處理序會在同一個 IP 位址但透過多個網路介面來接受連線，則將會報告每個介面的個別記錄。  
- 使用萬用字元 IP 的記錄將不會包含任何活動。 它們會包含在其中，以代表會針對輸入流量開啟電腦上的連接埠。 
- 若要減少詳細資訊和資料量，如果有具特定 IP 位址的相符記錄 (適用於相同的處理序、連接埠和通訊協定)，將略過具有萬用字元 IP 的記錄。 當省略萬用字元 IP 記錄時， *IsWildcardBind*具有特定的 IP 位址之記錄的屬性會設定為 *，則為 True*。  這表示報告的機器的每個介面透過公開的連接埠。 
- 繫結只在特定介面的連接埠已設為 IsWildcardBind *False*。 

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL 記錄
類型為 *ServiceMapComputer_CL* 的記錄會有伺服器 (具有 Dependency Agent) 的清查資料。 這些記錄具有下表中的屬性：

| 屬性 | 描述 |
|:--|:--|
| 類型 | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 工作區中機器的唯一識別碼 |
| ResourceName_s | 工作區中機器的唯一識別碼 |
| ComputerName_s | 電腦 FQDN |
| Ipv4Addresses_s | 伺服器的 IPv4 位址清單 |
| Ipv6Addresses_s | 伺服器的 IPv6 位址清單 |
| DnsNames_s | DNS 名稱的陣列 |
| OperatingSystemFamily_s | Windows 或 Linux |
| OperatingSystemFullName_s | 作業系統的完整名稱  |
| Bitness_s | 機器的運算位元數 (32 位元或 64 位元)  |
| PhysicalMemory_d | 實體記憶體 (MB) |
| Cpus_d | CPU 數目 |
| CpuSpeed_d | CPU 速度 (MHz)|
| VirtualizationState_s | *unknown**physical**virtual* *hypervisor* |
| VirtualMachineType_s | *hyperv*、*vmware* 等等 |
| VirtualMachineNativeMachineId_g | VM 識別碼 (由其 Hypervisor 指派) |
| VirtualMachineName_s | VM 的名稱 |
| BootTime_t | 開機時間 |

### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 類型記錄
類型為 *ServiceMapProcess_CL* 的記錄會有伺服器 (具有 Dependency Agent) 上 TCP 連線處理序的清查資料。 這些記錄具有下表中的屬性：

| 屬性 | 描述 |
|:--|:--|
| 類型 | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 工作區中處理序的唯一識別碼 |
| ResourceName_s | 在執行處理序的機器上，處理序的唯一識別碼|
| MachineResourceName_s | 機器的資源名稱 |
| ExecutableName_s | 處理序可執行檔的名稱 |
| StartTime_t | 處理序集區的開始時間 |
| FirstPid_d | 處理序集區中的第一個 PID |
| Description_s | 處理序的描述 |
| CompanyName_s | 公司的名稱 |
| InternalName_s | 內部名稱 |
| ProductName_s | 產品的名稱 |
| ProductVersion_s | 產品版本 |
| FileVersion_s | 檔案版本 |
| CommandLine_s | 命令列 |
| ExecutablePath _s | 可執行檔的路徑 |
| WorkingDirectory_s | 工作目錄 |
| UserName | 執行處理序的帳戶 |
| UserDomain | 執行處理序的網域 |

## <a name="sample-log-searches"></a>記錄搜尋範例

### <a name="list-all-known-machines"></a>列出所有已知的機器
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>VM 上次啟動時
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Azure VM 依映像、位置和 SKU 區分的摘要
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有受控電腦的實體記憶體容量。
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>列出電腦名稱、DNS、IP 和 OS。
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>在命令列中尋找具有「sql」的所有處理程序
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>以資源名稱尋找機器 (最新的記錄)
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>以 IP 位址尋找機器 (最新的記錄)
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>列出指定機器上的所有已知處理序
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>列出所有執行 SQL 伺服器的電腦
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>列出資料中心內所有唯一 curl 產品版本
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>為所有執行 CentOS 的電腦建立電腦群組
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>位元組傳送及接收趨勢
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>哪個 Azure VM 傳輸大部分的位元組
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>連結狀態趨勢
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>連線失敗趨勢
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
```

### <a name="bound-ports"></a>繫結連接埠
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>跨電腦的開啟連接埠數目
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>由他們擁有的連接埠數目開啟評分您的工作區中的程序
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>每個連接埠的彙總行為
這項查詢則可用來評分的連接埠活動，例如使用大部分的輸入/輸出流量的連接埠、 連接埠與大部分的連線
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>摘要說明來自機器群組的輸出連線
```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>後續步驟
* 如果您不熟悉 Azure 監視器中寫入記錄檔查詢，請檢閱[如何使用 Log Analytics](../../azure-monitor/log-query/get-started-portal.md)在 Azure 入口網站中要寫入記錄檔查詢。
* 了解如何[撰寫搜尋查詢](../../azure-monitor/log-query/search-queries.md)。
