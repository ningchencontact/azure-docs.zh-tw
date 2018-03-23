---
title: Azure 中的網路效能監控解決方案 | Microsoft Docs
description: Azure 中的「網路效能監控」可協助您即時監視網路的效能，以偵測和找出網路效能瓶頸。
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 399fe552d5c7d9a96cdabc2a1dfafe99635d4a61
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Azure 中的網路效能監控解決方案

![網路效能監控符號](./media/log-analytics-network-performance-monitor/npm-symbol.png)


網路效能監控 (NPM) 是雲端式的混合網路監視解決方案，可協助您監視網路基礎結構中多個端點間的網路效能、監視服務/應用程式端點的連線能力及監視 Azure ExpressRoute 的效能。  

NPM 會偵測網路問題，例如流量黑洞、路由錯誤，以及傳統網路監視方法無法偵測的問題。 此解決方案會產生警示，並在超過網路連結的閾值時產生通知，確保能及時偵測網路效能問題，並使問題的來源局限於特定網路區段或裝置。 

NPM 提供三種廣泛功能： 

[效能監視器](log-analytics-network-performance-monitor-performance-monitor.md)：可監視雲端部署和內部部署位置、多個資料中心和分公司、任務關鍵性多層式應用程式/微服務之間的網路連線。 利用效能監視器，您可以在使用者提出抱怨之前偵測到網路問題。  

[服務端點監視器](log-analytics-network-performance-monitor-service-endpoint.md)：您可以監視從使用者到所關心服務的連線，判斷路徑中有哪些基礎結構，以及發生網路瓶頸之處。 搶先您的使用者得知中斷情況，並查看網路路徑上發生問題的確切位置。 

此功能可協助您執行 http、HTTPS、TCP 與 ICMP 型測試以監視近乎即時或過去服務可用性與回應時間，以及因網路造成的封包遺失與延遲問題數目。 使用網路拓撲地圖，您可以利用每個躍點的延遲資料，識別從節點到服務的網路路徑沿途所發生的問題點。 使用內建測試，完全不需任何預先設定，即可監視與 Office365 和 Dynamics CRM 的網路連線。 使用此功能，您可以監視任何支援 TCP 端點的網路連線，例如網站、SaaS，PaaS 應用程式、SQL 資料庫等。  

[ExpressRoute 監視器](log-analytics-network-performance-monitor-expressroute.md)：透過 Azure ExpressRoute 監視分公司與 Azure 之間的端對端連線與效能。  
 

## <a name="set-up-and-configure"></a>安裝及設定

### <a name="install-and-configure-agents"></a>安裝及設定代理程式 

使用[將 Windows 電腦連接到 Log Analytics](log-analytics-windows-agents.md) 和[將 Operations Manager 連接到Log Analytics](log-analytics-om-agents.md) 中的基本程序來安裝代理程式。

### <a name="where-to-install-the-agents"></a>安裝代理程式的位置 

**效能監視器**：至少在一個節點上安裝 OMS 代理程式，您即可針對與該節點連線的每個子網路來監視和其他子網路的網路連線。  

若要監視網路連結，您必須在該連結的兩個端點上安裝代理程式。  如果您不確定您的網路拓撲，請在具有重要工作負載且您想要監視其間網路效能的伺服器上安裝代理程式。 例如，如果您想要監視網頁伺服器和執行 SQL 的伺服器 之間的網路連線，請在這兩部伺服器上安裝代理程式。 代理程式會監視主機之間的網路連線 (連結)，而不是主機本身。 

**服務端點監視器：**在您想要的每個節點上安裝 OMS 代理程式，監視它與服務端點之間的網路連線。 例如，如果您想要監視從辦公室網站 O1、O2 與 O3 至 Office365 的網路連線，請至少在一個節點上安裝 OMS 代理程式，O1、O2 及 O3 中各一。 

**ExpressRoute 監視器：**在您的 Azure VNET 中至少安裝一個 OMS 代理程式，以及在透過 ExpressRoute 私人對等互連來連線的內部部署子網路中至少安裝一個代理程式。  

### <a name="configure-oms-agents-for-monitoring"></a>設定 OMS 代理程式以進行監視  

NPM 使用綜合交易來監視來源與目的地代理程式之間的網路效能。 此解決方案可讓您 TCP 和 ICMP 之間選擇，作為要在監視「效能監視器」和「服務端點監視器」功能中進行監視的通訊協定，而 TCP 可用於「ExpressRoute 監視器」。 請確定防火牆允許 OMS 代理程式 (這些代理程式將用於在您選擇用於監視的通訊協定上進行監視) 之間的通訊。  

