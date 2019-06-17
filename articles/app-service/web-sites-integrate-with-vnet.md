---
title: 將應用程式與 Azure 虛擬網路整合 - Azure App Service
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
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a5187ed299f77c11892c6e34c8dfd3f904c7e075
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067717"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>將您的應用程式與 Azure 虛擬網路整合
本文件說明 Azure App Service 虛擬網路整合功能，以及如何設定中的應用程式[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)。 [Azure 虛擬網路][VNETOverview] (VNet) 可讓您將許多 Azure 資源，放在非網際網路可路由網路中。  

Azure App Service 具有兩種形式。 

1. 支援全系列價格方案 (除隔離式方案以外) 的多租用戶系統
2. App Service Environment (ASE)，它會部署到您的 VNet，並支援隔離 」 定價方案的應用程式

此文件會透過兩個 VNet 整合功能，這是多租用戶 App Service 中使用。 如果您的應用程式位於 [App Service 環境][ASEintro]，則它已經在 VNet 中，並且不需要使用 VNet 整合功能來觸達相同 VNet 中的資源。 如需所有 App Service 網路功能的詳細資訊，請閱讀[網路功能的 App Service](networking-features.md)

有兩種形式的 VNet 整合功能

1. 一種版本可與相同區域中 Vnet 整合。 這種形式的功能需要在相同區域中的 VNet 子網路。 這項功能仍處於預覽狀態，但支援的 Windows 應用程式的生產工作負載有一些注意事項如下所示。
2. 另一個版本可與其他區域中的 Vnet 或傳統 Vnet 的整合。 此版本的功能需要虛擬網路閘道部署到您的 VNet。 這是點對站 VPN 基礎功能。

應用程式一次只能使用一種形式的 VNet 整合功能。 問題會是您應該使用哪一項功能。 您可以使用許多項目。 雖然是明確的差異：

| 問題  | 解決方法 | 
|----------|----------|
| 相同區域中想要連線到 RFC 1918 位址 10.0.0.0/8、 172.16.0.0/12 （192.168.0.0/16） | 區域 VNet 整合 |
| 想要連線到傳統 VNet 或另一個區域中的 VNet | 需要閘道的 VNet 整合 |
| 想要透過 ExpressRoute 連線 RFC 1918 端點 | 區域 VNet 整合 |
| 想要連線到服務端點之間的資源 | 區域 VNet 整合 |

兩項功能可讓您透過 ExpressRoute 連線非 RFC 1918 位址。 若要這麼做，您必須使用目前的 ASE。

使用區域的 VNet 整合不會將 VNet 連線至內部部署或設定服務端點。 這是個別的網路組態。 區域的 VNet 整合可讓您的應用程式，這些連線類型之間進行呼叫。

不論使用的版本，VNet 整合可讓您的 web 應用程式存取您的虛擬網路中的資源，但不輸入私用存取權授與您的 web 應用程式從虛擬網路。 私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 VNet 整合是只從您的應用程式傳出呼叫至您的 VNet。 

VNet 整合功能：

* 需要「標準」、「進階」或「進階 V2」價格方案 
* 支援 TCP 和 UDP
* 適用於 App Service 應用程式，且函式應用程式

VNet 整合不支援的事項包括：

* 掛接磁碟機
* AD 整合 
* NetBios

## <a name="regional-vnet-integration"></a>區域 VNet 整合 

使用與您的應用程式相同的區域中 Vnet 使用 VNet 整合時，它會需要至少 32 個位址在其中使用委派的子網路的使用。 子網路無法用於任何其他項目。 從您的應用程式進行輸出呼叫將會從此中委派子網路的位址。 當您使用這個版本的 VNet 整合時，從您的 VNet 中的位址進行的呼叫。 使用您的 VNet 位址，可讓您的應用程式：

* 保護服務的服務端點的呼叫
* 透過 ExpressRoute 連線存取資源
* 您已連線到 VNet 中的存取資源
* 跨對等互連的連線，包括 ExpressRoute 連線存取資源

