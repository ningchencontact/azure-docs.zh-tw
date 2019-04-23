---
title: Azure DevTest labs 的參考架構，適用於企業
description: 這篇文章會提供參考架構指南適用於 Azure DevTest Labs 在企業中。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: bcb154f7cffb92ef23fc2606e1f604bb12f8d1a3
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996606"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Azure DevTest Labs-適用於企業的參考架構
這篇文章提供參考架構部署在企業中的 Azure DevTest Labs 為基礎的解決方案。 它包含透過 Express Route，若要從遠端登入虛擬機器的遠端桌面閘道、 連線到私用成品和其他 PaaS 服務，在實驗室中使用的成品存放庫的內部部署連線能力。

![參考架構](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>架構
參考架構中的重要元素如下：

- **Azure Active Directory (AAD)**:使用 azure DevTest Labs[身分識別管理的 Azure Active Directory 服務](../active-directory/fundamentals/active-directory-whatis.md)。 有兩個在提供根據使用者的研發/測試實驗室環境的存取權，要考慮的重要事項：
    - **資源管理**:它提供 Azure 入口網站來管理資源的存取權 （建立虛擬機器、 建立環境、 啟動/停止/重新啟動/刪除/applyartifacts，等等）。 它是在 Azure 中使用 Roble 型存取控制 (RBAC)，並套用使用者、 設定資源，以及存取層級權限的角色指派。
    - **虛擬機器 （網路層級）**:在預設組態中，虛擬機器會使用本機系統管理員帳戶。  如果沒有可用的網域 ([AAD 網域服務](../active-directory-domain-services/active-directory-ds-overview.md)，是在內部部署網域或雲端架構的網域)，機器可以加入至網域。 一旦加入，使用者就會使用其網域為基礎的身分識別來連線到 Vm。
- **在內部部署連線能力**:架構在上圖中， [Express Route](../expressroute/expressroute-introduction.md) i 使用，但您也可以使用[站台對站 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 雖然它不需要適用於 DevTest Labs，它通常會出現在企業。 您只需要是否連線到公司資源的原因。 常見的原因如下： 
    - 無法尚未移到雲端的內部部署資料
    - 若要將實驗室的虛擬機器加入內部部署網域的喜好設定
    - 強制所有網路流量流入和流出雲端環境透過內部部署防火牆，基於安全性/法務遵循方面的考量
- **網路安全性群組**：雲端環境 （或在雲端環境中） 來限制流量的常見方式是根據來源和目的地 IP 位址是使用[網路安全性群組](../virtual-network/security-overview.md)。 例如，允許僅來自公司網路，到實驗室的網路的網路流量。
- **遠端桌面閘道**:企業通常會封鎖在公司防火牆的連出遠端桌面連線。 若要啟用以雲端為基礎的環境，在 DevTest Labs 中的連線，有數個選項，例如使用[遠端桌面閘道](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)（白名單閘道負載平衡器的靜態 IP） 或[導向所有內送RDP 流量](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)透過 Express Route/站台-對站 VPN 連線。 規劃部署的企業中的研發/測試實驗室時，就是常見的考量。
- **Azure 網路 （Vnet、 子網路）**:[Azure 網路](../networking/networking-overview.md)拓樸是整體的研發/測試實驗室架構中的另一個關鍵要素。 它可讓從實驗室中進行通訊 （或停用），在內部部署環境的存取 （或停用） 和 （或停用） 的網際網路存取的資源。 架構圖包含客戶使用 DevTest Labs 的最常見方式 (透過連線的所有實驗室[VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)使用[中樞支點模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)Express 路由/站台間 VPN 連線為內部部署），但因為 DevTest Labs 會使用 Azure 網路直接沒有任何限制，有關如何設定網路基礎結構。
- **DevTest Labs**:DevTest Labs 是整體架構的重要部分。 若要深入了解此服務，請參閱[解 DevTest Labs](devtest-lab-overview.md)。
- **虛擬機器和其他資源 (SaaS、 PaaS、 IaaS)**:DevTest Labs 支援重要的工作負載的其中一個是虛擬機器，以及其他 Azure 資源。  研發/測試實驗室能夠以簡單且快速的企業提供存取權 （包括虛擬機器和其他 Azure 資源） 的 Azure 資源。  深入了解 Azure 的存取權[開發人員](devtest-lab-developer-lab.md)並[測試人員](devtest-lab-test-env.md)。

## <a name="scalability-considerations"></a>延展性考量
雖然 DevTest Labs 沒有任何內建的配額或限制，但其他 Azure 資源會使用在一般操作的實驗室沒有[訂用帳戶層級配額](../azure-subscription-service-limits.md)。 如此一來，在典型的企業部署中，您必須以涵蓋 DevTest Labs 的大規模部署多個 Azure 訂用帳戶。 最常企業所達到的配額是：

- **資源群組**：在預設組態中，DevTest Labs 會建立每個新的虛擬機器或使用者建立使用服務的環境的資源群組。 訂用帳戶可以包含[980 的資源群組的最大](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)，因此這項限制是上限的虛擬機器和訂用帳戶中的環境。 有兩個您應該考慮的其他組態：
    - **[所有虛擬機器都移至相同的資源群組](resource-group-control.md)**:雖然它可協助您使用的資源群組的限制，它會影響每個資源群組限制的資源類型。
    - **使用共用公用 Ip**:相同的大小和相同區域的所有 Vm 會都進入相同的資源群組。 如果虛擬機器都可以有公用 IP 位址，它就會是 '中庸' 之間的資源群組配額和每個資源群組配額的資源類型。 
- **每個資源的資源群組，每個資源類型**:預設限制[每個資源群組，每個資源類型的資源為 800](../azure-subscription-service-limits.md#resource-group-limits)。  當使用的所有虛擬機器移到相同的資源群組設定時，使用者會叫用此訂用帳戶限制更快，尤其是如果 Vm 有多個額外的磁碟。
- **儲存體帳戶**：在 DevTest Labs 中的對實驗室隨附儲存體帳戶和 Azure 配額[每個訂用帳戶區域的儲存體帳戶數目是 250](../azure-subscription-service-limits.md#storage-limits)。 這表示相同的區域中的 DevTest Labs 的數目上限也為 250。
- **角色指派**:角色指派是如何授與使用者或主體存取的資源 （擁有者、 資源、 權限層級）。 在那里的 Azure 中，[限制為 2000年個角色指派每個訂閱](../azure-subscription-service-limits.md#role-based-access-control-limits)。 DevTest Labs 中的服務 （預設設定） 針對每個 VM 建立資源群組，並將被授與擁有者**擁有者**DevTest Labs VM 的權限並**讀取器**權限資源群組。  如此一來，每個新建立的 VM 會使用兩個的其他角色指派，讓使用者在實驗室的權限時所建立的角色指派。
- **API 的讀取/寫入**:有許多不同的方式 （REST Api、 PowerShell、 CLI、 Azure SDK，等等） 將 Azure 和 DevTest Labs 自動化透過自動化您也可以叫用另一個限制 API 要求。 每個訂用帳戶允許最多[12000 讀取要求和 1200年寫入每小時的要求](../azure-resource-manager/resource-manager-request-limits.md)。  要注意的自動化研發/測試實驗室時，限制是它。

## <a name="manageability-considerations"></a>管理性考量
使用單一實驗室時，DevTest Labs 會有絕佳的系統管理使用者介面。 在企業中，有可能多個 Azure 訂用帳戶和幾個實驗室。 這表示，以一致的方式對所有實驗室的變更需要自動化指令碼。  以下是一些範例和最佳的方式管理研發/測試實驗室部署：

- **實驗室設定的變更**:常見的案例是要更新特定的實驗室設定跨部署中的所有實驗室。 比方說，新的 VM 執行個體大小可同時也必須更新所有實驗室，以允許它。  最好的方式是使用 Azure PowerShell 指令碼、 Azure CLI 或 REST Api，自動執行這些變更。  
- **成品存放庫個人存取權杖**:一般而言，Git 存放庫的個人存取權杖過期 （90 天，1 年，2 yrs）。 若要確保持續性，務必擴充的個人存取權杖，或建立一個新，並使用自動化來套用至所有實驗室的 新增個人存取權杖。
- **限制變更實驗室設定**:它通常是沒有必須是受限制的特定設定 （例如，允許 Marketplace 映像使用）。 也可以在透過 Azure 原則 （防止變更該資源類型） 或建立自訂角色，並授與該角色，而不是 「 擁有者 」 到實驗室。 對大部分的實驗室 （內部支援、 實驗室公告、 允許的 VM 大小等等） 中的設定
- **需要 Vm 遵循命名慣例**:它是常見的要求，更容易找出 Vm 的雲端開發和測試環境的一部分。 您可以這樣[使用 Azure 原則](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)。

請務必注意，DevTest Labs 會使用 （網路、 磁碟、 計算、 等等） 的 Azure 中管理的相同方式與在 Azure 中的基礎資源。  例如，Azure 原則適用於實驗室中建立的虛擬機器。 Azure 資訊安全中心可以在 VM 相容性報告。 Azure 備份服務可以在實驗室中，提供適用於 Vm 的定期備份等等。 

## <a name="security-considerations"></a>安全性考量
Azure DevTest Labs 在 Azure （計算、 網路等等） 中使用現有的資源，並因此自動受益於內建所有絕佳的安全性功能的平台。 例如，若要保護任何連入遠端桌面連線為僅來自公司網路，很簡單，只要將網路安全性群組新增至遠端桌面閘道的虛擬網路。 只能將額外的安全性考量適用於 Azure DevTest Labs 是提供給小組成員將會使用實驗室以每天為基礎的權限等級。  指定的常見權限[「 擁有者 」 和 「 DevTest Labs 使用者 」](devtest-lab-add-devtest-user.md)。 如需有關這些角色的詳細資訊，請參閱 <<c0> [ 在 Azure DevTest Labs 中新增擁有者和使用者](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>後續步驟
請參閱本系列的下一篇文章：[相應增加您 Azure DevTest Labs 的基礎結構](devtest-lab-guidance-scale.md)