**TCP 通訊協定：**如果您選擇 TCP 作為用於監視的通訊協定，請在用於「效能監視器」和「ExpressRoute 監視器」功能的代理程式上開啟防火牆連接埠，以確保代理程式可以彼此連線。 若要這樣做，請使用系統管理權限執行 EnableRules.ps1 PowerShell 指令碼，而不需要 PowerShell 視窗中的任何參數。  

此指令碼會建立解決方案所需的登錄機碼並建立 Windows 防火牆規則，以允許代理程式彼此建立 TCP 連線。 此指令碼建立的登錄機碼也會指定是否記錄偵錯記錄檔和記錄檔的路徑。 它也會定義用於通訊的代理程式 TCP 連接埠。 此指令碼會自動設定這些機碼的值，所以您不應手動變更這些機碼。 預設開啟的連接埠是 8084。 您可以為指令碼提供 portNumber 參數來使用自訂連接埠。 不過，相同的連接埠應使用於指令碼執行所在的所有電腦上。 

>[!NOTE]
> 指令碼只會在本機設定 Windows 防火牆。 如果您有網路防火牆，您應該確定它允許目的地為 NPM 將使用之 TCP 連接埠的流量 

>[!NOTE]
> 您不需要針對「服務端點監視器」執行 EnableRules.ps1 PowerShell 指令碼 

 

**ICMP 通訊協定**：如果您選擇 ICMP 作為用於監視的通訊協定，請啟用下列防火牆規則，就能夠可靠地使用 ICMP： 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>設定方案 

1. 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) 或使用[從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)中所述的程序，將網路效能監視器方案新增至您的工作區。 
2. 開啟 Log Analytics 工作區，然後按一下 [概觀] 圖格。  
3. 按一下標題為 [網路效能監視器]  且訊息為「解決方案需要其他設定」 **的新圖格。

    ![NPM 圖格](media/log-analytics-network-performance-monitor/npm-config.png)

