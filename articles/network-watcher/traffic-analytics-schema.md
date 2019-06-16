---
title: Azure 流量分析的結構描述 |Microsoft Docs
description: 了解流量分析來分析 Azure 網路安全性群組流量記錄的結構描述。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: 491f19abfd87c28ede45e98a24f31fe7e599b18b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64691422"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>在 流量分析的結構描述和資料彙總

流量分析是一個雲端式解決方案，可顯示雲端網路中的使用者和應用程式活動。 「流量分析」會分析網路監看員網路安全性群組 (NSG) 流量記錄，讓您深入了解 Azure 雲端中的流量。 透過流量分析，您可以：

- 視覺化您的 Azure 訂用帳戶之間的網路活動，並識別作用點。
- 透過開啟的連接埠、嘗試存取網際網路的應用程式，和連線至 Rogue 網路的虛擬機器 (VM) 等資訊，識別網路的安全性威脅並保護您的網路。
- 了解各個 Azure 區域間和網際網路的流量模式，使網路部署達到最理想的效能和容量。
- 找出導致網路連線失敗的網路設定錯誤。
- 了解位元組、 封包或流程中的網路使用量。

### <a name="data-aggregation"></a>資料彙總

1. 在 NSG"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t 」 之間的所有流量記錄會每隔一分鐘的儲存體帳戶中，為 blob 都擷取之前正在處理的流量分析。 
2. 預設流量分析的處理間隔時間為 60 分鐘。 這表示，流量分析會挑選彙總的儲存體的 blob 每隔 60 分鐘。
3. 流程具有相同來源 IP、 目的地 IP、 目的地連接埠、 NSG 名稱、 NSG 規則、 流程方向和傳輸層通訊協定 （TCP 或 UDP） (附註：來源連接埠排除於彙總） 會組合在一起成單一流程流量分析
4. 此單一記錄且裝飾 （詳細資料在下一節中的） 中內嵌的記錄分析流量 Analytics.This 程序最多可能需要 1 小時內最大值。
5. FlowStartTime_t 欄位會指出這類彙總流程 (相同四個 tuple) 第一個處理間隔 」 FlowIntervalStartTime_t"和"FlowIntervalEndTime_t 」 流程記錄檔中。 
6. Ta 及緊接在中的任何資源，UI 所示，流程是總流量看到的 NSG，但在 Log Analytics 使用者會看到只有單一、 縮減記錄。 若要查看所有流程，使用 blob_id 欄位中，您可以從儲存體參考。 流量總計算的記錄會比對出現在 blob 中的個別流程。


### <a name="fields-used-in-traffic-analytics-schema"></a>流量分析的結構描述中使用的欄位

流量分析為建置基礎 Log Analytics 中，因此您可以裝飾流量分析和警示設定位於相同的資料執行自訂查詢。

以下列出中的結構描述，以及它們所表示的欄位

| 欄位 | 格式 | 註解 | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 流量 Analytics 資料的資料表
| SubType_s | FlowLog | 流量記錄的子類型 |
| FASchemaVersion_s |   1   | Scehma 版本。 不會反映 NSG 流程記錄版本 |
| TimeProcessed_t   | 日期和時間-utc 時區  | 流量分析處理未經處理的流量記錄，從儲存體帳戶的時間 |
| FlowIntervalStartTime_t | 日期和時間-utc 時區 |  流程記錄檔處理間隔的開始時間。 這是從中流程間隔為單位的時間 |
| FlowIntervalEndTime_t | 日期和時間-utc 時區 | 結束時間的流程記錄檔處理間隔 |
| FlowStartTime_t | 日期和時間-utc 時區 |  第一次出現 （這會取得彙總） 的流程的流程記錄檔處理之間的間隔 」 FlowIntervalStartTime_t"和"FlowIntervalEndTime_t 」 中。 此流程取得彙總根據彙總的邏輯 |
| FlowEndTime_t | 日期和時間-utc 時區 | 最後一次出現 （這會取得彙總） 的流程的流程記錄檔處理之間的間隔 」 FlowIntervalStartTime_t"和"FlowIntervalEndTime_t 」 中。 流程記錄 v2，依據此欄位會包含具有相同的四個 tuple 的最後一個流程啟動時 （標示為 「 B 」 中的未經處理的流程記錄） 的時間 |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * 未知私用 <br> * 未知 | 下表的資訊中的定義 |
| SrcIP_s | 來源 IP 位址 | 將會是空白時 AzurePublic 和 ExternalPublic 流程 |
| DestIP_s | 目的地 IP 位址 | 將會是空白時 AzurePublic 和 ExternalPublic 流程 |
| VMIP_s | 之 VM 的 IP | 用於 AzurePublic 和 ExternalPublic 流程 |
| PublicIP_s | 公用 IP 位址 | 用於 AzurePublic 和 ExternalPublic 流程 |
| DestPort_d | 目的地連接埠 | 是連入流量的連接埠 | 
| L4Protocol_s  | * T <br> * U  | 傳輸通訊協定。 T = TCP <br> U = UDP | 
| L7Protocol_s  | 通訊協定名稱 | 衍生自目的地連接埠 |
| FlowDirection_s | * 我 = 輸入<br> * O = 輸出 | 根據流量記錄的放大/縮小 NSG 流量的方向 | 
| FlowStatus_s  | * NSG 規則允許使用 = <br> * D = 拒絕 NSG 規則  | 流程允許/nblocked 的 NSG 流量記錄根據的狀態 |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | 網路安全性群組 (NSG) 流程相關聯 |
| NSGRules_s | \<索引值為 0） >< NSG_RULENAME >\<流程方向 >\<流程的狀態 >\<FlowCount ProcessedByRule > |  NSG 規則，允許或拒絕此流程 |
| NSGRuleType_s | * 使用者定義 * 預設 |   NSG 規則流程所使用的型別 |
| MACAddress_s | MAC 位址 | 在流程中所擷取的 nic 的 MAC 位址 |
| Subscription_s | 訂用帳戶的 Azure 虛擬網路 / 網路介面 / 虛擬機器會填入此欄位 | 只適用於 FlowType = S2S，P2S、 AzurePublic、 ExternalPublic、 MaliciousFlow 和 UnknownPrivate 流程類型 （其中只有一個側邊是 azure 的流程類型） |
| Subscription1_s | 订阅 ID | 訂用帳戶 ID 的虛擬網路 / 網路介面 / 流程中的來源 IP 所屬的虛擬機器 |
| Subscription2_s | 订阅 ID | 訂用帳戶 ID 的虛擬網路 / 網路介面 / 目的地 ip 位址，在流程中的所屬的虛擬機器 |
| Region_s | Azure 區域的虛擬網路 / 網路介面 / 流程中的 IP 所屬的虛擬機器 | 只適用於 FlowType = S2S，P2S、 AzurePublic、 ExternalPublic、 MaliciousFlow 和 UnknownPrivate 流程類型 （其中只有一個側邊是 azure 的流程類型） |
| Region1_s | Azure 區域 | Azure 區域的虛擬網路 / 網路介面 / 流程中的來源 IP 所屬的虛擬機器 |
| Region2_s | Azure 區域 | 在流程中的目的地 ip 位址所屬的虛擬網路的 azure 區域 |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  傳送或接收流量的 VM 相關聯的 NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 在流程中的來源 IP 相關聯的 NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 在流程中的目的地 IP 相關聯的 NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | 網路介面 NIC_s 相關聯的虛擬機器 |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | 在流程中的來源 IP 相關聯的虛擬機器 |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | 在流程中的目的地 IP 相關聯的虛擬機器 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | NIC_s 相關聯的子網路 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 在流程中的來源 IP 相關聯的子網路 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | 在流程中的目的地 ip 位址相關聯的子網路 |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 在流程中的來源 IP 相關聯的應用程式閘道 | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 在流程中的目的地 ip 位址相關聯的應用程式閘道 |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 在流程中的來源 IP 相關聯的負載平衡器 |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 在流程中的目的地 ip 位址相關聯的負載平衡器 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 在流程中的來源 IP 相關聯的區域網路閘道 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 在流程中的目的地 ip 位址相關聯的區域網路閘道 |
| ConnectionType_s | 可能的值為 VNetPeering、 VpnGateway 和 ExpressRoute |    連線類型 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 連線名稱 |
| ConnectingVNets_s | 虛擬網路名稱的空格分隔的清單 | 中樞和支點拓撲中，如果中樞虛擬網路將此處填入 |
| Country_s | 兩個字母的國碼 (ISO 3166-1 alpha-2) | 針對流程類型 ExternalPublic 填入。 PublicIPs_s 欄位中的所有 IP 位址會都共用相同的國家/地區程式碼 |
| AzureRegion_s | Azure 區域位置 | 針對流程類型 AzurePublic 填入。 PublicIPs_s 欄位中的所有 IP 位址會都共用的 Azure 區域 |
| AllowedInFlows_d | | 允許的輸入流量的計數。 這表示共用相同的四個 tuple 的流程數目輸入處擷取流程 netweork 介面 | 
| DeniedInFlows_d |  | 已拒絕的輸入流量的計數。 （輸入的流程中所擷取的網路介面） |
| AllowedOutFlows_d | | 允許 (Outbound，以便在流程中所擷取的網路介面) 的輸出流程的計數 |
| DeniedOutFlows_d  | | 被拒絕 (Outbound，以便在流程中所擷取的網路介面) 的輸出流程的計數 |
| FlowCount_d | 已被取代。 比對相同的四個 tuple 的總流量。 發生非固定格式類型 ExternalPublic 和 AzurePublic，計數會包含來自各種 PublicIP 位址以及流程。
| InboundPackets_d | 接收的 NSG 規則套用的網路介面捕捉到封包 | 這會填入僅針對版本 2 的 NSG 流程記錄的結構描述 |
| OutboundPackets_d  | 當擷取的 NSG 規則套用的網路介面傳送的封包 | 這會填入僅針對版本 2 的 NSG 流程記錄的結構描述 |
| InboundBytes_d |  NSG 規則套用的網路介面的捕捉到接收的位元組 | 這會填入僅針對版本 2 的 NSG 流程記錄的結構描述 |
| OutboundBytes_d | 送出的 NSG 規則套用的網路介面捕捉到的位元組 | 這會填入僅針對版本 2 的 NSG 流程記錄的結構描述 |
| CompletedFlows_d  |  | 這會填入僅針對版本 2 的 NSG 流程記錄的結構描述的非零值 |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 所列的項目分隔 |
    
### <a name="notes"></a>注意
    
1. AzurePublic 和 ExternalPublic 流程，如果客戶擁有公用 IP 位址會被填入 PublicIPs_s 欄位中時，將會填入 VMIP_s 欄位中的 Azure VM 的 IP。 針對這些兩個流程類型，我們應該使用 VMIP_s 和 PublicIPs_s 代替 SrcIP_s 和 DestIP_s 欄位。 AzurePublic 和 ExternalPublicIP 的地址，我們彙總進一步，以便內嵌至客戶的 log analytics 工作區的記錄數目會最少。（即將淘汰此欄位和我們應該使用 SrcIP_ 和 DestIP_s 取決於 azure VM 是否為來源或流程中的目的地） 
1. 非固定格式類型的詳細資料：我們根據流程相關的 IP 位址，將在下列流程類型的流程： 
1. IntraVNet – 在流程中的兩個 IP 位址位於相同的 Azure 虛擬網路中。 
1. 內部 Vnet-在流程中的 IP 位址位於兩個不同 Azure 虛擬網路中。 
1. S2S – （站對站） 其中一個 IP 位址所屬的 Azure 虛擬網路時的其他 IP 位址屬於連接到 Azure 虛擬網路透過 VPN 閘道或 Express Route 的客戶網路 （站台）。 
1. P2S-（點對站） 的其中一個 IP 位址屬於 Azure 虛擬網路時的其他 IP 位址屬於客戶網路 （站台） 連接到 Azure 虛擬網路透過 VPN 閘道。
1. AzurePublic-其中一個 IP 位址所屬的 Azure 虛擬網路時的其他 IP 位址屬於 Microsoft 所擁有的 Azure 內部的公用 IP 位址。 客戶擁有的公用 IP 位址不是此流程類型的一部分。 比方說，任何客戶會擁有將流量傳送至 Azure 服務 （儲存體端點） 的 VM 會分類下此流程類型。 
1. ExternalPublic-其中一個 IP 位址屬於 Azure 虛擬網路不是在 Azure 中的公用 IP 的 IP 位址時，不會回報為惡意流量分析的處理間隔會耗用 ASC 摘要中 」FlowIntervalStartTime_t"和"FlowIntervalEndTime_t 」。 
1. MaliciousFlow-其中一個 IP 位址屬於 azure 虛擬網路的 IP 位址時，在 Azure 中並不會回報為惡意流量分析的處理間隔會耗用 ASC 摘要中的公用 IP 」FlowIntervalStartTime_t"和"FlowIntervalEndTime_t 」。 
1. UnknownPrivate-其中一個 IP 位址屬於 Azure 虛擬網路時的其他 IP 位址屬於私人 IP 範圍，如 RFC 1918 中所定義，而且無法對應，流量分析至客戶自有網站或 Azure 虛擬網路。
1. 未知-無法對應任一個 IP 位址在流程在 Azure 中的客戶拓樸，以及在內部部署 （網站）。
1. 某些欄位名稱會加上 _s 或 _d。 這些代表來源和目的地。

### <a name="next-steps"></a>後續步驟
若要取得常見問題的解答，請參閱[流量分析常見問題集](traffic-analytics-faq.md)若要查看有關功能的詳細資訊，請參閱[流量分析文件](traffic-analytics.md)
    


    


