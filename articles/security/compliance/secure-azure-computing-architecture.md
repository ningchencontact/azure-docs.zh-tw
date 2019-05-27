---
title: 保護 Azure 運算架構
description: 此參考架構的企業層級 DMZ 架構會使用網路虛擬設備和其他工具。 此架構被設計成符合國防部的安全雲端運算架構功能需求。 也可以使用適用於各種組織。 此參考包括使用 Citrix 或 F5 應用裝置的兩個自動化的選項。
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963233"
---
# <a name="secure-azure-computing-architecture"></a>保護 Azure 運算架構

美國將工作負載部署到 Azure 的美國國防部 (DoD) 客戶要求指導方針來設定安全的虛擬網路和設定的安全性工具和 DoD 標準與作法約定的服務。 

防禦資訊系統機關 (DISA) 發行[安全雲端運算架構 」 (SCCA) 功能的需求文件 (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017。 SCCA 描述保護防禦資訊系統的網路 (DISN) 和商業雲端提供者連接點的功能目標。 SCCA 也會說明如何安全的任務擁有者雲端應用程式在連接界限。 連接到商業雲端的每個 DoD 實體必須遵循 SCCA FRD 中設立的指導方針。
 
SCCA 有四個元件：
 
- 界限雲端存取點 (BCAP)
- 虛擬資料中心安全性堆疊 (VDSS)
- 虛擬資料中心管理服務 (VDM)
- 受信任的雲端認證管理員 (TCCM) 

Microsoft 已開發符合 IL4 和 IL5 在 Azure 中執行的工作負載的 SCCA 需求的解決方案。 此 Azure 特定解決方案稱為安全 Azure 運算架構 （saca 取得）。 如果客戶部署 saca 取得都符合 SCCA FRD。 他們可以啟用 DoD 客戶將工作負載移至 Azure 之後連線。

SCCA 指導和架構是以 DoD 客戶，但最新的修訂 saca 取得說明文職客戶符合受信任的網際網路連線 （井字） 指導方針。 最新版本也會協助商業客戶想要實作安全的 DMZ，以保護他們的 Azure 環境。


## <a name="secure-cloud-computing-architecture-components"></a>安全的雲端運算架構元件

### <a name="bcap"></a>BCAP

BCAP 的目的是為了防止攻擊是源自於雲端環境中的 DISN。 BCAP 執行入侵偵測與防護。 它也會篩選出未經授權的流量。 此元件可以是共 SCCA 的其他元件。 我們建議您在使用實體硬體來部署這個元件。 下表詳列 BCAP 安全性需求。

#### <a name="bcap-security-requirements"></a>BCAP 安全性需求

![BCAP 需求矩陣](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

VDSS 的目的是保護裝載於 Azure DoD 任務擁有者應用程式。 VDSS SCCA 以執行大量的安全性作業的作業。 它會進行流量檢查，來保護在 Azure 中執行的應用程式。 此元件可供您的 Azure 環境內。

#### <a name="vdss-security-requirements"></a>VDSS 安全性需求

![VDSS 需求矩陣](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

VDM 的目的是要提供主機安全性，並共用資料中心服務。 VDM 的函式可以執行您 SCCA 中樞中，或任務的擁有者可以部署它自己特定的 Azure 訂用帳戶中的項目。 此元件可供您的 Azure 環境內。

#### <a name="vdms-security-requirements"></a>VDM 安全性需求

![VDM 需求矩陣](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM 是商務角色。 此個人負責管理 SCCA。 其職責如下： 

- 建立方案和帳戶存取權的雲端環境的原則。 
- 確定 身分識別和存取管理運作正常。 
- 維護雲端認證管理計劃。 

此個人是由授權官方指派。 BCAP、 VDSS 和 VDM 提供 TCCM 來執行其工作所需要的功能。

#### <a name="tccm-security-requirements"></a>TCCM 安全性需求

![TCCM 需求矩陣](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Saca 取得元件和規劃考量 

Saca 取得參考架構被設計來部署 Azure 中的 VDSS 和 VDM 元件，並啟用 TCCM。 此架構是一個模組。 所有 VDSS 和 VDM 部分可以存留在集中式中樞。 某些控制項可以滿足任務擁有者空間或甚至是在內部部署中。 Microsoft 建議您共置於 VDSS 和 VDM 元件到中央所有任務的擁有者可透過連線虛擬網路。 下圖顯示此架構： 


![Saca 取得參考架構圖表](media/sacav2generic.png)

當您規劃您的 SCCA 合規性策略和技術的架構時，請考慮從一開始的下列主題，因為它們會影響每位客戶。 下列問題與 DoD 客戶有出現，而且容易規劃和執行變慢。 

#### <a name="which-bcap-will-your-organization-use"></a>您的組織會使用哪些 BCAP？
   - DISA BCAP:
        - 停用具有兩個操作 BCAPs，在五邊形和營 Roberts，CA。 第三個預計很快就會上線。 
        - 停用的 BCAPs 都有 Azure ExpressRoute 線路移至 Azure，可供 DoD 客戶連線。 
        - 停用具有想要為服務 (SaaS) 工具，例如 Office 365 訂閱的 Microsoft 軟體的 DoD 客戶的企業層級 Microsoft 對等互連工作階段。 藉由使用 DISA BCAP，您可以啟用連線能力和對等互連至 saca 取得執行個體。 
    - 建置您自己 BCAP:
        - 此選項需要您租用在共置資料中心內的空間，並設定 azure ExpressRoute 線路。 
        - 此選項需要另外核准。 
        - 因為額外的核准和實體的組建外，此選項會佔用大部分的時間。 
    - 我們建議您使用 DISA BCAP。 此選項是隨手可得，內建備援，並具有對其執行立即在生產環境中的客戶。
- DoD 可路由 IP 空間：
    - 您必須使用您的邊緣 DoD 可路由 IP 空間。 使用這些空間連接至在 Azure 中的私人 IP 空間使用 NAT 的選項。
    - 請連絡 DoD 網路資訊安全中心 (NIC) 來取得 IP 空間。 您需要為您的系統/網路核准程序 （嵌入式管理單元） 提交，以停用的一部分。 
    - 如果您打算使用 NAT 連接在 Azure 中的私人位址空間，您需要最少的/24 子網路從您打算部署 saca 取得每個區域的 NIC 指派的位址空間。
- 備援：
    - 將 saca 取得執行個體部署到至少兩個區域中。 在美國國防部雲端中，將它部署到這兩個可用的 DoD 區域。
    - 連接到至少兩個 BCAPs 透過個別的 ExpressRoute 線路。 這兩個 ExpressRoute 連線然後可以連結至每個區域 saca 取得執行個體。 
- DoD 元件特定的需求：
    - 貴組織是否有任何特定的需求外 SCCA 需求？ 某些組織有特定的 IP 需求。
- SACA 是模組化架構：
    - 使用只將為您的環境所需的元件。 
        - 部署中單層或多層的網路虛擬設備。
        - 使用整合式的 IP 」 或 「 攜帶您自己的 IP。
- DoD 影響等級的應用程式和資料：
    - 如果沒有任何可能會在 Microsoft IL5 區域中執行的應用程式，建置在 IL5 saca 取得執行個體。 IL4 應用程式和 IL5 前面，就可以使用的執行個體。 IL4 saca 取得執行個體前面 IL5 應用程式最有可能不會收到資格鑑定證明。

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>您將使用哪一個網路虛擬設備廠商的 VDSS？
如先前所述，您可以使用各種不同的設備和 Azure 服務來建置此 saca 取得參考。 Microsoft 有自動化部署 F5 及 Citrix saca 取得架構的解決方案範本。 下一節會說明這些解決方案。

#### <a name="which-azure-services-will-you-use"></a>您將使用哪些 Azure 服務？
- 有 Azure 服務可以符合需求的 log analytics、 主機型保護和識別碼的功能。 就某些服務在 Microsoft IL5 區域不是正式推出。 在此情況下，您可能需要使用協力廠商工具，如果這些 Azure 服務無法滿足您的需求。 看看您已經熟悉的工具和使用 Azure 的原生工具的可行性。
- 我們建議您盡可能使用多個 Azure 的原生工具。 他們建置的雲端安全性考量事項，並與 Azure 平台的其餘部分密切整合。 下列清單中使用 Azure 的原生工具，以因應各種需求 SCCA:

    - [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure 網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure 防火牆](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure 的大門](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure 安全性的群組](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- 調整大小
    - 必須完成調整大小練習。 看看您可能必須透過 saca 取得執行個體和網路輸送量需求的並行連線數目。 
    - 此步驟最重要的。 它可以協助來調整 Vm 大小，並找出需要從 saca 取得執行個體中您使用各種不同廠商的授權。 
    - 沒有此調整大小練習，無法執行良好的成本分析。 正確的大小也可讓以達到最佳效能。 


## <a name="most-common-deployment-scenario"></a>最常見的部署案例

 有數個 Microsoft 客戶已經透過完整的部署，或在最低 saca 取得環境的規劃階段。 其體驗會揭露深入了解最常見的部署案例。 下圖顯示的最常見的架構： 


![Saca 取得參考架構圖表](media/sacav2commonscenario.png) 


您可以從圖表中看到，DoD 客戶通常訂閱 DISA BCAPs 其中兩個。 其中一個位於美國西岸和其他生活美國東岸。 Azure 在每個 DISA BCAP 位置啟用 ExpressRoute 私人對等。 這些 ExpressRoute 對等連結到 DoD 東部和 DoD 中部 Azure 區域中的虛擬網路閘道。 Saca 取得執行個體部署在 DoD 東部和 DoD 中部 Azure 區域。 所有的輸入和輸出流量流經它與 DISA BCAP 的 ExpressRoute 連線。

擁有者的關鍵應用程式然後選擇 學生打算部署應用程式的 Azure 區域。 他們使用其應用程式的虛擬網路連線到 saca 取得虛擬網路對等互連的虛擬網路。 這些強制通道及其透過 VDSS 執行個體的所有流量。

我們會建議這種架構，因為它符合 SCCA 需求。 它是高度可用且可輕鬆擴充。 此外，它也會簡化部署和管理。

## <a name="automated-saca-deployment-options"></a>自動化 saca 取得部署選項

 如先前所述，Microsoft 已與要建立自動化的 saca 取得基礎結構範本的兩個供應商合作。 這兩個範本會部署下列 Azure 元件： 

- Saca 取得虛擬網路
    - 管理子網路
        - 這個子網路是以何處管理 Vm 和服務部署，也就是跳躍方塊。
        - VDM 子網路
            - 這個子網路是部署 Vm 和服務用於 VDM 的位置。
        - 未受信任及受信任的子網路
            - 這些子網路是虛擬設備部署的位置。
        - 閘道子網路
            - 這個子網路是 ExpressRoute 閘道的部署位置。
- 管理跳躍方塊虛擬機器
    - 其用途-頻外管理的環境。
- 網路虛擬設備
    - 使用任一 Citrix 或 f5 鍵以在您部署在哪一個範本。
- 公用 IP
    - ExpressRoute 上線之前，它們用於後端。 這些 Ip 會轉譯為後端 Azure 私人位址空間。
- 路由表 
    - 套用過程自動化，這些資料表強制通道將所有流量都路由透過虛擬設備。
- Azure 負載平衡器-標準 SKU
    - 它們用來應用裝置間的負載平衡流量。
- 網路安全性群組
    - 會使用它們來控制哪些類型的流量可以周遊到特定端點。


### <a name="citrix-saca-deployment"></a>Citrix saca 取得部署

Citrix 部署範本會部署兩層的高可用性的 Citrix ADC 設備。 這種架構符合 VDSS 的需求。 

![Citrix saca 取得圖表](media/citrixsaca.png)


針對 Citrix 文件和部署指令碼，請參閱[此 GitHub 連結](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)。


 ### <a name="f5-saca-deployment"></a>F5 saca 取得部署

兩個不同的 F5 部署範本涵蓋兩個不同的架構。 第一個範本有只有一個圖層的 F5 設備在主動-主動高可用性組態。 這種架構符合 VDSS 的需求。 第二個範本中新增第二個主動-主動高可用性 F5s 層。 第二層可讓客戶將自己從 F5 F5 層之間不同的 IP。 並非所有的 DoD 元件具有特定 IP 的使用規定。 如果這種情況，因為該架構包含 F5 裝置上的 IP F5 設備單一層適用於大部分。

![F5 saca 取得圖表](media/f5saca.png)

F5 文件和部署指令碼，請參閱[此 GitHub 連結](https://github.com/f5devcentral/f5-azure-saca)。












