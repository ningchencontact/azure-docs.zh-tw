---
title: 將應用程式與 Azure 虛擬網路整合
description: 示範如何將 Azure App Service 中的應用程式連接到新的或現有的 Azure 虛擬網路
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: ccompy
ms.openlocfilehash: 8a6d7fef902a3bd240b152cb15d6852a5fa0e7c6
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51687296"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>將您的應用程式與 Azure 虛擬網路整合
本文件說明 Azure App Service 虛擬網路整合功能，以及示範如何使用 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)中的應用程式來設定此功能。 [Azure 虛擬網路][VNETOverview] (VNet) 可讓您將許多 Azure 資源，放在非網際網路可路由網路中。 然後，可以使用 VPN 技術，將這些網路連線到您的內部部署網路。 

Azure App Service 具有兩種形式。 

1. 支援全系列價格方案 (除隔離式方案以外) 的多租用戶系統
2. 部署到您的 VNet 的 App Service 環境 (ASE)。 

本文會介紹 VNet 整合功能，該功能適用於多租用戶的 App Service。  如果您的應用程式位於 [App Service 環境][ASEintro]，則它已經在 VNet 中，並且不需要使用 VNet 整合功能來觸達相同 VNet 中的資源。

VNet 整合能讓您的 Web 應用程式存取虛擬網路中的資源，但不授與從虛擬網路存取 Web 應用程式的專用權限。 私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 只有使用透過內部負載平衡器 (ILB) 設定的 ASE 才可使用私人網站存取。 如需使用 ILB ASE 的詳細資訊，請從以下的文章開始︰[建立和使用 ILB ASE][ILBASE]。 

VNet 整合通常用於啟用從應用程式到 VNet 中執行之資料庫和 Web 服務的存取。 使用 VNet 整合時，您不需要公開 VM 上應用程式的公用端點，但可以改用專用的非網際網路可路由位址。 

VNet 整合功能：

* 需要「標準」、「進階」或「進階 V2」價格方案 
* 使用傳統 VNet 或 Resource Manager VNet 使用傳統或 Resource Manager VNet 
* 支援 TCP 和 UDP
* 使用 Web、行動、API 應用程式及函數應用程式
* 可讓應用程式一次只連接到 1 個 VNet
* 可在 App Service 方案中與最多五個 VNet 整合 
* 可讓 App Service 方案中的多個應用程式使用相同的 VNet
* 需要使用點對站 VPN 設定的虛擬網路閘道
* 由於閘道上的 SLA，可支援 99.9% SLA

VNet 整合不支援的事項包括：

* 掛接磁碟機
* AD 整合 
* NetBios
* 私人網站存取
* 透過 ExpressRoute 存取資源 
* 跨服務端點存取資源 

### <a name="getting-started"></a>開始使用
在將 Web 應用程式連接至虛擬網路之前，請留意以下事項：

* 目標虛擬網路必須先以路由式閘道啟用點對站 VPN，才能使該虛擬網路與應用程式連接。 
* VNet 與您的 App Service 方案 (ASP) 必須位於同一個訂用帳戶中。
* 與 VNet 整合的應用程式將使用指定給該 VNet 的 DNS。

## <a name="enabling-vnet-integration"></a>啟用 VNet 整合

VNet 整合功能的新版本處於預覽狀態。 它不會相依於點對站 VPN，而且也支援跨 ExpressRoute 或服務端點存取資源。 若要深入了解新的預覽功能，請移至本文件的結尾。 

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中設定閘道 ###

如果您已設定點對站位址的閘道，則可以略過設定與應用程序的 VNet 整合。  
若要建立閘道：

1. 在您的 VNet 中[建立閘道子網路][creategatewaysubnet]。  