這項功能處於預覽狀態，但它支援 Windows 應用程式生產工作負載，但有下列限制：

* 您可以只連線到在 RFC 1918 範圍內的位址。 這些都是在 10.0.0.0/8、 172.16.0.0/12、 192.168.0.0/16 位址區塊的位址。
* 您無法跨全域對等互連連線觸達資源
* 您不能設定從您的應用程式進入 VNet 的流量路由
* 此功能才可從較新的 App Service 縮放單位來支援 PremiumV2 App Service 方案。
* 此功能無法使用隔離式的方案的應用程式服務環境中的應用程式
* 此功能需要使用 Resource Manager VNet 中至少 32 個位址的未使用的子網路。
* 應用程式和 VNet 必須位於相同的區域
* 一個位址用於一個 App Service 方案執行個體。 因為子網路大小在指派之後就無法變更，所以請使用超過最大規模大小的子網路。 建議的大小為具有 32 個位址的 /27，因為這會容納擴充為 20 個執行個體的 App Service 方案。
* 您無法刪除具有整合式應用程式的 VNet。 您必須先移除整合 
* 您可以只有一個區域 VNet 整合，每個 App Service 方案。 在相同的 App Service 方案中的多個應用程式可以使用相同的 VNet。 

此功能為預覽狀態，也適用於 Linux。 若要使用 Resource Manager VNet 位於相同區域中的 VNet 整合功能：

1. 移至入口網站中的 [網路 UI]。 如果您的應用程式就能使用新的功能，您會看到一個選項來新增 VNet （預覽）。  

   ![選取 VNet 整合][6]

1. 選取 [新增 VNet (預覽)]  。  

1. 選取您想要與其整合的 Resource Manager VNet，然後建立新的子網路，或選擇一個空白的現有子網路。 整合只需不到一分鐘即可完成。 整合期間，會重新啟動您的應用程式。  完成整合之後，您將在整合的 VNet 上看到詳細資料，頂端有一個橫幅，告訴您此功能處於預覽狀態。

   ![選取 [VNet 和子網路]][7]

一旦您的應用程式已與您的 VNet 整合，它將使用相同的 DNS 伺服器，您的 VNet 設定。 

若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]  。 這會重新啟動 Web 應用程式。 

新的 VNet 整合功能可讓您使用服務端點。  若要在應用程式中使用服務端點，請使用新的 VNet 整合連線到選取的 VNet，然後在您用於整合的子網路上設定服務端點。 

### <a name="how-vnet-integration-works"></a>VNet 整合的運作方式

在 App Service 中的應用程式裝載於背景工作角色。 基本和更高的定價方案專用主控方案有相同的背景工作角色上執行任何其他客戶工作負載。 VNet 整合的運作方式掛接虛擬介面與委派的子網路中的位址。 因為從位址位於您的 VNet，才能夠存取大部分情況中，或透過您的 VNet 就像在您的 VNet 中的 VM。 網路功能的實作是與 VM 執行於您的 VNet，也就是為什麼某些網路功能尚無法使用這項功能時不同。

![VNet 整合](media/web-sites-integrate-with-vnet/vnet-integration.png)

啟用 VNet 整合時，您的應用程式仍能如往常一樣的相同管道網際網路的輸出呼叫。 應用程式屬性入口網站中所列的輸出位址仍是您的應用程式所使用的位址。 變更您的應用程式為何對服務端點保護的服務，或 RFC 1918 位址會進入您的 VNet。 

此功能僅支援每個背景工作的一個虛擬介面。  每個背景工作的一個虛擬介面表示每個 App Service 方案的一個區域 VNet 整合。 所有相同的 App Service 方案中的應用程式可以使用相同的 VNet 整合，但如果您需要應用程式連接至其他 VNet 時，您必須建立另一個 App Service 方案。 使用的虛擬介面不是客戶可以直接存取的資源。

