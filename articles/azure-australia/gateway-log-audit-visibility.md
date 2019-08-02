---
title: Azure 澳大利亞的閘道記錄、審核和可見度
description: 如何在澳大利亞地區內設定記錄、審核和可見度, 以符合澳大利亞政府政策、法規和法規的特定需求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 5971fe53f52725a88c484edcc9a5a672fceceb07
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571363"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Azure 澳大利亞的閘道記錄、審核和可見度

偵測和回應網路安全性威脅需要產生、收集及分析與系統作業相關的資料。

Microsoft 在 Azure 中有內建工具, 可協助您執行記錄、審核和可見度, 以管理您部署在 Azure 中的系統安全性。 還有一個參考架構, 它會與澳大利亞網路安全中心 (ACSC) 消費者指引和資訊安全性手冊 (ISM) 的意圖一致。

閘道是在網路層做為資訊流程式控制制機制, 也可以控制開放系統互相連線 (OSI) 模型較高層級的資訊。 需要閘道來控制安全性網域之間的資料流程, 並防止從外部網路進行未經授權存取。 基於控制安全性網域之間的資訊流程的閘道重要性, 任何失敗 (尤其是較高的分類) 可能會有嚴重的後果。 因此, 在可能導致網路安全性事件的情況下, 對閘道而言, 警示人員的健全機制特別重要。

執行閘道的記錄和警示功能有助於偵測網路安全性事件、嘗試入侵, 以及不尋常的使用模式。 此外, 將事件記錄檔儲存在不同的安全記錄伺服器上, 會增加敵人刪除記錄資訊的難度, 以摧毀目標網路入侵的辨識項。

## <a name="australian-cyber-security-centre-acsc-requirements"></a>澳大利亞網路安全中心 (ACSC) 需求

美國聯邦系統的整體安全性需求是在 ACSC 資訊安全性手冊 (ISM) 中定義。 為了協助「聯邦」實體符合 Azure 中的這些需求, *ACSC 消費者指南– MICROSOFT AZURE 受保護*和*ACSC 的認證報告-Microsoft Azure*發行物詳述下列相關的特定需求若要記錄、審核和可見度:

1. 若要降低使用共用基礎雲端資源所產生的風險, 必須選擇 Microsoft Azure 提供的功能, 包括 Azure 安全性中心、Azure 監視器、Azure 原則和 Azure Advisor, 以協助實體執行即時監視其 Azure 工作負載

2. ACSC 也建議將所有規定的安全性記錄轉送至 ACSC, 以供整個澳大利亞政府監視之用

3. 為了協助降低風險, 必須在其 Azure 訂用帳戶內設定美國聯邦實體:

    * 啟用 Azure 安全中心
    * 升級為標準層
    * 啟用自動將 Microsoft Monitoring Agent 布建至支援的 Azure Vm
    * 在資訊安全中心儀表板上定期審查、prioritise 及緩和安全性建議和警示

4. 政府機構必須啟用從其 Azure 訂用帳戶轉送到 ACSC 的記錄和事件, 以提供 ACSC 與本指引不相容的可見度。 Azure 事件中樞提供執行外部記錄串流至美國聯邦實體所擁有之 ACSC 或內部部署系統的功能。

5. 美國聯邦的實體應將在 Azure 內啟用的記錄, 與 ISM 中指定的需求對齊

6. Microsoft 會在 Azure 中保留90天的記錄。 客戶實體必須執行記錄封存 regime, 以確保記錄可保留在 NAA AFDA 下所需的七年

7. 具有內部部署或以 Azure 為基礎之安全性資訊和事件管理 (SIEM) 功能的美國聯邦實體也可以將記錄轉送至這些系統

8. 「自由」實體應針對網路安全性群組 (Nsg) 和虛擬機器執行網路監看員流量記錄。 這些記錄應該儲存在只包含安全性記錄的專用儲存體帳戶中, 而且應該使用角色型存取控制來保護對儲存體帳戶的存取

9. 自由的實體必須實行 ACSC 的取用者指導方針, 以確保 Azure 工作負載符合 ISM 的目的以進行記錄和監視。 「自由」實體也必須選擇 Azure 功能, 以協助 ACSC 接收與 Azure 的澳大利亞政府使用量相關聯的即時監視、警示和記錄

## <a name="architecture"></a>架構

若要安心地瞭解進入和離開 Azure 環境的網路流量, 必須在正確的元件集上啟用必要的記錄。 這可確保完整的環境可見度, 並提供執行分析所需的資料。