1. [建立 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [將點設定為網站位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在點對站組態中停用 IKEV2，而且必須選取 SSTP。 位址空間必須位於下列位址區塊之一：

* 10.0.0.0/8 - 這表示 IP 位址範圍從 10.0.0.0 到 10.255.255.255
* 172.16.0.0/12 - 這表示 IP 位址範圍從 172.16.0.0 到 172.31.255.255 
* 192.168.0.0/16 - 這表示 IP 位址範圍從 192.168.0.0 到 192.168.255.255

如果您只是建立用於 App Service VNet 整合的閘道，則不需要將憑證上傳。 建立閘道可能需要 30 分鐘。 在佈建閘道之前，您將無法將應用程式與 VNet 整合。 

### <a name="configure-vnet-integration-with-your-app"></a>設定您的應用程式與 VNet 整合 ###

若要在您的應用程式上啟用 VNet 整合： 

1. 移至 Azure 入口網站中的應用程式並開啟應用程式設定，然後選取 [網路] > [VNet 整合]。 在設定 VNet 整合之前，將 ASP 擴充到標準 SKU 或更高版本。 
 ![VNet 整合 UI][1]

1. 選取 [新增 VNet]。 
 ![新增 VNet 整合][2]

1. 選取您的 VNet。 
  ![選取您的 VNet][8]
  
在最後一個步驟之後，您的應用程式將會重新啟動。  

## <a name="how-the-system-works"></a>系統的運作方式
VNet 整合功能是建置在點對站 VPN 技術之上。 Azure App Service 中的應用程式裝載於多租用戶的系統中，其會防止在 VNet 中直接佈建應用程式。 點對站技術可將網路存取限制為僅限裝載應用程式的虛擬機器。 應用程式會限制為僅透過混合式連線或透過 VNet 整合，將流量傳送至網際網路。 

![VNet 整合的運作方式][3]

## <a name="managing-the-vnet-integrations"></a>管理 VNet 整合功能
要在應用程式層級上，才能連接或中斷連接 VNet。 可在多個應用程式之間影響 VNet 整合的作業是在 App Service 方案層級上。 您可以從應用程式 UID 取得 VNet 的詳細資料。 相同的資訊也會顯示在 ASP 層級。 

 ![VNet 詳細資料][4]

從 [網路功能狀態] 頁面中，您可以看到應用程式是否已連接到 VNet。 無論您的 VNet 閘道基於何種原因關閉，您的狀態將顯示為未連接。 

現在可供您在應用程式層級 VNet 整合 UI 中使用的資訊，與您從 ASP 取得的詳細資訊相同。 這些項目如下：

* VNet 名稱 - 虛擬網路 UI 的連結
* 位置 - 這會反映 VNet 的位置。 在另一個位置與 VNet 整合，可能會導致您的應用程式出現延遲問題。 
* 憑證狀態 - 反映您的憑證是否在 App Service 方案和 VNet 之間同步處理。
* 閘道狀態 - 無論您的閘道基於何種原因關閉，您的應用程式將無法存取 VNet 中的資源。 
* VNet 位址空間 - 顯示 VNet 的 IP 位址空間。 
* 點對站位址空間 - 顯示 VNet 的點對站 IP 位址空間。 在呼叫 VNet 時，您的應用程式 FROM 位址會是其中一個位址。 
* 站對站位址空間：您可以使用站對站 VPN，將 VNet 連線到內部部署資源或連線到其他 VNet。 使用該 VPN 連線定義的 IP 範圍，如此處所示。
* DNS 伺服器 - 顯示使用 VNet 設定的 DNS 伺服器。
* 路由傳送至 VNet 的 IP 位址 - 顯示路由傳送至 VNet 的位址區塊 (用於驅動流量進入 VNet) 

您可以在 VNet 整合的應用程式檢視中採取的唯一作業，就是中斷連接目前連接到 VNet 的應用程式。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]。 當您從 VNet 中斷連線時，您的應用程式將重新啟動。 中斷連線不會變更您的 VNet。 VNet 及其組態 (包括閘道) 仍會保持原狀。 如果接著您要刪除 VNet，則需要先刪除其中資源，包括閘道。 

若要觸達 ASP VNet 整合 UI，請開啟您的 ASP UI 並選取 [網路]。  在 [VNet 整合] 下，選取 [按一下這裡以設定] 以開啟 [網路功能狀態 UI]。

![ASP VNet 整合資訊][5]

ASP VNet 整合 UI 會向您顯示 ASP 中的應用程式所使用的所有 VNet。 您的 App Service 方案中的任意數目的應用程式，最多可以連線到 5 個 VNet。 每個應用程式只能設定一個整合。 若要查看每個 VNet 的詳細資料，按一下您感興趣的 VNet 即可。 您可以在此處執行兩項動作。

* **同步網路**。 同步網路作業可確保您的憑證和網路資訊會保持同步。如果您新增或變更 VNet 的 DNS，則需要執行**同步網路**作業。 這項作業將重新啟動任何使用此 VNet 的應用程式。
* **新增路由** 新增路由將驅動輸出流量進入 VNet。

**路由** VNet 中所定義的路由用於將應用程式中的流量導向至 VNet。 如果需要將額外的輸出流量傳送到 VNet，則可以在此處新增這些位址區塊。   

**憑證** 啟用 [VNet 整合] 時，需要交換憑證以確保連線的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。
如果憑證或網路資訊已變更，您需要按一下 [同步網路]。 按一下 [同步處理網路] 時，會導致應用程式與 VNet 之間的連線短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。 

## <a name="accessing-on-premises-resources"></a>存取內部部署資源
應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 若要存取內部部署資源，您需要使用點對站位址區塊更新內部部署 VPN 閘道路由。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 此做法的詳細資料會根據閘道而有所不同，不在這裡詳述。 此外，您不能使用站對站 VPN 連線設定 BGP。

> [!NOTE]
> VNet 整合功能不會將應用程式與具有 ExpressRoute 閘道的 VNet 整合。 即使是在[共存模式][VPNERCoex]中設定 ExpressRoute 閘道，VNet 整合仍無法運作。 如果您需要透過 ExpressRoute 連線存取資源，則可使用在您 VNet 中執行的 [App Service 環境][ASE]。 
> 
> 

## <a name="peering"></a>對等互連
您可以使用 VNet 整合來存取與您所連接的 VNet 對等互連之 Vnet 中的資源。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時，啟用 [允許虛擬網路存取] 並核取 [允許轉寄的流量] 和 [允許閘道器傳輸]。
1. 在 VNet 上新增對等互連連線，該 VNet 與您連線的 VNet 對等互連。 在目的地 VNet 上新增對等互連連線時，啟用 [允許虛擬網路存取] 並核取 [允許轉寄的流量] 和 [允許遠端閘道器]。
1. 移至入口網站中的 [App Service 方案] > [網路] > [VNet 整合 UI]。  選取您的應用程式連線到的 VNet。 在路由區段中，新增與您的應用程式所連線到的 VNet 對等互連之 VNet 的位址範圍。  


## <a name="pricing-details"></a>價格詳細資料
使用 VNet 整合功能有三項相關費用：

* ASP 定價層需求
* 資料傳輸成本
* VPN 閘道器成本

您的應用程式必須是「標準」、「進階」或「進階 V2」方案。 您可以在這裡看到這些成本的詳細資料：[App Service 價格][ASPricing]。 

即使 VNet 位於相同的資料中心，也需要對資料輸出收費。 [資料傳輸定價詳細資料][DataPricing]中會說明這些費用。 

點對站 VPN 所需之 VNet 閘道的成本。 如需詳細資訊，請參閱 [VPN 閘道定價][VNETPricing]頁面。

## <a name="troubleshooting"></a>疑難排解
儘管功能易於設定，這不表示您的體驗不會遇到問題。 如果您在存取所需端點時遇到問題，有一些公用程式，您可以用來從應用程式主控台測試連線功能。 有兩個您可以使用的主控台。 一個是 Kudu 主控台，另一個則是 Azure 入口網站中的主控台。 若要從您的應用程式觸達 Kudu 主控台，請移至 [工具] -> [Kudu]。 這與移至 [sitename].scm.azurewebsites.net 相同。 開啟之後，請移至 [偵錯主控台] 索引標籤。若要前往 Azure 入口網站裝載的主控台，請從您的應用程式移至 [工具] -> [主控台]。 

#### <a name="tools"></a>工具
由於安全性限制，工具 **ping**、**nslookup** 和 **tracert** 無法透過主控台運作。 為了填滿此空隙，已加入兩個不同的工具。 為了測試 DNS 功能，已加入名為 nameresolver.exe的工具。 語法為：

    nameresolver.exe hostname [optional: DNS Server]

您可以使用 **nameresolver** 來檢查應用程式依賴的主機名稱。 如此一來您可以測試是否有任何項目未正確設定，而無法與 DNS 搭配，或可能對 DNS 伺服器沒有存取權。

下一個工具可讓您測試主機是否可與 TCP 連線，以及連接埠組合。 此工具稱為 **tcpping**，語法如下：

    tcpping.exe hostname [optional: port]

**tcpping** 公用程式會讓您知道是否可以連接特定主機和連接埠。 只有在下列情況才會顯示成功：有一個應用程式監聽主機和連接埠組合，並且有網路從您的應用程式存取到指定的主機和連接埠。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>偵錯 VNet 裝載資源的存取
有一些事物，可以阻止您的應用程式觸達特定的主機和連接埠。 大部分的情況下，會是下列三個情況的其中一種︰

* **路線中有防火牆。** 如果路線中有防火牆，則將會達到 TCP 逾時。 此案例中 TCP 逾時值就是 21 秒。 使用 **tcpping** 工具來測試連線能力。 TCP 逾時可能是因為許多項目越過防火牆並啟動。 
* **DNS 無法存取。** DNS 逾時值為三秒 (每部 DNS 伺服器)。 如果您有兩部 DNS 伺服器，逾時為 6 秒。 使用 nameresolver 來檢查 DNS 是否運作。 請記住，您不能使用 nslookup，因為它不會使用您的 VNet 設定使用的 DNS。
* **點對站位址範圍無效。** 點對站 IP 範圍需要在 RFC 1918 私人 IP 範圍中 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)。 如果範圍中使用的 IP 不在其中，則無法正常運作。 