由於這項技術的運作方式，可使用 VNet 整合的流量不會顯示網路監看員或 NSG 流量記錄中。  

## <a name="gateway-required-vnet-integration"></a>需要閘道的 VNet 整合 

閘道需要 VNet 整合功能：

* 可用來連線到任何區域中 Vnet 是在 Resource Manager 或傳統 Vnet
* 可讓應用程式一次連接到只有 1 個 VNet
* 可在 App Service 方案，與整合的最多五個 Vnet 
* 可讓相同的 VNet，以供在 App Service 方案中的多個應用程式而不會影響可供 App Service 方案的總數。  如果您有 6 應用程式使用相同的 VNet 位於相同的 App Service 方案，都會被視為正在使用的 1 個 VNet。 
* 需要已使用點對站 VPN 的虛擬網路閘道
* 不支援搭配 Linux 應用程式使用
* 在閘道上支援 99.9 %sla，因為 SLA

這項功能不支援：

* 透過 ExpressRoute 存取資源 
* 跨服務端點存取資源 

### <a name="getting-started"></a>開始使用

在將 Web 應用程式連接至虛擬網路之前，請留意以下事項：

* 目標虛擬網路必須先以路由式閘道啟用點對站 VPN，才能使該虛擬網路與應用程式連接。 
* VNet 與您的 App Service 方案 (ASP) 必須位於同一個訂用帳戶中。
* 與 VNet 整合的應用程式將使用指定給該 VNet 的 DNS。

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中設定閘道 ###

如果您已設定點對站位址的閘道，則可以略過設定與應用程序的 VNet 整合。  
若要建立閘道：

1. 在您的 VNet 中[建立閘道子網路][creategatewaysubnet]。  

