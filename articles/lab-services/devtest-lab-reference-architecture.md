---
title: Azure DevTest Labs 的企業參考架構
description: 本文章提供適用於 Azure DevTest Labs 在企業中的參考架構指引。
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
ms.openlocfilehash: 1bfd1b5b4b7febd98499e338fcb62e339867aef4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244725"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>適用於企業的 azure DevTest Labs 參考架構
本文章提供參考架構，以幫助您部署在企業中基礎 Azure DevTest Labs 的解決方案。 包括下列各項：
- 透過 Azure ExpressRoute 的內部部署連線能力
- 若要從遠端登入虛擬機器的遠端桌面閘道
- 連線到私用成品的成品存放庫
- 在實驗室內使用其他 PaaS 服務

![參考架構圖表](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>架構
這些是參考架構的重要元素：

- **Azure Active Directory (Azure AD)** ：使用 DevTest Labs[身分識別管理的 Azure AD 服務](../active-directory/fundamentals/active-directory-whatis.md)。 當您授與使用者存取基礎研發/測試實驗室環境時，請考慮這些兩個重要層面：
    - **資源管理**:它提供 Azure 入口網站來管理資源的存取權 （建立虛擬機器; 建立環境，啟動、 停止、 重新啟動、 刪除和套用成品、 和等）。 資源管理，即可在 Azure 中使用角色型存取控制 (RBAC)。 您指派角色給使用者，並設定資源和存取層級權限。
    - **虛擬機器 （網路層級）** :在預設組態中，虛擬機器會使用本機系統管理員帳戶。 如果沒有可用的網域 ([Azure AD Domain Services](../active-directory-domain-services/overview.md)，是在內部部署網域或雲端架構的網域)，機器可以加入至網域。 然後，使用者可以使用其網域為基礎的身分識別，來連線到 Vm。
- **內部部署連線能力**：在我們的架構圖表[ExpressRoute](../expressroute/expressroute-introduction.md)用。 您也可以使用，但[站對站 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 ExpressRoute 不需要研發/測試實驗室，雖然它常用的企業。 只有當您需要存取公司資源時，才需要 ExpressRoute。 常見的案例包括：
    - 您的內部部署資料無法移至雲端。
    - 您想要將實驗室的虛擬機器加入內部部署網域。
    - 您想要強制所有網路流量流入和流出通過安全性/法務遵循方面的內部部署防火牆的雲端環境。
- **網路安全性群組**：雲端環境 （或在雲端環境中） 來限制流量的常見方式是根據來源和目的地 IP 位址是使用[網路安全性群組](../virtual-network/security-overview.md)。 例如，您會想要允許來自公司網路，到實驗室的網路的流量。
- **遠端桌面閘道**:企業通常會封鎖在公司防火牆的連出遠端桌面連線。 有數個選項，才能連接至雲端架構的環境，在 DevTest Labs 中，包括：
  - 使用[遠端桌面閘道](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)、 及白名單閘道的靜態 IP 位址的負載平衡器。
  - [所有的連入 RDP 流量導向](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)透過 ExpressRoute/站台間 VPN 連線。 當企業計劃研發/測試實驗室部署，這項功能會是常見的考量。
- **網路服務 （虛擬網路、 子網路）** :[Azure 網路](../networking/networking-overview.md)拓樸是在研發/測試實驗室架構中的另一個關鍵要素。 它可控制實驗室的資源可以進行通訊並能夠存取內部部署和網際網路。 我們的架構圖包含最常見方法，讓客戶使用 DevTest Labs:透過連線的所有實驗室[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)利用[中樞支點模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)到內部部署 ExpressRoute/站台間 VPN 連接。 但是，DevTest Labs 會使用 Azure 虛擬網路的直接管理，因此沒有任何限制，在您設定網路基礎結構的方式。
- **DevTest Labs**:DevTest Labs 是整體架構的重要部分。 若要了解服務的相關資訊，請參閱[解 DevTest Labs](devtest-lab-overview.md)。
- **虛擬機器和其他資源 (SaaS、 PaaS、 IaaS)** :虛擬機器是 DevTest Labs 支援以及其他 Azure 資源的關鍵工作負載。 DevTest Labs 可讓它輕鬆又快速的企業提供 （包括 Vm 和其他 Azure 資源） 的 Azure 資源的存取權。 深入了解 Azure 的存取權[開發人員](devtest-lab-developer-lab.md)並[測試人員](devtest-lab-test-env.md)。

## <a name="scalability-considerations"></a>延展性考量
雖然 DevTest Labs 沒有內建的配額或限制，確實具有實驗室的一般作業中使用其他 Azure 資源[訂用帳戶層級配額](../azure-subscription-service-limits.md)。 因此，在典型的企業部署中，您需要以涵蓋 DevTest Labs 的大規模部署多個 Azure 訂用帳戶。 是企業最常達到配額：

- **資源群組**：在預設組態中，DevTest Labs 就會建立每個新的虛擬機器的資源群組或使用者建立使用服務的環境。 訂用帳戶可以包含[980 最多的資源群組](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)。 因此，這是虛擬機器和訂用帳戶中的環境的限制。 有兩個您應該考慮的其他組態：
    - **[所有虛擬機器都移至相同的資源群組](resource-group-control.md)** :雖然此設定可協助您符合資源群組的限制，它會影響的資源類型-每個-資源群組限制。
    - **使用共用公用 Ip**:相同的大小和區域的所有 Vm 會都進入相同的資源群組。 此設定是 「 中庸"資源群組配額與資源類型-每個-資源群組配額之間，如果虛擬機器都可以有公用 IP 位址。
- **每個資源的資源群組，每個資源類型**:預設限制[每個資源群組，每個資源類型的資源為 800](../azure-subscription-service-limits.md#resource-group-limits)。  當您使用*移至相同的資源群組的所有 Vm*設定、 使用者叫用此訂用帳戶限制太多更快，尤其是如果 Vm 有多個額外的磁碟。
- **儲存體帳戶**：在 DevTest Labs 中的對實驗室隨附儲存體帳戶。 Azure 配額[每個訂用帳戶區域的儲存體帳戶數目是 250](../azure-subscription-service-limits.md#storage-limits)。 研發/測試實驗室，在相同的區域中的最大數目也為 250。
- **角色指派**:角色指派是如何您授與使用者或主體存取的資源 （擁有者、 資源、 權限層級）。 在 Azure 中，沒有[限制為 2,000 每訂用帳戶的角色指派](../azure-subscription-service-limits.md#role-based-access-control-limits)。 根據預設，DevTest Labs 服務會建立資源群組，每個 VM。 授與擁有者*擁有者*DevTest Labs VM 的權限並*讀取器*的資源群組的權限。 如此一來，您所建立的每個新 VM 會使用兩個角色指派，除了您實驗室的權限授與使用者時所使用的指派。
- **API 的讀取/寫入**:有各種方式自動化 Azure 與研發/測試實驗室，包括 REST Api、 PowerShell、 Azure CLI 和 Azure SDK。 透過自動化，您可能會叫用另一個限制 API 要求：每個訂用帳戶允許最多[12,000 讀取要求和 1200 寫入每小時的要求](../azure-resource-manager/resource-manager-request-limits.md)。 請注意這項限制時自動執行 DevTest Labs。

## <a name="manageability-considerations"></a>管理性考量
DevTest Labs 有絕佳的系統管理使用者介面使用單一的實驗室。 但在企業中，您可能有多個 Azure 訂用帳戶和幾個實驗室。 對您的實驗室，以一致的方式進行變更，需要自動化指令碼。 以下是一些範例和最佳的管理作法，研發/測試實驗室部署：

- **實驗室設定的變更**:常見的案例是要更新特定的實驗室設定跨部署中的所有實驗室。 例如，新的 VM 執行個體大小可供使用，而必須更新所有實驗室，以允許它。 最好的方式是使用 PowerShell 指令碼、 CLI 或 REST Api，自動執行這些變更。  
- **成品存放庫個人存取權杖**:一般而言，在 90 天，一年或兩年後到期的 Git 儲存機制的個人存取權杖。 若要確保持續性，很重要擴充的個人存取權杖，或建立新的。 要套用至所有實驗室的 新增個人存取權杖，然後使用自動化。
- **限制實驗室設定的變更**:通常，特定的設定必須是受限制 （例如允許的 marketplace 映像使用）。 您可以使用 Azure 原則以防止資源類型的變更。 您可以建立自訂角色，並授與使用者而不是該角色*擁有者*實驗室的角色。 在實驗室 （內部支援、 實驗室公告、 允許 VM 大小等等） 中的大部分設定，您可以執行這項操作。
- **需要遵循命名慣例的 Vm**:管理員通常會想更容易找出 Vm 的雲端開發和測試環境的一部分。 您可以使用[Azure 原則](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)。

請務必注意，DevTest Labs 會使用管理的相同方式的基礎 Azure 資源： 網路、 磁碟、 計算、 等等。 例如，Azure 原則適用於在實驗室中建立的虛擬機器。 Azure 資訊安全中心可以在 VM 相容性報告。 和 Azure 備份服務可以為實驗室中的 Vm 提供定期備份。

## <a name="security-considerations"></a>安全性考量
Azure DevTest Labs 會使用現有的資源 （計算、 網路等等） 的 Azure 中。 因此它會自動得益於平台內建安全性功能。 比方說，若要要求只來自公司網路的連入遠端桌面連線，只要加入網路安全性群組上遠端桌面閘道的虛擬網路。 只能將額外的安全性考量是您在日常使用實驗室的小組成員授與的權限等級。 最常見的權限[*擁有者*並*使用者*](devtest-lab-add-devtest-user.md)。 如需有關這些角色的詳細資訊，請參閱 <<c0> [ 在 Azure DevTest Labs 中新增擁有者和使用者](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>後續步驟
請參閱本系列的下一篇文章：[相應增加您 Azure DevTest Labs 的基礎結構](devtest-lab-guidance-scale.md)。