如果這些項目沒有回答您的問題，請先確認基本問題，例如︰ 

* 閘道是否顯示為正在入口網站中啟動？
* 憑證是否顯示為同步中？
* 是否有任何人未在受影響的 ASP 中執行 [同步處理網路] 的情況下變更網路組態？ 

如果您的閘道已關閉，請重新啟動。 如果您的憑證未同步，請移至 VNet 整合的 ASP 檢視，並點擊 [同步網路]。 如果您懷疑 VNet 組態發生了未與 ASP 同步處理的變更，請點擊 [同步網路]。  [同步網路] 作業將重新啟動 ASP 中與該 VNet 整合的任何應用程式。 

如果一切都沒問題，您需要更深入發掘問題：

* 是否有任何其他使用 VNet 整合來觸達相同 VNet 中資源的應用程式？ 
* 您是否可以移至應用程式主控台，並使用 tcpping 觸達 VNet 中的任何其他資源？ 

如果上述任一項成立，表示 VNet 整合沒問題，問題出在其他地方。 此情況將更有挑戰性，因為沒有簡單的方法，來了解為何無法觸達主機:連接埠。 部分原因包括：

* 您已在主機上啟動防火牆，防止從點對站 IP 範圍存取應用程式連接埠。 跨子網路通常需要公用存取權。
* 目標主機已關閉
* 應用程式已關閉
* IP 或主機名稱錯誤
* 應用程式不是接聽您預期的連接埠。 您可以在端點主機上使用 "netstat -aon"，將處理序識別碼與接聽連接埠進行比對。 
* 您配置網路安全性群組的方式，讓這些群組防止從點對站 IP 範圍存取應用程式主機和連接埠