3. 您在 [設定] 頁面上看到的選項可安裝 OMS 代理程式，以及設定代理程式以在 [一般設定] 檢視中進行監視。 如上所述，如果您已安裝並設定 OMS 代理程式，請按一下 [設定] 檢視來設定您有興趣使用的功能。  

    **效能監視器檢視**：選擇預設效能監視器規則中的綜合交易應該使用哪種通訊協定，然後按一下 [儲存並繼續]。 此通訊協定選取項目只會保留系統產生的預設規則，而且您每次建立效能監視器規則時都需要明確地選擇通訊協定。 您之後可以隨時移至 [效能監視器] 索引標籤 (這會在您完成第 0 天設定之後出現)，再變更通訊協定。 如果您對效能監視器功能不感興趣，您可以從 [效能監視器] 索引標籤中的預設規則設定停用預設規則。 

    ![NPM 設定](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **服務端點監視器檢視**：此功能提供內建的預先設定測試，可從代理程式監視 Office365 和 Dynamcis365 的網路連線。 請選取 Office365 和 Dynamcis365 服務旁邊的核取方塊，來選擇您有興趣監視的服務。 按一下 [新增代理程式] 按鈕來選擇您要用來監視的代理程式。 如果您不想要使用此功能，或想要稍後再設定，您可以選擇跳過此步驟，然後直接按一下 [儲存並繼續] 而不選擇任何項目。  

    ![NPM 設定](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **ExpressRoute 監視器檢視**：按一下 [立即探索] 按鈕，以探索線到 VNET (位於與此 Log Analytics 工作區連結的 Azure 訂用帳戶中) 的所有 ExpressRoute 私人對等互連。  


    >[!NOTE] 
    > 目前，解決方案只會探索 ExpressRoute 私人對等互連。 

    >[!NOTE] 
    > 只會探索到那些已連線到 VNET (這些 VNET 與連結到此 Log Analytics 工作區的訂用帳戶關聯) 的私人對等互連。 如果您的 ExpressRoute 連線到與此工作區連結之訂用帳戶以外的 VNET，您必須在那些訂用帳戶中建立 Log Analytics 工作區，並使用 NPM 來監視那些對等互連。 

    ![NPM 設定](media/log-analytics-network-performance-monitor/npm-express-route.png)

    完成探索之後，探索到的私人對等互連會列在表格中。  

    ![NPM 設定](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    這些對等互連的監視一開始會處於停用狀態。 按一下您想要監視的每個對等互連，然後從右邊的 (RHS) 詳細資料檢視設定其監視。  按一下 [儲存] 按鈕以儲存設定。 如需詳細資訊，請參閱[設定 ExpressRoute 監視]()。  

    設定完成之後，需要 30 分鐘至一小時的時間來填入資料。 解決方案從您的網路中彙總資料，而您會在 NPM 概觀圖格上看到「解決方案需要其他設定」。 在收集資料並編製索引之後，概觀圖格會變更，並通知您網路的健康情況摘要。 接著，您可以選擇編輯 OMS 代理程式安裝所在節點的監視，以及從環境中探索到的子網路監視 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>編輯子網路與節點的監視設定 

至少安裝一個代理程式的所有子網路都會列在設定頁面的 [子網路] 索引標籤上。 


啟用或停用特定子網路的監視 

1. 選取或清除  **子網路識別碼** 旁邊的方塊，然後確定已視情況選取或清除  **[用於監視]** 。 您可以選取或清除多個子網路。 停用後，子網路就不受監視，因為代理程式將會更新為停止 ping 其他代理程式。 
2. 從清單中選取特定子網路，並且在包含非受控與受控節點的清單之間移動所需的節點，即可選擇您想要監視之子網路的節點。 您可以將**自訂描述**新增至子網路。 
3. 按一下 [儲存] 以儲存設定。 

#### <a name="choose-nodes-to-monitor"></a>選擇要監視的節點

所有已安裝代理程式的節點會都列在 [節點] 索引標籤中。 

1. 選取或清除您要監視或停止監視的節點。 
2. 視需要按一下 [用於監視] ****，或將它清除。 
3. 按一下 [檔案]。 


設定您感興趣的功能： 
- 設定[效能監視器](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- 設定[服務端點監視器](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- 設定 [ExpressRoute 監視器](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>資料收集詳細資料
當選取 TCP 時，網路效能監視器會使用 TCP SYN-SYNACK-ACK 交握封包，當選取 ICMP 做為收集中斷和延遲資訊的通訊協定時，會使用 ICMP ECHO ICMP ECHO REPLY。 追蹤路由也用來取得拓撲資訊。

下表顯示網路效能監視器的資料收集方法，以及有關如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP 交握/ICMP ECHO 訊息會每 5 秒進行一次，而資料會每 3 分鐘傳送一次 |
 

 
此方案會使用綜合交易來評估網路的健全狀況。 OMS 代理程式已安裝在網路交換 TCP 封包或 ICMP Echo (取決於已選取要監視的通訊協定) 各種點。 在過程中，代理程式會了解來回行程時間和封包遺失 (如果有的話)。 此外，每個代理程式也會定期執行其他代理程式的路徑追蹤，以找出網路中必須測試的所有各種路由。 使用這項資料，代理程式就能夠推論網路延遲和封包遺失數字。 測試會每 5 秒重複一次，而代理程式會先彙總三分鐘的資料，再將資料上傳至 Log Analytics 服務。 



>[!NOTE]
> 雖然代理程式會經常彼此通訊，但是在進行測試時不會產生大量網路流量。 代理程式只依賴 TCP SYN-SYNACK-ACK 交握封包來判斷遺失和延遲 - 不會交換任何資料封包。 在此過程中，代理程式只會在需要時彼此通訊，而且代理程式通訊拓撲已最佳化以減少網路流量。

## <a name="using-the-solution"></a>使用解決方案 

### <a name="npm-overview-tile"></a>NPM 概觀圖格 

啟用網路效能監視器解決方案之後，[概觀] 頁面上的解決方案圖格會提供網路健康情況的快速概觀。 

 ![NPM 概觀圖格](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>網路效能監視器儀表板 

[頂端網路健全狀況事件] 頁面會列出系統中最近的健全狀況事件和警示，以及事件變成作用中的時間。 每當針對監視規則所選擇的計量值 (遺失、延遲、回應時間或頻寬使用率) 超過閾值時，就會產生健康情況事件或警示。 

 **[效能監視器]** 頁面會提供由解決方案所監視之網路連結和子網路連結的健康情況摘要。 [拓撲] 圖格會通知您網路中受監視的網路路徑數目。 按一下此圖格，可直接瀏覽至拓撲檢視。 

 **[服務端點監視器]** 頁面會提供您所建立之不同測試的健康情況摘要。 [拓撲] 圖格會通知您受監視的端點數目。 按一下此圖格，可直接瀏覽至拓撲檢視。

 **[ExpressRoute 監視器]** 頁面會提供您受解決方案監視之各種不同 ExpressRoute 對等互連連線的健康情況摘要。 [拓撲] 圖格會透過網路中受監視的 ExpressRoute 線路通知您網路路徑數目。 按一下此圖格，可直接瀏覽至拓撲檢視。

 **[常用查詢]** 頁面包含一組搜尋查詢，可直接擷取原始的網路監視資料。 您可以使用這些查詢做為起點，建立自己的查詢以供自訂報告。 

![NPM 儀表板](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>深入鑽研 

您可以按一下解決方案儀表板上的各種連結，更深入鑽研任何感興趣的區域。 例如，當您看到儀表板上出現警示或狀況不良的網路連結時，您可以按一下它，進一步調查。 接著顯示的分頁會列出特定網路連結的所有子網路連結。 您可以看到每個子網路連結的遺失、延遲和健康情況，並快速找出哪些子網路連結造成問題。 您接著可以按一下  **[檢視節點連結]**  來查看狀況不良子網路連結的所有節點連結。 然後，您可以看到個別的節點間連結並尋找狀況不良的節點連結。 

您可以按一下  **[檢視拓撲]** 來檢視來源與目的地節點之間路由的逐一躍點拓撲。 狀況不良的路由會顯示為紅色，而且您可以檢視每個躍點產生的延遲，以便您快速識別特定網路部分的問題。 

 

### <a name="network-state-recorder"></a>網路狀態錄製器 

每個檢視會在特定時間點顯示您網路健全狀況的快照集。 根據預設，會顯示最新狀態。 在頁面頂端的列會顯示狀態正在顯示的時間點。 您可以按一下 [動作] 上的該列，選擇回到過去並檢視您網路健健康情況的快照集。 您也可以在檢視最新狀態時，選擇啟用或停用任何頁面的自動重新整理。 

 ![網路狀態錄製器](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>趨勢圖 

您會在向下鑽研的每個層級看到遺失、延遲、回應時間與頻寬使用率等適用計量的趨勢。 您可以使用圖形頂端的時間控制項，變更趨勢的時間間隔。 

趨勢圖會顯示效能計量效能的歷程記錄觀點。 某些網路問題的本質是暫時的，很難只透過查看網路的目前狀態來攔截。 這是因為問題可能會在有人注意前迅速浮現而後消失，只會在稍後的時間點再度出現。 應用程式管理員也可能難以發現這類暫時性問題，因為即使所有的應用程式元件都看似執行順暢，當應用程式的回應時間莫名增加時，通常會出現這些問題。 

您可以查看問題將顯示為網路延遲或封包遺失突增的趨勢圖，以輕鬆地偵測那些種類的問題。 您接著可以使用網路狀態錄製器，檢視發生問題當時的網路快照集和拓撲來調查問題。 

 
![趨勢圖](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>拓撲圖 

NPM 會在互動式拓撲圖上顯示來源與目的地端點之間路由的逐一躍點拓撲。 您可以按一下解決方案儀表板上的 [拓撲] 圖格，或按一下向下鑽研頁面上的 [檢視拓撲] 連結，來檢視拓撲圖。  

拓撲圖會顯示來源與目的地之間有多少個路由，以及資料封包所採用的路徑。 您也可看見每個網路躍點所產生的延遲。 路徑延遲總計超過閾值 (在對應監視規則中所設定) 的所有路徑都會顯示為紅色。  

當您按一下節點或將滑鼠移至拓撲圖上的該節點，您會看到節點的屬性 (如 FQDN 和 IP 位址)。 按一下躍點以查看其 IP 位址。 您可由所產生的延遲識別出發生問題的網路躍點。 您可以使用可摺疊動作窗格中的篩選器來選擇篩選特定的路徑。 您也可以使用動作窗格中的滑桿隱藏中繼躍點，來簡化網路拓樸。 您可以使用滑鼠滾輪來放大或縮小拓撲圖。 

請注意，圖中顯示的拓撲是第 3 層拓撲，不包含第 2 層裝置和連線。 

 
![拓撲圖](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Log Analytics 搜尋 

以圖形方式透過 NPM 儀表板和向下鑽研頁面公開的所有資料，也可以在 [Log Analytics 搜尋](log-analytics-log-search-new.md)中取得。 您可以執行存放庫資料的互動式分析，讓來自不同來源的資料相互關聯、建立自訂警示、建立自訂檢視，以及將資料匯出至 Excel、PowerBI 或可共用連結。 儀表板中的 [常用查詢] 區域有一些實用的查詢，您可以使用這些查詢做為起點來建立自己的查詢和報告。 

 

## <a name="provide-feedback"></a>提供意見反應 

**UserVoice** - 您可以張貼您對於希望我們改善之網路效能監視器功能的想法。 瀏覽我們的  [UserVoice 頁面](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring) \(英文\)。 

**加入我們的社群**  - 我們竭誠歡迎新客戶加入我們的社群。 加入其中，您就能夠提早存取新功能，並協助我們改善網路效能監視器。 如果您想加入，請填妥這份 [快速問卷調查](https://aka.ms/npmcohort) \(英文\)。 

## <a name="next-steps"></a>後續步驟 
- 深入了解[效能監視器](log-analytics-network-performance-monitor-performance-monitor.md)、[服務端點監視器](log-analytics-network-performance-monitor-performance-monitor.md)與 [ExpressRoute 監視器](log-analytics-network-performance-monitor-expressroute.md)。 
