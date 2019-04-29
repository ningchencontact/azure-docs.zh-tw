---
title: 適用於 VM 的 Azure 監視器 (預覽) 常見問題集 | Microsoft Docs
description: 適用於 VM 的 Azure 監視器是結合了 Azure VM 作業系統健康情況與效能監控的 Azure 解決方案，其會自動探索應用程式元件和與其他資源的相依性，並且會對應它們之間的通訊。 本文將回答常見問題。
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: ade12225a470b64278b9d27676ceab768f64d904
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596593"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>適用於 VM 的 Azure 監視器 (預覽) 常見問題集
此 Microsoft 常見問題集是適用於 VM 的 Azure 監視器常見問題清單。 若您有任何關於解決方案的其他問題，請前往[討論論壇](https://feedback.azure.com/forums/34192--general-feedback)並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

## <a name="can-i-onboard-to-an-existing-workspace"></a>我可以將現有的工作區上線嗎？
如果您的虛擬機器已經連線到 Log Analytics 工作區，則您可以在上線到適用於 VM 的 Azure 監視器時繼續使用該工作區，但前提是它位於[此處](vminsights-onboard.md#prerequisites)所列的其中一個支援區域。

上線時，我們會設定工作區的效能計數器，這將導致所有 VM 都會向工作區回報資料，開始收集此資訊，以便在適用於 VM 的 Azure 監視器中加以顯示和分析。  因此，您將會看到來自所有連線到所選工作區之 VM 的效能資料。  健康情況和對應功能只能針對您已指定來上線的 VM 加以啟用。

如需要啟用哪些效能計數器的詳細資訊，請參閱我們的[上線](vminsights-onboard.md)文章。

## <a name="can-i-onboard-to-a-new-workspace"></a>我可以上線到新的工作區嗎？ 
如果您的 VM 目前並未連線到現有的 Log Analytics 工作區，您需要建立新的工作區來儲存資料。  如果您透過 Azure 入口網站為適用於 VM 的 Azure 監視器設定單一 Azure VM，建立新的預設工作區即會自動完成。

如果您選擇使用指令碼型方法，則可在[上線](vminsights-onboard.md)文章中找到這些步驟。 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果我的 VM 已經向現有的工作區回報，我該怎麼做？
如果您已經從虛擬機器收集資料，則您可能已經將它設為向現有的 Log Analytics 工作區回報資料。  只要該工作區位於我們支援的其中一個區域，您就可以針對該預先存在的工作區啟用適用於 VM 的 Azure 監視器。  如果您已經使用的工作區不在我們支援的其中一個區域，目前將無法上線到適用於 VM 的 Azure 監視器。  我們正積極努力地支援更多區域。

>[!NOTE]
>我們會針對工作區設定效能計數器，其會影響要向該工作區回報的所有 VM，而不論您是否已選擇要將它們上線到適用於 VM 的 Azure 監視器。 如需如何針對工作區設定效能計數器的詳細資訊，請參閱我們的[文件](../../azure-monitor/platform/data-sources-performance-counters.md)。 如需針對適用於 VM 的 Azure 監視器所設定的計數器相關資訊，請參閱我們的[上線文件](vminsights-onboard.md#performance-counters-enabled)。  

## <a name="why-did-my-vm-fail-to-onboard"></a>為什麼無法將我的 VM 上線？
從 Azure 入口網站將 Azure VM 上線時，請執行下列步驟：

* 如果已選取該選項，即會建立預設的 Log Analytics 工作區。
* 已針對所選工作區設定效能計數器。 如果這個步驟失敗，您會注意到某些效能圖表和表格不會顯示您所上線之 VM 的資料。 您可以執行[此處](vminsights-onboard.md#enable-with-powershell)所述的 PowerShell 指令碼來修正此問題。
* 如果決定需要使用 Log Analytics 代理程式，可使用 VM 延伸模組在 Azure VM 上進行安裝。  
* 如果決定需要使用適用於 VM 的 Azure 監視器對應 Dependency Agent，可使用延伸模組在 Azure VM 上進行安裝。  
* 視需要設定支援健康情況功能的 Azure 監視器元件，並設定 VM 來回報健康情況資料。

在上線過程中，我們會檢查上述每一項的狀態，以便在入口網站中將通知狀態傳回給您。 設定工作區與代理程式安裝通常需要 5 至 10 分鐘。 在入口網站中檢視監視與健康情況資料需要額外的 5 至 10 分鐘。  

如果您已初始上線，並看到一則訊息指出必須將 VM 上線，則允許 VM 最多 30 分鐘的時間來完成此程序。 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>我只針對 VM 啟用了 Azure 監視器，為什麼我會看到 [健康情況] 功能所監視的所有 VM？
所有連線到 Log Analytics 工作區的 VM 都會啟用 [健康情況] 功能，即使該動作是從單一 VM 起始亦然。

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>我是否可以修改健全準則何時評估條件的排程？
否，此版本的健全準則時段和頻率均無法修改。 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>我是否可以停用我不需要監視之條件的健全準則？
在此版本中健全準則無法停用。

## <a name="are-the-health-alert-severities-configurable"></a>健康情況警示嚴重性是否可設定？  
無法修改健康情況警示的嚴重性，只能加以啟用或停用。 此外，某些警示嚴重性會根據健康情況準則的狀態來更新。 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>如果我重新設定特定健全準則的設定，它是否可以作用於特定執行個體？  
如果您修改健全準則執行個體的任何設定，即會修改 Azure VM 上相同類型的所有健全準則執行個體。 例如，如果對應到邏輯磁碟 C: 的磁碟可用空間健全準則執行個體閾值已修改，那麼，此閾值會套用到已針對相同 VM 探索並監視的所有其他邏輯磁碟。

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>[健康情況] 功能是否可監視邏輯處理器和核心？
否，Windows 不包含個別處理器和邏輯處理器層級健全準則，依預設僅會監視 CPU 總使用率，以根據 Azure VM 可用的邏輯 CPU 總數有效評估 CPU 壓力。 

## <a name="are-all-health-criteria-thresholds-configurable"></a>所有健全準則臨界值都是可設定的嗎？  
以 Windows VM 為目標的健全準則閾值均為不可修改的，因為其健全準則會設定為「執行中」或「可用」。 當您從[工作負載監視 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) \(英文\) 查詢健全狀態時，在下列情況中，會針對服務或實體顯示 **LessThan** 或 **GreaterThan** 的 *comparisonOperator* 值且「閾值」為 **4**：
   - DNS 用戶端服務健康情況：服務未執行。 
   - DHCP 用戶端服務健康情況：服務未執行。 
   - RPC 用戶端服務健康情況：服務未執行。 
   - Windows 防火牆服務健康情況：服務未執行。
   - Windows 事件記錄服務健康情況：服務未執行。 
   - 伺服器服務健康情況：服務未執行。 
   - Windows 遠端管理服務健康情況：服務未執行。 
   - 檔案系統錯誤或損毀：邏輯磁碟無法使用。

下列 Linux 健全準則的閾值不可修改，因為其健全狀態已設定為 *true*。 從工作負載監視 API 進行查詢時，實體的健全狀態會根據其內容顯示值為 **LessThan** 的 *comparisonOperator* 且「閾值」為 **1**：
   - 邏輯磁碟狀態：邏輯磁碟未連線/無法使用
   - 磁碟狀態：磁碟未連線/無法使用
   - 網路介面卡狀態 - 網路介面卡已停用

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>如何修改 [健康情況] 功能中隨附的警示？
為每個健全準則所定義的警示規則不會顯示於 Azure 入口網站中。 您只能在[工作負載監視 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) \(英文\) 中啟用或停用健康情況警示規則。 此外，您無法從 Azure 入口網站指派健康情況警示的 [Azure 監視器動作群組](../../azure-monitor/platform/action-groups.md)。 您只可以使用通知設定 API，設定在健康情況警示引發時所要觸發的動作群組。 目前，您可以針對 VM 指派動作群組，因此，對 VM 引發的所有「健康情況警示」會觸發相同的動作群組。 不同於傳統的 Azure 警示，每個健康情況警示規則均不具個別動作群組的概念。 此外，在觸發健康情況警示時，僅支援設定來提供電子郵件或簡訊通知的動作群組。 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我在適用於 VM 的效能圖表中看不到部分或任何資料
如果您在磁碟表格中或在某些效能圖表中看不到效能資料，則您可能未在工作區中設定效能計數器。 若要解決此問題，請執行下列 [PowerShell 指令碼](vminsights-onboard.md#enable-with-powershell)。

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>適用於 VM 的 Azure 監視器對應功能與服務對應有何不同？
適用於 VM 的 Azure 監視器對應功能會以服務對應為根據，但有下列差異：

* [對應] 檢視可以從 VM 刀鋒視窗，以及從 Azure 監視器下方適用於 VM 的 Azure 監視器進行存取。
* [對應] 中的連線現在可供點選，並在所選連線的側邊面板中顯示連線計量資料的檢視。
* 有一個新的 API 可用來建立對應，以便為更複雜的對應提供更好的支援。
* 受監視的 VM 現在包含於用戶端群組節點中，而環圈圖會顯示群組中受監視與未受監視的虛擬機器比例。  它也可以在展開群組時用來篩選機器清單。
* 受監視的虛擬機器現在包含於伺服器連接埠群組節點中，而環圈圖會顯示群組中受監視與未受監視的機器比例。  它也可以在展開群組時用來篩選機器清單。
* 對應樣式已更新，以便與 Application Insights 的應用程式對應更為一致。
* 側邊面板已經更新，並不需要完整的整合的服務對應-更新管理、 變更追蹤、 安全性和服務台中才支援。 
* 用來選擇要對應之群組與機器的選項已更新，現在支援訂用帳戶、資源群組、Azure 虛擬機器擴展集和雲端服務。
* 您無法在適用於 VM 的 Azure 監視器對應功能中建立新的服務對應機器群組。  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>為什麼我的效能圖表會顯示虛線？
發生的原因有數種。  萬一資料集合中有間隔，我們就會將線條描繪為虛線。  如果您針對啟用的效能計數器修改了資料取樣頻率 (預設設定是每隔 60 秒收集一次資料)，若您針對圖表選擇較窄的時間範圍，而且您的取樣頻率小於圖表中所使用的貯體大小 (例如，取樣頻率為每隔 10 分鐘，而圖表上的每個貯體是 5 分鐘)，則您會在圖表中看到虛線。  在此案例中，選擇更寬的時間範圍來檢視，應該會讓圖表線條顯示為實線，而非虛線。

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器支援群組嗎？
是，在您安裝相依性代理程式後，我們就會從 VM 收集資訊，以根據訂用帳戶、資源群組、虛擬機器擴展集和雲端服務顯示群組。  如果您先前使用服務對應，並已建立電腦群組，這些群組將會一併顯示。  如果您為您所檢視的工作區建立了電腦群組，這些群組也會出現在群組篩選條件中。 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>如何查看彙總效能圖表中衍生第 95 條百分位數線之項目的詳細資料？
根據預設，此清單會排序以顯示針對所選計量的第 95 個百分位數具有最高值的 VM，但可用記憶體圖表除外，因為它會顯示具有第 5 個百分位數之最小值的機器。  按一下圖表，將利用已選取的適當計量來開啟 [N 大排行榜] 檢視。

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>對應功能如何跨不同的 VNet 和子網路處理重複的 IP？
如果您跨子網路和 VNet 使用 VM 或 Azure 虛擬機器擴展集來複製 IP 範圍，它可能會造成適用於 VM 的 Azure 監視器對應功能顯示不正確的資訊。 這是個已知的問題，而我們正在研究改善此體驗的選項。

## <a name="does-map-feature-support-ipv6"></a>對應功能支援 IPv6 嗎？
對應功能目前僅支援 IPv4，而我們正在研究對於 IPv6 的支援。 我們也支援在 IPv6 內部設定通道的 IPv4。

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>當我針對資源群組或其他大型群組載入對應時，很難檢視該對應
儘管我們已改進對應來處理大型且複雜的設定，但我們理解到對應可擁有許多節點、連線，以及作為叢集使用的節點。  我們承諾會繼續加強支援以提高延展性。   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>[效能] 索引標籤上的網路圖表為什麼看起來與 [Azure VM 概觀] 頁面上的網路圖表不同呢？

適用於 Azure 虛擬機器的概觀頁面會根據客體 VM 中主機的活動量值來顯示圖表。  至於 [Azure VM 概觀] 上的網路圖表，它只會顯示將收費的網路流量。  這不包括虛擬網路間的流量。  針對適用於 VM 的 Azure 監視器顯示的資料和圖表，會以來自客體 VM 的資料為基礎，而網路圖表會顯示輸入和輸出到該 VM (包括虛擬網路間) 的所有 TCP/IP 流量。

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>回應時間方式測量資料儲存在 VMConnection，並顯示在連線 面板和活頁簿？

回應時間是近似值。 因為我們未執行檢測的應用程式的程式碼，我們並不真的知道何時開始將要求和回應的送達時。 而是我們觀察到的連接上傳送的資料，然後返回該連接上的資料。 我們的代理程式會追蹤這些傳送及接收並嘗試加以配對： 接收的傳送，後面接著一連串的序列會解譯為要求/回應配對。 這些作業之間的時間是回應時間。 它會包括網路延遲和伺服器處理時間。

這項估計值非常適合基礎要求/回應通訊協定： 單一要求送出連線，並以單一回應到達時。 這是 HTTP (S) （不含管線），但不是符合其他通訊協定。

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>如果我採用 Log Analytics 免費定價方案，有何限制？
如果您使用「免費」定價層設定了具有 Log Analytics 工作區的 Azure 監視器，則適用於 VM 的 Azure 監視器的對應功能僅支援五部已連線到工作區的電腦。 如果您有五部連線至免費工作區的 VM，您可中斷其中一部 VM 的連線，然後連線新的 VM，則新的 VM 不受監視，而且不會反映於 [對應] 頁面。  

在此情況下，當您開啟 VM 並從左側窗格選取 [Insights (預覽)] 時 (即使它已安裝在 VM 上)，系統會以 [立即試用] 選項提示您。  不過，如果此 VM 並未在適用於 VM 的 Azure 監視器上線，就不會以通常會出現的選項提示您。 

## <a name="next-steps"></a>後續步驟
檢閱[將適用於 VM 的 Azure 監視器上線](vminsights-onboard.md)，了解啟用虛擬機器監視的需求和方法。