請記住，您不知道應用程式將使用點對站 IP 範圍中的哪個 IP，因此必須允許從整個範圍存取。 

其他偵錯步驟包括：

* 連線至 VNet 中的 VM，並嘗試從該處連接資源 host:port。 若要測試 TCP 存取，請使用 PowerShell 命令 **test-netconnection**。 語法為：

      test-netconnection hostname [optional: -Port]

* 啟動 VM 上的應用程式，並從主控台測試是否可從應用程式存取該主機和連接埠

#### <a name="on-premises-resources"></a>內部部署資源 ####

如果您的應用程式無法連接內部部署資源，則請檢查您是否可以從 VNet 連接資源。 使用 **test-netconnection** PowerShell 命令來檢查是否有 TCP 存取權。 如果 VM 無法觸達內部部署資源，請確定站對站 VPN 連線正在運作中。 如果正在運作中，接著檢查先前提到的相同事項，以及內部部署閘道組態和狀態。 

如果 VNet 裝載的 VM 可以連接您的內部部署系統，但您的應用程式無法連接此系統，則很可能是下列其中一個原因：

* 內部部署閘道中未以您的點對站 IP 範圍設定路由
* 網路安全性群組將封鎖點對站 IP 範圍的存取
* 內部部署防火牆將封鎖來自點對站 IP 範圍的流量


