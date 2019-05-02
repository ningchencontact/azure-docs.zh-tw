---
title: 保護 Azure 運算架構
description: 這是使用網路虛擬設備和其他工具的企業層級 DMZ 架構的參考架構。 此架構被設計成符合國防部的安全雲端運算架構功能需求。 不過，您可以運用適用於各種組織。 此參考會包含兩個使用 Citrix 或 F5 設備的自動化的選項。
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917594"
---
# <a name="secure-azure-computing-architecture"></a>保護 Azure 運算架構

一個快速遞增的數字，將工作負載部署至 Azure 的 DoD 客戶一直要求的安全虛擬網路安裝和設定的安全性工具和服務約定 DoD 標準與作法的指引。 發行的 DISA[功能性需求文件的安全雲端運算架構 」 (SCCA)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017。 SCCA 描述 Defense 資訊系統保護網路安全的 (DISN) 的功能目標與商業雲端提供者連接點，和如何任務的擁有者在連接界限的安全雲端應用程式。 託管，連接到商業雲端的每個 DoD 實體如下所示 SCCA FRD 中設立的指導方針。
 
有四個元件的 SCCA。 界限雲端存取點 (BCAP) 」、 「 虛擬資料中心安全性堆疊 (VDSS)、 「 虛擬資料中心管理的服務 (VDM)，並受信任的雲端認證管理員 (TCCM)。 Microsoft 已開發會符合 IL4 和 IL5 在 Azure 中執行的工作負載的 SCCA 需求的解決方案。 此 Azure 的特定解決方案稱為安全 Azure 運算架構 （saca 取得）。 如果客戶部署 SACA 會符合 SCCA FRD，而且可讓 DoD 客戶將工作負載移至 Azure 之後連線。 

文職客戶遵循受信任的網際網路連線 （井字） 指導方針，以及想要實作 DMZ，以安全的商業客戶，SCCA 指導和架構特有 DoD 客戶時，將能協助 saca 取得最新修訂保護他們的 azure 環境。


## <a name="secure-cloud-computing-architecture-components"></a>安全的雲端運算架構元件

**BCAP**

BCAP 的目的是為了防止攻擊源自於雲端環境中的 DISN。 BCAP 會執行入侵偵測與防護，以及篩選出未經授權的流量。 此元件可以是共 SCCA 的其他元件。 強烈建議此元件會使用實體硬體部署。 您將在下面找到 BCAP 安全性需求的清單。

***BCAP 安全性需求***

![BCAP 需求矩陣](media/bcapreqs.png)


**VDSS**

VDSS 的目的是保護裝載於 Azure DoD 任務的擁有者應用程式。 VDSS SCCA 以執行大量的安全性作業的作業。 為了保護 Azure 中執行的應用程式，它會進行流量檢查。 此元件可供您的 Azure 環境內。

***VDSS 安全性需求***

![VDSS 需求矩陣](media/vdssreqs.png)

**VDMS**

VDM 的目的是要提供主機安全性，以及共用資料中心服務。 VDM 的函式可以執行您 SCCA 中樞中，或任務的擁有者可以部署它自己特定的 Azure 訂用帳戶中的項目。 此元件可供您的 Azure 環境內。

***VDM 安全性需求***

![VDM 需求矩陣](media/vdmsreqs.png)


**TCCM**

TCCM 是商務角色。 此人員會負責管理 SCCA。 其職責包括建立計劃和雲端環境，確保身分識別的帳戶存取權的原則和存取管理運作正常，並維護雲端認證管理計畫。 此個人是由授權官方指派。 BCAP、 VDSS 和 VDM 會提供執行其作業職責 TCCM 所需要的功能。

***TCCM 安全性需求***

![TCCM 需求矩陣](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Saca 取得元件和規劃考量 

Saca 取得參考架構被設計來部署 VDSS 和 VDM azure 中的元件，以及啟用 TCCM。 此架構已模組化，這表示所有 VDSS 和 VDM 部分可以存留在集中式中樞，或全部的控制項可以滿足任務的擁有者空間或甚至是在內部部署中。 我們的 Microsoft 小組的建議是中央虛擬網路所有的任務擁有者可透過連線到共置的 VDSS 和 VDM 元件。 下圖說明我們的建議的架構。 


![Saca 取得參考架構圖表](media/sacav2generic.png)

規劃您 SCCA 合規性策略和技術的架構時，有許多需要考量事項。 很重要的下列主題會納入考量，從一開始，因為每個客戶必須涵蓋這些。 下列主題已與實際的 DoD 客戶有出現，而且通常會變慢的計劃和執行的問題。 

- 您的組織會使用哪些 BCAP？
    - DISA BCAP
        - 停用具有兩個操作 BCAPs 五邊形，以及研習營 Roberts CA，第三個即將推出。 
        - 停用的 BCAPs 所有具有至 Azure，可以利用 DoD 客戶連線的 ExpressRoute 線路。 
        - 停用已經具有想要 Microsoft SaaS 的工具，例如 Office 365 訂閱的 DoD 客戶的企業層級的 Microsoft 對等互連工作階段。 藉由使用 DISA BCAP，您可以啟用連線能力和對等互連至 saca 取得執行個體。 
    - 建置您自己 BCAP
        - 這會需要您租用在共置資料中心內的空間，並設定 azure ExpressRoute 線路。 
        - 這個選項將需要額外核准 
        - 由於額外的核准和實體的組建時，此選項會佔用大部分的時間。 
    - Microsoft 的建議是利用 DISA BCAP。 這個選項是隨手可得，具有內建的備援，並已作用於其立即在生產環境中的客戶。
- DoD 可路由 IP 空間
    - 您必須在您的邊緣使用 DoD 可路由 IP 空間。 NAT 的選項到在 Azure 中的私人 IP 空間使用。  
    - 請連絡 DoD NIC 來取得 IP 空間，它將需要為您的嵌入式管理單元提交，以停用的一部分。 
    - 如果您計劃 NAT 至在 Azure 中的私人位址空間，您需要最少的/24 子網路從您打算部署 saca 取得每個區域的 NIC 指派的位址空間。 
- 備援性 
    - Microsoft 建議您將 saca 取得執行個體部署到至少兩個區域。 在美國國防部雲端中，這可能表示您將它部署到這兩個可用的 DoD 區域。 
    - 此外，也建議您連接到至少兩個 BCAPs 透過個別的 ExpressRoute 線路。 這兩個 Express Route 接著可以連結至每個區域 saca 取得執行個體。 
- DoD 元件特定的需求
    - 貴組織是否有任何特定的需求外 SCCA 需求？ （有些組織會有特定的 IP 需求）
- SACA 是模組化架構  
    - 使用您的環境需要只是哪些元件。 
        - 單層或多層式部署 Nva
        - 整合式的 IP，或攜帶您自己的 IP
- DoD 影響等級的應用程式和資料
    - 如果沒有任何可能會在我們 IL5 區域中執行的應用程式，建議您建置在 IL5 saca 取得執行個體。 IL4 應用程式，以及 IL5 前面，就可以使用的執行個體。 但是，IL4 saca 取得執行個體前面 IL5 應用程式不會很有可能收到資格鑑定證明。 
- 您將使用哪些網路虛擬設備廠商的 VDSS？
    - 如先前所述，就可以使用各種設備和 Azure 服務來建置此 saca 取得參考。 不過，我們沒有自動化的解決方案範本來部署使用 F5 及 Citrix saca 取得架構。 這些方案會涵蓋詳細說明如下。 
- 您將使用哪些 Azure 服務？
    - 有可以滿足需求，log analytics、 主機型保護，以及識別碼功能的 Azure 服務。 不過，就可以在我們 IL5 各地，某些服務不正式推出。 這可能會導致使用一些第 3 方工具，如果這些 Azure 服務不能符合您需求的需要。 您必須查看有哪些工具已熟悉及使用 Azure 的原生工具的可行性。 
    - 它是 Microsoft 的建議，您會使用多個 Azure 原生工具所建置的雲端安全性考量事項，並順暢地整合與 Azure 平台的其餘部分的可能。 以下是 Azure 的原生工具，可用來滿足各種需求 SCCA 的清單。 
        - [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [應用程式閘道](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Azure 防火牆](https://docs.microsoft.com/azure/firewall/overview) 
        - [Azure 的大門](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- 調整大小
    - 調整大小練習必須完成。 您必須看看您可能必須透過 saca 取得執行個體，以及網路輸送量需求的並行連線數目。 
    - 這會是重要的步驟，因為它會協助調整大小的 Vm，以及協助找出需要從 saca 取得執行個體中將使用不同廠商的授權。 
    - 無法完成理想的成本分析而不需要此調整大小練習，也很重要，可確保所有項目為了達到最佳效能，以便正確大小。 


## <a name="most-common-deployment-scenario"></a>最常見的部署案例

Microsoft 有幾個已經通過完整的客戶部署，或至少規劃其 saca 取得環境的階段。 這讓我們能夠取得深入了解最常見的部署案例。 下圖說明最常見的架構。 


![Saca 取得參考架構圖表](media/sacav2commonscenario.png) 


您可以從圖表中看到，DoD 客戶通常訂閱兩個 DISA BCAPs，其中這些存在於美國西岸和美國東岸上其他的生活。 Azure 在每個 DISA BCAP 位置啟用 ExpressRoute 私人對等。 這些 ExpressRoute 對等接著連結至虛擬網路閘道的 DoD 東部和 DoD 中部 Azure 區域中。 Saca 取得執行個體部署在 DoD 東部和 DoD 中部 Azure 區域和所有輸入和輸出流量透過它與 DISA BCAP 的 Express Route 連線。 

關鍵任務應用程式，然後選擇哪個 Azure 地區，他們將會部署在其應用程式的擁有者和使用虛擬網路對等互連，讓其應用程式連接的虛擬網路至 saca 取得虛擬網路。 強制通道及其透過 VDSS 執行個體的所有流量。 

強烈，由 Microsoft 來建議此架構，因為它將會符合 SCCA 需求，它是高度可用、 輕鬆地擴充，它可簡化部署和管理。

## <a name="automated-saca-deployment-options"></a>自動化 saca 取得部署選項

 先前我們提過 Microsoft 已與要建立自動化的 saca 取得基礎結構範本的兩個供應商合作。 這兩個範本會部署下列 Azure 元件。 

- Saca 取得虛擬網路
    - 管理子網路
        - 管理 Vm 和服務的部署位置 （跳躍）
        - VDM 子網路
            - 部署 Vm 和服務用於 VDM
        - 未受信任及受信任的子網路 
            - 虛擬應用裝置的部署位置
        - 閘道子網路
            - ExpressRoute 閘道的部署位置
- 管理跳躍方塊虛擬機器
    - 用於超出訊號範圍管理的環境。
- 網路虛擬設備
    - Citrix 或 f5 鍵，在您部署根據的範本。
- 公用 IP
    - ExpressRoute 上線之前，請使用後端。 這些 Ip 會轉譯為後端 Azure 私人位址空間
- 路由表 
    - 套用過程自動化，這些路由資料表強制通道的所有流量透過虛擬設備
- Azure 負載平衡器-標準 SKU
    - 這些用來應用裝置間的負載平衡的流量
- 網路安全性群組
    - 用來控制哪些類型的流量可以周遊到特定端點


**Citrix saca 取得部署**

Citrix 已建立部署範本會部署兩層的高可用性的 Citrix ADC 設備。 這種架構符合 VDSS 的需求。 

![Citrix saca 取得圖表](media/citrixsaca.png)


您可以找到 Citrix 文件和部署指令碼[這裡。](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **F5 saca 取得部署**

F5 已建立兩個不同的部署範本涵蓋兩個不同的架構。 第一個有只有一個圖層的 F5 設備在主動-主動高可用性組態。 這種架構符合 VDSS 的需求。 第二個新增主動-主動高可用性 F5s 的第二的層。 此第二個圖層的目的是允許客戶將自己從 F5 F5 層之間不同的 IP。 並非所有的 DoD 元件具有特定 IP 的使用規定。 如果是這樣，F5 應用裝置的單一層適用於最自架構包含 F5 裝置的 IP。  

![Citrix saca 取得圖表](media/f5saca.png)

您可以找到 F5 文件和部署指令碼[這裡。](https://github.com/f5devcentral/f5-azure-saca) 