![Azure 監視架構](media/visibility.png)

## <a name="components"></a>元件

上面顯示的架構是由提供記錄來源、記錄檔收集、記錄保留、記錄檔分析或事件回應之功能的離散元件所組成。 此架構包含一般會與網際網路可存取的 Azure 部署相關的個別元件。

|Functions|元件|
|---|---|
|記錄來源|<ul><li>應用程式閘道</li><li>VPN 閘道</li><li>Azure 防火牆</li><li>網路虛擬設備</li><li>Azure Load Balancer</li><li>虛擬機器</li><li>網域命名系統 (DNS) 伺服器</li><li>Syslog 和/或記錄收集伺服器</li><li>NSG</li><li>Azure 活動記錄檔</li><li>Azure 診斷記錄</li><li>Azure 原則</li></ul>|
|記錄檔集合|<ul><li>事件中樞</li><li>網路監看員</li><li>Log Analytics</li></ul>|
|記錄保留|<ul><li>Azure 儲存體</li></ul>|
|Log Analysis|<ul><li>Azure 資訊安全中心 (ASC)</li><li>Azure 建議程式</li><li>Log Analytics 解決方案<ul><li>流量分析</li><li>DNS 分析 (預覽版)</li><li>活動記錄分析</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|事件回應|<ul><li>Azure 警示</li><li>Azure 自動化</li></ul>|
|

此架構的運作方式是先從所需的來源產生記錄, 然後再將它們收集到集中式存放庫。 收集記錄檔之後, 就可以:

* 供 Azure analysis services 用來取得見解,
* 轉送至外部系統, 或
* 封存至長期保存的儲存體。

若要回應分析工具所識別的重要事件或事件, 可以設定警示, 並針對主動式管理和回應採取必要動作來進行自動化開發。

## <a name="general-guidance"></a>一般指導

在執行本文中列出的元件時, 適用下列一般指引:

* 驗證服務的區域可用性, 確保所有資料都保留在授權位置, 並部署至 AU Central 或 AU Central 2 作為受保護工作負載的第一個喜好設定

* 如需個別服務的認證狀態, 請參閱*AZURE ACSC 認證報告-受保護的 2018*發行集, 並根據 ACSC 取用者指南, 對未包含在報表中的任何相關元件執行自我評估 *–受保護的 Microsoft Azure*

* 對於本文中未參考的元件, 「自由」實體應遵循包含關於產生、捕獲、分析和保留記錄的原則

* 識別並 prioritise 高價值系統上的記錄、審核和可見度, 以及裝載于 Azure 之系統的所有網路輸入和輸出點

* 合併記錄, 並將記錄工具的實例數目降至最低, 例如儲存體帳戶、Log Analytics 工作區和事件中樞

* 透過角色型存取控制來限制系統管理許可權

* 針對在 Azure 中管理或設定資源的帳戶使用多重要素驗證 (MFA)

* 在多個訂用帳戶之間 centralising 記錄收集時, 請確定系統管理員具有每個訂閱中的必要許可權

* 請確認網路連線和任何必要虛擬機器的 proxy 設定 (包括網路虛擬裝置 (Nva)、記錄收集伺服器和 DNS 伺服器), 以連線至必要的 Azure 服務, 例如 Log Analytics 工作區, 事件中樞、和儲存體

* 設定 Microsoft Monitoring Agent (MMA) 以利用 TLS 版本1。2

* 使用 Azure 原則來監視和強制符合需求

* 在所有資料存放庫 (例如儲存體和資料庫) 上強制加密

* 使用本機多餘的儲存體 (LRS) 和快照集來取得儲存體帳戶和相關聯資料的可用性

* 考慮使用異地多餘儲存體 (GRS) 或異地儲存體, 以配合嚴重損壞修復策略

