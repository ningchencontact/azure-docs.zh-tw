---
title: 保護 Azure 運算架構
description: 企業層級 DMZ 架構的此參考架構會使用網路虛擬裝置和其他工具。 此架構的設計是為了符合國防部的安全雲端運算架構功能需求。 它也可以用於任何組織。 此參考包含兩個使用 Citrix 或 F5 設備的自動化選項。
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 3a27eac3d4609f1054b0ef6a9417fe2f1ca53ae4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656637"
---
# <a name="secure-azure-computing-architecture"></a>保護 Azure 運算架構

美國將工作負載部署至 Azure 的國防部 (DoD) 客戶, 已要求設定安全虛擬網路的指引, 並設定由 DoD 標準和實務約定的安全性工具和服務。 

國防資訊系統機構 (DISA) 已在2017中發佈[安全的雲端運算架構 (SCCA) 功能需求檔 (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) 。 SCCA 說明保護國防資訊系統網路 (DISN) 和商業雲端提供者連接點的功能目標。 SCCA 也會說明「任務擁有者」如何在連線界限保護雲端應用程式。 連接到商業雲端的每個 DoD 實體都必須遵循 SCCA FRD 中所述的指導方針。
 
SCCA 有四個元件:
 
- 界限雲端存取點 (BCAP)
- 虛擬資料中心安全性堆疊 (VDSS)
- 虛擬資料中心受控服務 (VDMS)
- 受信任的雲端認證管理員 (TCCM) 

Microsoft 開發了一項解決方案, 符合 Azure 中執行的 IL4 和 IL5 工作負載的 SCCA 需求。 此 Azure 特有的解決方案稱為安全的 Azure 運算架構 (SACA)。 部署 SACA 的客戶符合 SCCA FRD。 他們可以讓 DoD 客戶在連線後, 將工作負載移至 Azure。

SCCA 指引和架構是 DoD 客戶特有的, 但是 SACA 的最新修訂可協助文職客戶遵守信任的網際網路連線 (TIC) 指引。 最新的修訂也會協助商業客戶, 想要執行安全的 DMZ 來保護其 Azure 環境。


## <a name="secure-cloud-computing-architecture-components"></a>保護雲端運算架構元件

### <a name="bcap"></a>BCAP

BCAP 的目的是要保護 DISN 免于源自雲端環境的攻擊。 BCAP 會執行入侵偵測及預防。 它也會篩選出未經授權的流量。 此元件可以與 SCCA 的其他元件共置。 我們建議您使用實體硬體來部署此元件。 下表列出 BCAP 的安全性需求。

#### <a name="bcap-security-requirements"></a>BCAP 安全性需求

![BCAP 需求矩陣](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

VDSS 的目的是要保護裝載于 Azure 中的 DoD 任務擁有者應用程式。 VDSS 會在 SCCA 中執行大量安全性作業。 它會進行流量檢查, 以保護在 Azure 中執行的應用程式。 此元件可在您的 Azure 環境中提供。

#### <a name="vdss-security-requirements"></a>VDSS 安全性需求

![VDSS 需求矩陣](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

VDMS 的目的是要提供主機安全性和共用資料中心服務。 VDMS 的功能可以在您 SCCA 的中樞內執行, 或讓任務擁有者可以在自己特定的 Azure 訂用帳戶中部署部分。 此元件可在您的 Azure 環境中提供。

#### <a name="vdms-security-requirements"></a>VDMS 安全性需求

![VDMS 需求矩陣](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM 是一種商務角色。 這個人負責管理 SCCA。 其職責如下: 

- 為帳戶和原則建立雲端環境的存取權。 
- 確定身分識別和存取管理運作正常。 
- 維護雲端認證管理方案。 

這個人是由授權官方所授與。 BCAP、VDSS 和 VDMS 提供 TCCM 執行其工作所需的功能。

#### <a name="tccm-security-requirements"></a>TCCM 安全性需求

![TCCM 需求矩陣](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA 元件和規劃考慮 

SACA 參考架構是設計用來在 Azure 中部署 VDSS 和 VDMS 元件, 以及啟用 TCCM。 此架構為模組化。 VDSS 和 VDMS 的所有部分都可以存留在集中式中樞內。 某些控制項可以在任務擁有者空間或甚至內部部署中符合。 Microsoft 建議您將 VDSS 和 VDMS 元件共置到所有任務擁有者都可以透過連線的中央虛擬網路。 下圖顯示此架構: 


![SACA 參考架構圖表](media/sacav2generic.png)

當您規劃 SCCA 合規性策略和技術架構時, 請考慮從一開始的下列主題, 因為它們會影響每個客戶。 下列問題與 DoD 客戶有關, 通常會讓規劃和執行變慢。 

#### <a name="which-bcap-will-your-organization-use"></a>您的組織會使用哪些 BCAP？
   - DISA BCAP:
        - DISA 有兩個操作 BCAPs, 位於五個正則, 而在 Camp Roberts, CA。 第三個計畫很快就會上線。 
        - DISA 的 BCAPs 全都有 azure ExpressRoute 線路到 Azure, 可供 DoD 客戶用來連線。 
        - DISA 具有企業層級的 Microsoft 對等互連會話, 適用于想要訂閱 Microsoft 軟體即服務 (SaaS) 工具 (例如 Office 365) 的 DoD 客戶。 藉由使用 DISA BCAP, 您可以啟用 SACA 實例的連線和對等互連。 
    - 建立您自己的 BCAP:
        - 此選項會要求您在共置的資料中心內租用空間, 並設定 Azure 的 ExpressRoute 線路。 
        - 此選項需要額外的核准。 
        - 因為有額外的核准和實體的組建, 所以這個選項會花費最多時間。 
    - 建議您使用 DISA BCAP。 此選項目前可供使用, 具有內建的冗余, 而且目前在生產環境中有客戶在其上運作。
- DoD 可路由 IP 空間:
    - 您必須在邊緣使用 DoD 可路由 IP 空間。 您可以使用 NAT 將這些空間連線到 Azure 中的私人 IP 空間。
    - 請聯絡 DoD 網路資訊中心 (NIC) 以取得 IP 空間。 您需要它做為系統/網路核准程式 (貼齊) 與 DISA 提交的一部分。 
    - 如果您打算使用 NAT 來連線 Azure 中的私人位址空間, 您需要從 NIC 針對您打算部署 SACA 的每個區域, 至少指派一個/24 子網的位址空間。
- 備援：
    - 將 SACA 實例部署到至少兩個區域。 在 DoD 雲端中, 您可以將其部署至兩個可用的 DoD 區域。
    - 透過個別的 ExpressRoute 線路連接到至少兩個 BCAPs。 然後, 這兩個 ExpressRoute 連線都可以連結到每個區域的 SACA 實例。 
- DoD 元件特定需求:
    - 您的組織是否有任何 SCCA 需求以外的特定需求？ 有些組織有特定的 IP 需求。
- SACA 是模組化架構:
    - 只使用您的環境所需的元件。 
        - 在單一層或多層中部署網路虛擬裝置。
        - 使用整合式 IP 或攜帶您自己的 IP。
- 應用程式和資料的 DoD 影響層級:
    - 如果有任何可能的應用程式在 Microsoft IL5 區域中執行, 請在 IL5 中建立 SACA 實例。 實例可以在 IL4 應用程式和 IL5 之前使用。 IL5 應用程式前方的 IL4 SACA 實例很可能不會收到認證。

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>您將使用哪個網路虛擬裝置廠商來進行 VDSS？
如先前所述, 您可以使用各種不同的應用裝置和 Azure 服務來建立此 SACA 參考。 Microsoft 已自動化解決方案範本, 可使用 F5 和 Citrix 部署 SACA 架構。 這些解決方案會在下一節中討論。

#### <a name="which-azure-services-will-you-use"></a>您將使用哪些 Azure 服務？
- 有一些 Azure 服務可符合 log analytics、主機型保護和識別碼功能的需求。 有些服務可能未在 Microsoft IL5 區域中正式運作。 在此情況下, 如果這些 Azure 服務無法符合您的需求, 您可能需要使用協力廠商工具。 請查看您所熟悉的工具, 以及使用 Azure 原生工具的可行性。
- 我們建議您盡可能使用最多的 Azure 原生工具。 它們是以雲端安全性為考慮, 並與 Azure 平臺的其他部分緊密整合。 使用下列清單中的 Azure 原生工具, 以符合 SCCA 的各種需求:

    - [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure 網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure 防火牆](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure Front](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure 安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS 保護](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- 調整大小
    - 調整大小練習必須完成。 查看您可能會透過 SACA 實例和網路輸送量需求的並行連線數目。 
    - 這個步驟很重要。 它有助於調整 Vm 的大小, 並識別您在 SACA 實例中使用的各種廠商所需的授權。 
    - 如果沒有這個調整大小的練習, 就無法進行理想的成本分析。 正確的調整大小也可達到最佳效能。 


## <a name="most-common-deployment-scenario"></a>最常見的部署案例

 有數個 Microsoft 客戶已完成完整部署, 或至少有 SACA 環境的規劃階段。 他們的經驗顯示了最常見部署案例的深入解析。 下圖顯示最常見的架構: 


![SACA 參考架構圖表](media/sacav2commonscenario.png) 


如您在圖表中所見, DoD 客戶通常會訂閱兩個 DISA BCAPs。 其中一個是位於 West Coast, 另一個則位於東部海岸。 Azure 會在每個 DISA BCAP 位置啟用 ExpressRoute 私用對等。 這些 ExpressRoute 對等會接著連結至 DoD 東部和 DoD Central Azure 區域中的虛擬網路閘道。 SACA 實例會部署在 DoD 東部和 DoD Central Azure 區域中。 所有的輸入和輸出流量會流經其進出 DISA BCAP 的 ExpressRoute 連線。

然後, 任務擁有者應用程式會選擇他們打算在其中部署其應用程式的 Azure 區域。 他們會使用虛擬網路對等互連, 將其應用程式的虛擬網路連線到 SACA 虛擬網路。 然後, 它們會透過 VDSS 實例強制通道所有流量。

我們建議此架構, 因為它符合 SCCA 需求。 其高度可用且可輕鬆擴充。 它也簡化了部署和管理。

## <a name="automated-saca-deployment-options"></a>自動化 SACA 部署選項

 如先前所述, Microsoft 已與兩個廠商合作, 以建立自動化的 SACA 基礎結構範本。 這兩個範本都會部署下列 Azure 元件: 

- SACA 虛擬網路
    - 管理子網
        - 此子網是管理 Vm 和服務的部署位置, 也稱為跳躍方塊。
        - VDMS 子網
            - 此子網是用於部署用於 VDMS 之 Vm 和服務的位置。
        - 不受信任和受信任的子網
            - 這些子網是虛擬裝置部署所在的位置。
        - 閘道子網路
            - 此子網是 ExpressRoute 閘道部署所在的位置。
- 管理跳躍箱虛擬機器
    - 它們是用於環境的頻外管理。
- 網路虛擬設備
    - 您可以根據部署的範本使用 Citrix 或 F5。
- 公用 IP
    - 它們是用於前端, 直到 ExpressRoute 上線為止。 這些 Ip 會轉譯為後端 Azure 私人位址空間。
- 路由表 
    - 適用于自動化期間, 這些路由表會強制通道所有流量通過虛擬應用裝置。
- Azure 負載平衡器-標準 SKU
    - 它們是用來對設備之間的流量進行負載平衡。
- 網路安全性群組
    - 它們是用來控制哪些類型的流量可以跨越特定端點。


### <a name="citrix-saca-deployment"></a>Citrix SACA 部署

Citrix 部署範本會部署兩層高可用性的 Citrix ADC 應用裝置。 此架構符合 VDSS 的需求。 

![Citrix SACA 圖表](media/citrixsaca.png)


如需 Citrix 檔和部署腳本, 請參閱[此 GitHub 連結](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)。


 ### <a name="f5-saca-deployment"></a>F5 SACA 部署

兩個個別的 F5 部署範本涵蓋兩個不同的架構。 在主動-主動高可用性設定中, 第一個範本只有一層 F5 設備。 此架構符合 VDSS 的需求。 第二個範本會新增主動-主動高可用性 F5s 的第二層。 第二層可讓客戶在 F5 層之間新增自己的 IP, 並將其不同于 F5。 並非所有 DoD 元件都有指定要使用的特定 IP。 如果是這種情況, 則最多會有一層 F5 設備的作用, 因為該架構包含 F5 裝置上的 IP。

![F5 SACA 圖](media/f5saca.png)

如需 F5 檔和部署腳本, 請參閱[此 GitHub 連結](https://github.com/f5devcentral/f5-azure-saca)。