## <a name="powershell-automation"></a>PowerShell 自動化

您可以使用 PowerShell 將 App Service 與 Azure 虛擬網路整合。 如需隨時可執行的指令碼，請參閱[將 Azure App Service 中的應用程式連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) \(英文\)。

## <a name="hybrid-connections-and-app-service-environments"></a>混合式連線和 App Service 環境
有三個可讓您存取 VNet 裝載之資源的功能。 如下：

* VNet 整合
* 混合式連線
* App Service 環境

混合式連線需要您在網路中安裝稱為混合式連線管理員 (HCM) 的轉送代理程式。 HCM 必須同時能夠連接至 Azure 和您的應用程式。 混合式連線不需要遠端網路的輸入網際網路可存取端點，這是 VPN 連線所需的。 HCM 只在 Windows 上執行，而且您最多可有五個執行個體同時執行，以提供高可用性。 雖然混合式連線只支援 TCP，但是每個 HC 端點必須符合特定的主機:連接埠組合。 

App Service 環境功能可讓您在 VNet 中執行 Azure App Service 的單一租用戶執行個體。 如果您的應用程式位於 App Service 環境中，那麼您的應用程式可以存取 VNet 中的資源，而不需要任何額外的步驟。 使用 App Service 環境，您的應用程式可以在更強大的背景工作角色上執行，並且可以擴充到多達 100 個 ASP 執行個體。 App Service 環境適用於所有網路功能，包括 ExpressRoute 和服務端點。  

儘管有使用案例重疊的情況，但這些功能無法彼此取代。 知道使用哪個功能取決於您的需求。 例如︰

* 如果您是開發人員，而且想要在 Azure 中執行可存取辦公桌下工作站上之資料庫的網站，則最簡單的方式就是使用混合式連線。 
* 如果您是想要將大量 Web 內容放在公用雲端，並在自己的網路中管理它們的大型組織，則您會想要使用 App Service 環境。 
* 如果您有多個應用程式需要存取 VNet 中的資源，則 VNet 整合是最好的選擇。 

當 VNet 已經連線到內部部署網路，則使用 VNet 整合或 App Service 環境是取用內部部署資源的簡單方法。 如果 VNet 未連線到內部部署網路，則相較於安裝 HCM，搭配您的 VNet 設定站對站 VPN，需要更多的額外負荷。 