1. [建立 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [將點設定為網站位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在點對站組態中停用 IKEV2，而且必須選取 SSTP。 位址空間必須是在 RFC 1918 位址區塊、 10.0.0.0/8、 172.16.0.0/12、 192.168.0.0/16

如果您只是建立虛擬網路閘道使用與應用程式服務的 VNet 整合，則您不需要上傳憑證。 建立閘道可能需要 30 分鐘。 在佈建閘道之前，您將無法將應用程式與 VNet 整合。 

### <a name="configure-vnet-integration-with-your-app"></a>設定您的應用程式與 VNet 整合 

若要在您的應用程式上啟用 VNet 整合： 

1. 移至 Azure 入口網站中的應用程式並開啟應用程式設定，然後選取 [網路] > [VNet 整合]。 ASP 必須位在標準 SKU，或最好是使用任一 VNet 整合功能。 
 ![VNet 整合 UI][1]

1. 選取 [新增 VNet]  。 
 ![新增 VNet 整合][2]

1. 選取您的 VNet。 
  ![選取您的 VNet][8]
  
在最後一個步驟之後，您的應用程式將會重新啟動。  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>閘道所需的 VNet 整合功能如何運作

VNet 整合功能已內建在點對站 VPN 技術之上時，需要閘道。 點對站技術可將網路存取限制為僅限裝載應用程式的虛擬機器。 應用程式會限制為僅透過混合式連線或透過 VNet 整合，將流量傳送至網際網路。 

![VNet 整合的運作方式][3]

## <a name="managing-vnet-integration"></a>管理 VNet 整合
要在應用程式層級上，才能連接或中斷連接 VNet。 可在多個應用程式之間影響 VNet 整合的作業是在 App Service 方案層級上。 從應用程式 > 網路 > VNet 整合入口網站中，您可以在 VNet 上取得詳細資料。 您可以看到在 ASP 層級，在此 ASP 中的類似資訊 > 網路 > 包括該 App Service 方案中的哪些應用程式會使用所給定的整合的 VNet 整合入口網站。

 ![VNet 詳細資料][4]

您可以使用到的資訊相同的應用程式與 ASP 入口網站之間是您在 VNet 整合 UI 中。

* VNet 名稱 - 虛擬網路 UI 的連結
* 位置 - 這會反映 VNet 的位置。 在另一個位置與 VNet 整合，可能會導致您的應用程式出現延遲問題。 
* 憑證狀態 - 反映您的憑證是否在 App Service 方案和 VNet 之間同步處理。
* 閘道狀態-應該您使用閘道需要 VNet 整合，您可以看見閘道狀態。
* VNet 位址空間 - 顯示 VNet 的 IP 位址空間。 
* 點對站位址空間 - 顯示 VNet 的點對站 IP 位址空間。 使用閘道的必要的功能時呼叫至您的 VNet 時，您的應用程式從位址會是其中一個地址。 
* 站對站位址空間：您可以使用站對站 VPN，將 VNet 連線到內部部署資源或連線到其他 VNet。 使用該 VPN 連線定義的 IP 範圍，如此處所示。
* DNS 伺服器 - 顯示使用 VNet 設定的 DNS 伺服器。
* 路由傳送至 VNet 的 IP 位址 - 顯示路由傳送至 VNet 的位址區塊 (用於驅動流量進入 VNet) 

您可以在 VNet 整合的應用程式檢視中採取的唯一作業，就是中斷連接目前連接到 VNet 的應用程式。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]  。 當您從 VNet 中斷連線時，您的應用程式將重新啟動。 中斷連線不會變更您的 VNet。 不會移除子網路或閘道。 如果您要刪除 VNet 時，您必須先中斷連接您的應用程式，並刪除資源，例如閘道。 

若要觸達 ASP VNet 整合 UI，請開啟您的 ASP UI 並選取 [網路]  。  在 [VNet 整合] 下，選取 [按一下這裡以設定]  以開啟 [網路功能狀態 UI]。

![ASP VNet 整合資訊][5]

ASP VNet 整合 UI 會向您顯示 ASP 中的應用程式所使用的所有 VNet。 若要查看每個 VNet 的詳細資料，按一下您感興趣的 VNet 即可。 您可以在此處執行兩項動作。

* **同步網路**。 同步處理網路作業只是閘道相依 VNet 整合功能。 執行同步處理網路作業，可確保您的憑證和網路資訊會保持同步。如果您新增或變更 VNet 的 DNS，則需要執行**同步網路**作業。 這項作業將重新啟動任何使用此 VNet 的應用程式。
* **新增路由** 新增路由將驅動輸出流量進入 VNet。

**路由** VNet 中所定義的路由用於將應用程式中的流量導向至 VNet。 如果需要將額外的輸出流量傳送到 VNet，則可以在此處新增這些位址區塊。 此功能只能搭配閘道需要 VNet 整合。

**憑證**閘道需要啟用 VNet 整合時不需要的交換憑證以確保連線的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。
如果憑證或網路資訊已變更，您需要按一下 [同步網路]。 按一下 [同步處理網路] 時，會導致應用程式與 VNet 之間的連線短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。 

## <a name="accessing-on-premises-resources"></a>存取內部部署資源
應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 如果您使用閘道所需的 VNet 整合，您需要更新您的內部部署 VPN 閘道路由與您的點對站位址區塊。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 此做法的詳細資料會根據閘道而有所不同，不在這裡詳述。 您不能設定具有站對站 VPN 連線的 BGP。

沒有任何連線到您的 VNet，以及在內部部署區域的 VNet 整合功能所需的其他組態。 您只需要將 VNet 連線至內部部署使用 ExpressRoute 或站對站 VPN。 

> [!NOTE]
> VNet 整合功能不會與具有 ExpressRoute 閘道的 VNet 整合的應用程式時，需要閘道。 即使是在[共存模式][VPNERCoex]中設定 ExpressRoute 閘道，VNet 整合仍無法運作。 如果您需要存取資源，透過 ExpressRoute 連線，則您可以使用區域的 VNet 整合功能或[App Service Environment][ASE]，在您的 VNet 中執行。 
> 
> 

## <a name="peering"></a>對等互連
如果您使用對等互連時，區域的 VNet 整合，您不需要進行任何額外的設定。 

如果您使用閘道所需要的對等互連的 VNet 整合，您需要設定幾個額外的項目。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時，啟用 [允許虛擬網路存取]  並核取 [允許轉寄的流量]  和 [允許閘道器傳輸]  。
1. 在 VNet 上新增對等互連連線，該 VNet 與您連線的 VNet 對等互連。 在目的地 VNet 上新增對等互連連線時，啟用 [允許虛擬網路存取]  並核取 [允許轉寄的流量]  和 [允許遠端閘道器]  。
1. 移至入口網站中的 [App Service 方案] > [網路] > [VNet 整合 UI]。  選取您的應用程式連線到的 VNet。 在路由區段中，新增與您的應用程式所連線到的 VNet 對等互連之 VNet 的位址範圍。  


## <a name="pricing-details"></a>價格詳細資料
區域的 VNet 整合功能具有 ASP 定價層的費用之外不需額外付費。

有三個相關的費用，以使用所需的閘道 VNet 整合功能：

* ASP 的定價層費用-而您的應用程式必須在標準、 進階或 PremiumV2 App Service 方案。 如需有關這些成本的更多詳細資料，請參閱這裡：[App Service 價格][ASPricing]。 
* 資料傳輸成本有會資料輸出費用，即使 VNet 位於相同的資料中心。 [資料傳輸定價詳細資料][DataPricing]中會說明這些費用。 
* VPN 閘道器成本-這裡是所需的點對站 VPN 將 VNet 閘道的成本。 如需詳細資訊，請參閱 [VPN 閘道定價][VNETPricing]頁面。


## <a name="troubleshooting"></a>疑難排解
儘管功能易於設定，這不表示您的體驗不會遇到問題。 如果您在存取所需端點時遇到問題，有一些公用程式，您可以用來從應用程式主控台測試連線功能。 有兩個您可以使用的主控台。 一個是 Kudu 主控台，另一個則是 Azure 入口網站中的主控台。 若要從您的應用程式觸達 Kudu 主控台，請移至 [工具] -> [Kudu]。 這與移至 [sitename].scm.azurewebsites.net 相同。 開啟之後，請移至 [偵錯主控台] 索引標籤。若要前往 Azure 入口網站裝載的主控台，請從您的應用程式移至 [工具] -> [主控台]。 

#### <a name="tools"></a>工具
由於安全性限制，工具 **ping**、**nslookup** 和 **tracert** 無法透過主控台運作。 為了填滿此空隙，已加入兩個不同的工具。 為了測試 DNS 功能，已加入名為 nameresolver.exe的工具。 語法為：

    nameresolver.exe hostname [optional: DNS Server]

您可以使用 **nameresolver** 來檢查應用程式依賴的主機名稱。 如此一來您可以測試是否有任何項目未正確設定，而無法與 DNS 搭配，或可能對 DNS 伺服器沒有存取權。 您可以看到您的應用程式會使用主控台中，藉由查看 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER 環境變數的 DNS 伺服器。

下一個工具可讓您測試主機是否可與 TCP 連線，以及連接埠組合。 此工具稱為 **tcpping**，語法如下：

    tcpping.exe hostname [optional: port]

**tcpping** 公用程式會讓您知道是否可以連接特定主機和連接埠。 只有在下列情況才會顯示成功：有一個應用程式監聽主機和連接埠組合，並且有網路從您的應用程式存取到指定的主機和連接埠。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>偵錯 VNet 裝載資源的存取
有一些事物，可以阻止您的應用程式觸達特定的主機和連接埠。 大部分的情況下，會是下列三個情況的其中一種︰

* **路線中有防火牆。** 如果路線中有防火牆，則將會達到 TCP 逾時。 此案例中 TCP 逾時值就是 21 秒。 使用 **tcpping** 工具來測試連線能力。 TCP 逾時可能是因為許多項目越過防火牆並啟動。 
* **DNS 無法存取。** DNS 逾時值為三秒 (每部 DNS 伺服器)。 如果您有兩部 DNS 伺服器，逾時為 6 秒。 使用 nameresolver 來檢查 DNS 是否運作。 請記住，您不能使用 nslookup，因為它不會使用您的 VNet 設定使用的 DNS。 如果無法存取，您可能會有防火牆或 NSG 封鎖存取 DNS 或它可能已關閉。

如果這些項目沒有回答您的問題，看起來情形等項目的第一個項目： 

**區域 VNet 整合**
* 是您的目的地的 RFC 1918 位址嗎
* 有從您的整合子網路 NSG 封鎖輸出
* 如果通過 ExpressRoute 或 VPN，您的內部部署閘道設定為備份至 Azure 的流量路由傳送嗎？ 如果您可以連線到您的 VNet，但不在內部端點，這是最好檢查。

**需要閘道的 VNet 整合**
* 是在 RFC 1918 的範圍中的點對站位址範圍 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)？
* 閘道是否顯示為正在入口網站中啟動？ 如果您的閘道已關閉，請重新啟動。
* 憑證是否顯示為同步中，或您懷疑網路組態已變更？  如果您的憑證未同步，或是您懷疑已對 VNet 組態未與 Asp 同步處理的變更，然後點擊 [同步網路]。
* 如果通過 ExpressRoute 或 VPN，您的內部部署閘道設定為備份至 Azure 的流量路由傳送嗎？ 如果您可以連線到您的 VNet，但不在內部端點，這是最好檢查。