|Resource|URL|
|---|---|
|澳大利亞法規和原則合規性檔|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 產品-澳大利亞地區和非地區|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 澳大利亞中部, 澳大利亞中部-2, 澳大利亞-東部, 澳大利亞-東南部](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Microsoft Azure 安全性與稽核記錄管理白皮書|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Microsoft Monitoring Agent 設定|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>元件指引

本節提供在整體記錄、審核和可見度架構中, 每個元件及其角色用途的資訊。 提供額外的連結來存取有用的資源, 例如參考檔、指南和教學課程。

## <a name="log-sources"></a>記錄來源

在可以完成任何分析、警示或報告之前, 必須先產生必要的記錄檔。 Azure 記錄會分類為控制/記錄管理、資料平面記錄和已處理的事件。

|Type|描述|
|---|---|
|控制/記錄管理檔|提供 Azure Resource Manager 作業的相關資訊|
|資料平面記錄|提供在 Azure 資源使用量中引發之事件的相關資訊, 例如虛擬機器中的記錄, 以及可透過 Azure 監視器的診斷記錄|
|已處理的事件|提供 Azure 已處理的分析事件/警示的相關資訊, 例如 Azure 資訊安全中心已處理的位置, 以及分析訂閱以提供安全性警示|
|

### <a name="application-gateway"></a>應用程式閘道

Azure 應用程式閘道是 Azure 環境的其中一個可能進入點, 因此您需要捕捉與 web 應用程式通訊之連入連線的相關資訊。 應用程式閘道可以提供與 web 應用程式使用方式相關的重要資訊, 以及協助偵測網路安全性事件。 應用程式閘道會在 Azure 監視器中, 將中繼資料傳送至活動記錄和診斷記錄, 以便在 Log Analytics 中使用或發佈至事件中樞或儲存體帳戶。

|資源|連結|
|---|---|
|應用程式閘道文件|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|應用程式閘道快速入門手冊|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>VPN 閘道

VPN 閘道是在 Azure 環境中進行廣泛通訊的可能進入點, 例如內部部署環境與系統管理流量的連線。 登入 VPN 閘道可為 Azure 環境的連線提供深入解析和可追蹤性。 記錄可以提供審核和分析, 並協助偵測或調查惡意或異常的連線。 VPN 閘道記錄會傳送至 Azure 監視器活動記錄檔, 您可以在其中使用記錄分析, 或將它們發佈至事件中樞或儲存體帳戶。

|資源|連結|
|---|---|
|VPN 閘道文件|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|澳大利亞政府特定 VPN 閘道指導方針|[https://aka.ms/AzGovAUSecurity](https://aka.ms/AzGovAUSecurity)|
|

### <a name="azure-firewall"></a>Azure 防火牆

Azure 防火牆會從 Azure 環境提供受控制的結束點, 而產生的記錄 (包括嘗試和成功輸出連線的相關資訊) 是記錄策略中的重要元素。 這些記錄可以驗證系統是否依照設計的方式運作, 以及協助偵測惡意程式碼或嘗試連接到未經授權外部系統的動作專案。 Azure 防火牆會將記錄寫入至活動記錄和診斷記錄, 其可用於 Log Analytics 中, 或發佈至事件中樞或儲存體帳戶的 Azure 監視器中。

|資源|連結|
|---|---|
|Azure 防火牆文件|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|教學課程：監視 Azure 防火牆記錄和計量|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>網路虛擬裝置 (NVA)

Nva 可以用來補充 Azure 中以原生方式提供的安全性功能。 在 Nva 上產生的記錄可能是偵測網路安全性事件的寶貴資源, 而且是整體記錄、審核和可見度策略的重要部分。 若要從 Nva 捕捉記錄, 請利用 Microsoft Monitoring Agent (MMA)。 對於不支援安裝 MMA 的 Nva, 請考慮使用 Syslog 或其他記錄收集伺服器來轉送記錄。

|資源|連結|
|---|---|
|網路虛擬裝置總覽|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|NVA 檔|請參閱廠商檔, 以在 Azure 中執行相關的 NVA|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Azure Load Balancer 記錄檔可用來取得與 Azure 中部署之系統相關的連線和使用方式的實用資訊。 這可用於健全狀況和可用性監視, 但也會形成另一個重要元件, 以取得通訊流量的必要深入解析, 以及偵測惡意或異常的流量模式。 Azure Load Balancer 記錄到 Azure 監視器中的活動記錄和診斷記錄, 其可在 Log Analytics 中使用或散發至事件中樞或儲存體帳戶。

|資源|連結|
|---|---|
|Azure Load Balancer 檔|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|標準 Load Balancer 的計量和健康情況診斷|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>虛擬機器

虛擬機器是傳送和接收網路通訊、處理資料並提供服務的端點。 虛擬機器可以裝載資料或重要的系統服務, 以確保它們正常運作, 且偵測網路安全性事件可能很重要。 虛擬機器收集各種事件和 audit 記錄檔, 以追蹤系統的作業, 以及在該系統上執行的動作。 在虛擬機器上收集的記錄可以使用 Microsoft Monitoring Agent 轉送到 Log Analytics 工作區, Azure 資訊安全中心和適用的 Log Analytics 解決方案可以加以分析。 虛擬機器也可以直接或透過記錄收集伺服器, 直接與 Azure 事件中樞或 SIEM 整合。

|資源|連結|
|---|---|
|虛擬機器|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|從虛擬機器收集資料|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|將虛擬機器記錄串流至事件中樞|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>功能變數名稱服務 (DNS) 伺服器

DNS 伺服器記錄提供與系統在內部或外部嘗試存取之服務相關的重要資訊。 捕捉 DNS 記錄有助於識別網路安全性事件, 並提供事件種類的深入解析, 以及可能受到影響的系統。 Microsoft 管理代理程式 (MMA) 可以在 DNS 伺服器上用來將記錄轉送到 Log Analytics, 以便在 DNS 分析 (預覽) 中使用。

|資源|連結|
|---|---|
|適用于虛擬網路的 Azure 名稱解析|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog 和記錄收集伺服器

若要從網路虛擬裝置接收記錄, 或從其他系統 SIEM 自訂安全性記錄以供在內部使用, 可以在 Azure Vnet 內部署專用伺服器。 Syslog 記錄可以在 Syslog 伺服器上收集, 並轉送至 Log Analytics 進行分析。 記錄收集伺服器是集中式監視系統或 Siem 所使用之任何記錄匯總和散發功能的一般詞彙。 這些可以用來簡化網路架構和安全性, 並在將記錄散發到集中化的功能之前進行篩選和匯總。

|資源|連結|
|---|---|
|Log Analytics 中的 Syslog 資料來源|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|記錄收集伺服器|如需監視和 SIEM 架構的詳細資訊, 請參閱廠商檔|
|

### <a name="network-security-groups-nsgs"></a>網路安全性群組 (NSG)

Nsg 控制在 Azure 中進出虛擬網路的流量。 Nsg 會針對允許或拒絕的流量套用規則, 包括 Azure 中的流量, 以及 Azure 和外部網路 (例如內部部署或網際網路) 之間的流量。 Nsg 會套用至虛擬網路內的子網或個別的網路介面。 若要在 Azure 中捕捉進入和離開系統的流量資訊, 可以透過網路監看員 NSG 流量記錄功能來啟用 NSG 記錄。 這些記錄是用來構成系統標準作業的基準, 而且是流量分析的資料來源, 可針對 Azure 中裝載之系統的流量模式提供詳細的深入解析。

|資源|連結|
|---|---|
|網路安全性群組檔|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|網路安全性群組流量記錄簡介|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|教學課程：使用 Azure 入口網站記錄進出虛擬機器的網路流量|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Azure 活動記錄檔

Azure 活動記錄是 Azure 監視器的一部分, 它是訂用客戶紀錄, 可讓您深入瞭解 Azure 中發生的訂用帳戶層級事件。 活動記錄可協助您判斷訂用帳戶中資源***上***任何寫入作業 (PUT、POST、DELETE) 的「內容、物件和時間」。 活動記錄對於追蹤在 Azure 環境中所做的設定變更非常重要。 Azure 活動記錄會自動提供給 Log Analytics 解決方案使用, 而且可以傳送至事件中樞或 Azure 儲存體以進行處理或保留。

|資源|連結|
|---|---|
|Azure 活動記錄檔|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|將 Azure 活動記錄檔串流至事件中樞|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Azure 診斷記錄

Azure 監視器診斷記錄是由 Azure 服務發出的記錄, 提供有關該服務之作業的豐富、經常性資料。 診斷記錄可讓您深入瞭解資源在詳細層級的作業, 而且可以用於各種需求, 例如進行審核或疑難排解。 Azure 診斷記錄會自動提供給 Log Analytics 解決方案使用, 而且可以傳送至事件中樞或 Azure 儲存體以進行處理或保留。

|資源|連結|
|---|---|
|Azure 診斷記錄檔|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|診斷記錄的支援服務|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Azure 原則

Azure 原則會強制執行如何部署資源的規則, 例如類型、位置和設定。 Azure 原則可以設定為確保只有在符合需求時才會部署資源。 Azure 原則是維護 Azure 環境完整性的核心元件。 與 Azure 原則相關的事件會記錄到 Azure 活動記錄中, 而且會自動提供給 Log Analytics 解決方案使用, 或者可以傳送至事件中樞或 Azure 儲存體進行處理或保留。

|資源|連結|
|---|---|
|Azure 原則文件|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|利用 Azure 原則和 Resource Manager 使用 Azure 藍圖的範本|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>記錄檔集合

從多個記錄來源產生後, 必須將記錄儲存在集中式位置, 以進行進行中的存取和分析。 Azure 會根據記錄類型和需求, 提供多個可使用的記錄收集方法和選項。

### <a name="event-hubs"></a>事件中樞

事件中樞的目的是要匯總各種來源的記錄資料以供散發。 從事件中樞, 記錄資料可以傳送到 SIEM、ACSC 的合規性, 以及長期保留的儲存體。

|資源|連結|
|---|---|
|事件中樞文件|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|事件中樞和外部工具的指引|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics 是 Azure 監視器的一部分, 用於記錄分析。 Log Analytics 會使用工作區做為儲存機制, 讓您可以在 Azure 中提供各種不同的分析工具和解決方案的記錄資料。 Log Analytics 會直接與各式各樣的 Azure 元件整合, 以及透過 Microsoft Monitoring Agent 的虛擬機器。

|資源|連結|
|---|---|
|Log Analytics 文件|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|教學課程：分析 Log Analytics 中的資料|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>網路監看員

ACSC 建議使用網路監看員, 以協助您瞭解及捕獲 Azure 訂用帳戶中的網路流量。 NSG 流量記錄可讓您輸入 Log Analytics 中的流量分析解決方案, 透過 Azure 以原生方式提供更高的可見度、分析和報告功能。 網路監看員也會直接從 Azure 入口網站提供封包捕捉功能, 而不需要登入虛擬機器。 封包捕獲可讓您建立封包捕獲會話, 以追蹤進出虛擬機器的流量。

|資源|連結|
|---|---|
|網路監看員|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|封包捕獲總覽|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>記錄保留

針對澳大利亞政府組織, 必須根據澳大利亞[管理功能處置授權單位 (AFDA)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx)的國家封存來保留 Azure 中所捕獲的記錄, 這會指定最多七年的保留記錄。

|記錄檔位置|保留週期|
|---|---|
|Azure 活動記錄檔|最多90天|
|Log Analytics 工作區|最多兩年|
|事件中樞|最多7天|
|

您必須負責確保記錄檔會適當地封存, 以符合 AFDA 和其他法律需求。

### <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體是 Azure 中長期保留的記錄存放庫。 Azure 儲存體可用來封存 Azure 中的記錄, 包括事件中樞、Azure 活動記錄和 Azure 診斷記錄。 儲存體中的資料保留期可以設定為零, 或可指定為天數。 保留零天表示記錄會永久保留, 否則值可以是介於1到2147483647之間的任何天數。

|資源|連結|
|---|---|
|Azure 儲存體文件|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|透過 Azure 事件中樞在 Azure Blob 儲存體或 Azure Data Lake Storage 中擷取事件|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|教學課程：使用 Azure 儲存體封存 Azure 計量和記錄資料|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Azure 儲存體複寫|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|建立 Blob 的快照集|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>記錄檔分析

一旦產生並儲存在集中式位置, 必須分析記錄, 以協助偵測嘗試或成功的安全性事件。 當偵測到安全性事件時, 機構需要能夠回應這些事件, 並追蹤、包含及補救任何威脅。

### <a name="azure-security-center-asc"></a>Azure 資訊安全中心 (ASC)

Azure 資訊安全中心提供統一的安全性管理和先進的威脅防護。 Azure 資訊安全中心可以跨工作負載套用安全性原則、限制暴露于威脅的程度, 以及偵測和回應攻擊。 Azure 資訊安全中心提供各種不同 Azure 元件的儀表板和分析。 在 ACSC 取用者指引中, 會將 Azure 資訊安全中心指定為需求。

|資源|連結|
|---|---|
|Azure 資訊安全中心文件|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|快速入門：將 Azure 訂用帳戶上架到資訊安全中心的標準層|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>流量分析

流量分析是以雲端為基礎的解決方案, 可讓您看到 Azure 中的使用者和應用程式活動。 流量分析會分析網路監看員 NSG 流量記錄, 以提供 Azure 中流量的深入解析。 流量分析可用來提供儀表板、報告、分析, 以及與虛擬網路中所見的網路流量相關的事件回應功能。 流量分析可提供重要的深入解析, 並協助識別和解決網路安全性事件。

|資源|連結|
|---|---|
|流量分析檔|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure 建議程式

Azure Advisor 分析資源設定和其他資料來建議解決方案, 以協助改善資源的效能、安全性及高可用性, 同時尋找降低整體 Azure 費用的機會。 ACSC 建議使用 Azure Advisor, 並針對 Azure 環境的設定提供容易存取和詳細的建議。

|資源|連結|
|---|---|
|Azure 建議程式文件|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|開始使用 Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>DNS 分析 (預覽版)

DNS 分析是一種 Log Analytics 解決方案, 它會收集、分析和關聯 Windows DNS 分析和 audit 記錄檔, 以及其他相關資料。 DNS 分析識別嘗試解析惡意功能變數名稱、過時的資源記錄、經常查詢的功能變數名稱, 以及 talkative DNS 用戶端的用戶端。 DNS 分析也可讓您深入瞭解 DNS 伺服器上的要求負載和動態 DNS 註冊失敗。 DNS 分析可用來提供儀表板、報表、分析, 以及與在 Azure 環境中所做的 DNS 查詢相關的事件回應功能。 DNS 分析提供重要的深入解析, 並協助識別和解決網路安全性事件。

|資源|連結|
|---|---|
|DNS 分析文件|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>活動記錄分析

活動記錄分析是 Log Analytics 解決方案, 可協助您跨多個 Azure 訂用帳戶分析及搜尋 Azure 活動記錄。 活動記錄分析可用來提供集中式儀表板、報表、分析, 以及與整個 Azure 環境資源上執行之動作相關的事件回應功能。 活動記錄分析可以協助進行審核和調查。

|資源|連結|
|---|---|
|在 Log Analytics 中收集並分析 Azure 活動記錄|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>安全性資訊和事件管理 (SIEM)

SIEM 是一種系統, 可提供集中化的儲存、審核和分析安全性記錄, 並已定義內嵌各種記錄資料和智慧型工具以進行分析、報告及事件偵測與回應的機制。 您可以使用包含 Azure 記錄資訊的 SIEM 功能, 來補充在 Azure 中以原生方式提供的安全性功能。 在 Azure、內部部署或軟體即服務 (SaaS) 功能中, 可根據特定需求來利用裝載于虛擬機器的 SIEM。

|資源|連結|
|---|---|
|Azure Sentinel (預覽)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|SIEM 檔|如需 SIEM 架構和指導方針, 請參閱廠商檔|
|使用 Azure 監視器與 SIEM 工具整合|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>澳大利亞網路安全中心

澳大利亞網路安全中心 (ACSC) 是澳大利亞政府在全國網路安全性的領導。 它整合了來自澳大利亞政府的網路安全性功能, 以改善澳大利亞社區的網路恢復能力, 並在數位年齡中支援澳大利亞的經濟和社交平安。 ACSC 建議將所有規定的系統產生記錄檔、事件和記錄檔轉送到 ACSC, 以供整個澳大利亞政府監視之用。

|資源|連結|
|---|---|
|澳大利亞網路安全中心網站|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>事件回應

產生適當的記錄檔, 將其收集到集中式存放庫並執行分析, 可增加對系統的瞭解, 並提供偵測網路安全性事件的機制。 在偵測到事件或事件之後, 下一步就是對這些事件做出反應, 並執行動作來維護系統健全狀況, 並保護服務和資料免于遭到入侵。 Azure 提供服務的組合, 可有效回應任何發生的事件。

### <a name="azure-alerts"></a>Azure 警示

您可以使用 Azure 警示來通知支援和安全性人員, 以回應特定事件。 這可讓「美國聯邦」實體主動回應本文所列的 analysis services 所引發之相關事件的偵測。

|資源|連結|
|---|---|
|Microsoft Azure 中的警示總覽|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|管理及回應 Azure 資訊安全中心的安全性警示|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|使用 Azure 監視器警示來回應事件|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Azure 自動化

Azure 自動化可讓美國聯邦的實體觸發動作以回應事件。 這可能是在虛擬機器上啟動封包捕獲、執行工作流程、停止或啟動虛擬機器或服務, 或是其他工作的範圍。 自動化可讓您快速回應警示, 而不需要手動介入, 因而減少事件或事件的回應時間和嚴重性。

|資源|連結|
|---|---|
|Azure 自動化檔|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|操作指南:使用警示來觸發 Azure 自動化 Runbook|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>後續步驟

如需在 Azure 中安全地管理閘道環境的詳細資訊, 請參閱[閘道安全遠端系統管理](gateway-secure-remote-administration.md)一文。