除了功能差異以外，還有價格差異。 App Service 環境功能是進階服務供應項目，除了其他很棒的功能外，還提供最多的網路組態可能性。 VNet 整合可與標準或進階 ASP 搭配使用，並非常適合從多租用戶 App Service 安全取用 VNet 中的資源。 混合式連線目前取決於 BizTalk 帳戶，此帳戶具有的價格層級，從一開始免費，然後根據您需要的數量而越發昂貴。 不過，當在許多網路之間工作時，沒有其他功能像混合式連線一樣，可讓您存取超過 100 個不同網路中的資源。 

## <a name="new-vnet-integration"></a>新的 VNet 整合 ##

有新版的 VNet 整合功能未取決於點對站 VPN 技術。 與既有的功能不同，新的預覽功能將可搭配 ExpressRoute 和服務端點使用。 

只有較新的 Azure App Service 縮放單位才提供新的功能。 如果您可以擴充到「進階 V2」，則您是使用較新的 App Service 縮放單位。 入口網站中的 VNet 整合 UI 將會告訴您，您的應用程式是否可以使用新 VNet 整合功能。 

新版本處於預覽狀態，並具有下列特性。

* 不需要任何閘道，即可使用新的 VNet 整合功能
* 除了與 ExpressRoute 連線的 VNet 整合之外，您可以透過 ExpressRoute 連線來存取資源，而不需要進行任何額外設定。
* 應用程式和 VNet 必須位於相同的區域
* 新功能需要 Resource Manager VNet 中的未使用子網路。
* App Service 方案必須是「標準」、「進階」或「進階 V2」方案
* 新功能處於預覽狀態時，不支援生產工作負載
* 您的應用程式必須位於 Azure App Service 部署中，該部署能夠擴展到「進階 V2」。
* 新的 VNet 整合功能不適用於 App Service 環境中的應用程式。
* 您無法刪除具有整合式應用程式的 VNet。  
* 新的 VNet 整合尚未提供路由表和全域對等互連。  
* 一個位址用於一個 App Service 方案執行個體。 因為子網路大小在指派之後就無法變更，所以請使用超過最大規模大小的子網路。 建議的大小為具有 32 個位址的 /27，因為這會容納擴充為 20 個執行個體的 App Service 方案。  您可以使用新的 VNet 整合功能來取用服務端點受保護資源。 若要這樣做，請在用於 VNet 整合的子網路上啟用服務端點。

若要使用新功能：

1. 移至入口網站中的 [網路 UI]。 如果您的應用程式能夠使用新的功能，那麼您將看到可使用新預覽功能的功能。  

 ![選取新的預覽 VNet 整合][6]

1. 選取 [新增 VNet (預覽)]。  

1. 選取您想要與其整合的 Resource Manager VNet，然後建立新的子網路，或選擇一個空白的現有子網路。 整合只需不到一分鐘即可完成。 整合期間，會重新啟動您的應用程式。  完成整合之後，您將在整合的 VNet 上看到詳細資料，頂端有一個橫幅，告訴您此功能處於預覽狀態。

 ![選取 [VNet 和子網路]][7]

若要使您的應用程式能夠使用您的 VNet 已設定的 DNS 伺服器，請為您的應用程式建立應用程式設定，其中名稱為 WEBSITE_DNS_SERVER，而值是伺服器的 IP 位址。  如果您有次要 DNS 伺服器，則建立另一個應用程式設定，其中名稱為 WEBSITE_DNS_ALT_SERVER，而值是伺服器的 IP 位址。 

若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]。 這會重新啟動 Web 應用程式。 

新的 VNet 整合功能可讓您使用服務端點。  若要在應用程式中使用服務端點，請使用新的 VNet 整合連線到選取的 VNet，然後在您用於整合的子網路上設定服務端點。 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: http://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