網路問題進行偵錯是一項挑戰，因為您不能看到什麼封鎖存取特定的主機： 連接埠組合。 部分原因包括：

* 您已在主機上啟動防火牆，防止從點對站 IP 範圍存取應用程式連接埠。 跨子網路通常需要公用存取權。
* 目標主機已關閉
* 應用程式已關閉
* IP 或主機名稱錯誤
* 應用程式不是接聽您預期的連接埠。 您可以在端點主機上使用 "netstat -aon"，將處理序識別碼與接聽連接埠進行比對。 
* 您配置網路安全性群組的方式，讓這些群組防止從點對站 IP 範圍存取應用程式主機和連接埠

請記住，您不知道您的應用程式實際上會使用哪些位址。 這可能是整合的子網路或點對站位址範圍內的任何位址，因此您需要允許從整個位址範圍存取。 

其他偵錯步驟包括：

* 連線至 VNet 中的 VM，並嘗試從該處連接資源 host:port。 若要測試 TCP 存取，請使用 PowerShell 命令 **test-netconnection**。 語法為：

      test-netconnection hostname [optional: -Port]

* 啟動 VM 並測試存取上的應用程式，該主控件，並從主控台，從您的應用程式使用連接埠**tcpping**

#### <a name="on-premises-resources"></a>內部部署資源 ####

如果您的應用程式無法連接內部部署資源，則請檢查您是否可以從 VNet 連接資源。 使用 **test-netconnection** PowerShell 命令來檢查是否有 TCP 存取權。 如果您的 VM 無法觸達內部部署資源，您的 VPN 或 ExpressRoute 連線可能未正確設定。

如果 VNet 裝載的 VM 可以連接您的內部部署系統，但您的應用程式無法連接此系統，則很可能是下列其中一個原因：

* 您的路由不會設定您的子網路，或指向您的內部部署閘道中的站台位址範圍
* 網路安全性群組將封鎖點對站 IP 範圍的存取
* 內部部署防火牆將封鎖來自點對站 IP 範圍的流量
* 您嘗試連線到使用區域的 VNet 整合功能的非 RFC 1918 位址


## <a name="powershell-automation"></a>PowerShell 自動化

您可以使用 PowerShell 將 App Service 與 Azure 虛擬網路整合。 如需隨時可執行的指令碼，請參閱[將 Azure App Service 中的應用程式連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) \(英文\)。


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
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
