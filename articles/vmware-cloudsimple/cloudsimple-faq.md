---
title: 常見問題集-CloudSimple VMware 方案
description: Azure CloudSimple VMware 解決方案的常見問題集
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358524"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>關於 VMware CloudSimple 方案常見問題集

常見問題集的問與答 CloudSimple VMware 解決方案可協助您了解服務以及如何使用它。  下列類別排列的問題與解答。

* CloudSimple 服務
* 連線能力
* 網路功能
* 安全性
* 計算
* 儲存體
* VMware
* Azure 整合
  
## <a name="cloudsimple-service"></a>CloudSimple 服務

**什麼是 VMware CloudSimple 方案？**

**VMware 的解決方案，由 CloudSimple**轉換，並擴充至私用、 專用的雲端，在 Azure 上的 VMware 工作負載，以分鐘為單位。 我們會負責佈建、 管理基礎結構和協調內部部署與 Azure 之間的工作負載。 因為您的應用程式執行完全相同內部部署與在 Azure 中，您受益的彈性和雲端，以免除複雜的重新架構您的應用程式的服務。 CloudSimple 降低與雲端耗用量模型，可提供隨選佈建、 全額而定-做-您-成長，以及容量最佳化的擁有權總成本。  請參閱[什麼是 Azure CloudSimple 上的 VMware 解決方案](cloudsimple-vmware-solutions-overview.md)功能、 優點和案例。

**什麼是 CloudSimple 私用雲端？**

您佈建私用、 專用的雲端，其中包含高效能運算、 儲存體，和 Azure 位置中的網路功能環境部署在 Microsoft Azure 基礎結構 （硬體及資料中心空間）。  私用雲端提供原生的 VMware 平台 ' 做為服務 '。 VMware 而言，每個私用雲端會包含一個 vCenter Server 執行個體。 VCenter 伺服器管理多個 ESXi 節點包含一或多個 vSphere 叢集，並使用對應的虛擬 SAN (vSAN) 儲存體中。 CloudSimple 服務可以包含在您的 Azure 訂用帳戶中的多個私人雲端。  如需私人雲端的詳細資訊，請參閱[私用雲端概觀](cloudsimple-private-cloud.md)。

**其中是 CloudSimple 服務可用？**

CloudSimple 適用於美國東部和美國西部區域。

**如何啟用我的訂用帳戶，如 CloudSimple？**

您可以連絡 Microsoft 客戶代表在[ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com)啟用 CloudSimple 服務的訂用帳戶。 提供您要為其啟用 CloudSimple 服務電子郵件中的訂用帳戶識別碼。  

**如何存取 CloudSimple 入口網站？**

您從 Azure 入口網站存取 CloudSimple 入口網站。  請參閱[CloudSimple 入口網站，從 Azure 入口網站存取 VMware 解決方案](https://docs.azure.cloudsimple.com/access-cloudsimple-portal)如需有關存取 CloudSimple 入口網站的文章。 

**如何增加私人雲端容量？**

您購買從 Azure 入口網站的節點，並依序展開 CloudSimple 入口網站從您私用雲端。  藉由將其他節點新增至現有的 vSphere 叢集或建立新的 vSphere 叢集，您可以展開私用雲端。  請參閱[展開 CloudSimple 私用雲端](https://docs.azure.cloudsimple.com/expand-private-cloud)程序文章。

**會發生什麼事我的私用雲端在維護期間？**

CloudSimple 提供定期通知的排程維護之前的天數。  維護完成非干擾性的方式，以確保您的私用雲端的可用性。  維護可以是下列類型：

1. **CloudSimple 基礎結構：** CloudSimple 基礎結構被設計為高可用性。  在維護期間，被確保連線能力和可用性的私用雲端藉由更新備援元件一次而造成的影響。  您必須存取您的私用雲端 vCenter、 所有虛擬機器、 從私用雲端的網際網路連線和連線到內部部署或 Azure。
2. **CloudSimple 入口網站：** 在維護期間，CloudSimple 入口網站上的某些功能可能無法存取或已停用。  維護通知會包含項目可以在入口網站的詳細資料。

## <a name="connectivity"></a>連線能力

**CloudSimple 區域網路的連線選項有哪些？**

CloudSimple 提供三種不同的連接選項，來連接到 CloudSimple 區域網路。  這三個可以一起使用。

1. ExpressRoute 從內部部署資料中心 CloudSimple 區域網路連線-高速低延遲安全的私人連線橋接內部部署 ExpressRoute 線路使用觸及全球範圍對 CloudSimple ExpressRoute 線路。 請參閱[至 CloudSimple 使用 ExpressRoute 從內部部署連線](https://docs.azure.cloudsimple.com/on-premises-connection)設定連線的文章。
2. ExpressRoute 連線從 Azure 虛擬網路到 CloudSimple 區域網路的高速低延遲安全的私人連線橋接與您使用虛擬網路閘道的 CloudSimple ExpressRoute 線路的虛擬網路在 Azure 上。  請參閱[CloudSimple 私用雲端環境連線至 Azure 的虛擬網路，使用 ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection)設定連線的文章。
3. 站對站 VPN 連線，從內部部署資料中心到 CloudSimple 區域網路的安全虛擬私人網路，從您的內部部署 VPN 裝置以 CloudSimple 私用雲端區域。  設定 VPN 連線的 [設定您的內部部署網路與 CloudSimple 私用雲端之間的 VPN 連線]，請參閱文章。

**如何連接至私人雲端？**

您可以在 CloudSimple 入口網站中檢視私用雲端的詳細的資料。 若要連線到對應至您的私用雲端的 vCenter，請確定網路連線是建立使用站對站、 點對站或 ExpressRoute。 然後，啟動 CloudSimple 入口網站中的，從 Azure 入口網站，然後按一下 *啟動 vSphere 用戶端*的首頁上，或在私用雲端的 詳細資料 頁面上。

**ExpressRoute 線路的優點為何？**

Azure ExpressRoute 線路提供高速低延遲的安全連線。  CloudSimple 提供專用的 ExpressRoute 線路，每個區域每位客戶。  使用這個循環，您可以建立從內部部署和/或您的 Azure 訂用帳戶的安全連線。

**若要從 CloudSimple 連線的網路成本為何。從 azure CloudSimple 任何出口流量費用？跨區域嗎？**

沒有任何網路出口流量另外收費。  Azure 的標準費率適用於任何的輸出流量從您的虛擬網路或內部部署 ExpressRoute 線路。

## <a name="networking"></a>網路功能

**網路功能可供我的私用雲端？**

您可以佈建虛擬區域網路 （和其的子網路）、 防火牆資料表，並指派公用 IP 位址，並對應至私用雲端中執行的虛擬機器。  如需詳細資訊，請參閱 < [Vlan/子網路的概觀](cloudsimple-vlans-subnets.md)，[防火牆表格概觀](cloudsimple-firewall-tables.md)，並[公用 IP 位址概觀](cloudsimple-public-ip-address.md)文章。

**如何設定不同的子網路設定我的應用程式在我的私用雲端？**

您可以從您 CloudSimple 入口網站來建立私用雲端上的 Vlan。  一旦您建立 VLAN，您可以在您使用 VLAN 的私用雲端 vCenter 上建立分散式的連接埠群組，並建立虛擬機器連線至分散式通訊埠群組。  您可以啟用 VLAN/子網路的防火牆資料表，並定義要保護的網路流量的防火牆規則。

**哪些防火牆設定可供我的私人雲端？**

您可以設定北南向調整大小和東-西流量的規則。  防火牆資料表中定義的規則。  防火牆資料表可以附加至 VLAN(s) 私用雲端。  請參閱[防火牆資料表和規則設定為私用雲端](https://docs.azure.cloudsimple.com/firewall)安裝程序的文件。

**可以指派公用 IP 位址的 Vm 在私用雲端環境中？**

CloudSimple 入口網站中，您可以輕鬆地配置新的公用 IP 位址，並與您的虛擬機器或設備的私人 IP 位址建立關聯。  您也可以建立新的防火牆規則，或套用現有的防火牆規則以允許流量從特定連接埠和/或特定設定的入口網站中的 IP 位址。 請參閱[配置的私用雲端環境的公用 IP 位址](https://docs.azure.cloudsimple.com/public-ips)的安裝程序。

## <a name="security"></a>安全性

**我在 CloudSimple 上的安全性選項有哪些？**

CloudSimple 私用雲端的保護您的私用雲端環境提供下列安全性功能：

1. **待用資料加密**:您可以加密待用 vSAN 儲存空間上位於私用雲端的資料。 vSAN 支援外部金鑰管理伺服器，可以部署在您的 Azure vNet 或內部部署環境。  請參閱[設定 CloudSimple 私用雲端的 vSAN 加密](https://docs.azure.cloudsimple.com/vsan-encryption)如需詳細資訊。
2. **網路安全性**:控制網路流量從/至您從網際網路、 內部和私人雲端使用防火牆規則的子網路內的私用雲端。
3. **保障安全私人連線**:在內部部署網路與您 Azure 訂用帳戶之間的安全私人連線。

## <a name="compute"></a>計算

**哪種主機可供使用？**

CloudSimple 提供兩種主機類型：

* **CS28 節點：** CPU:2 2.2 GHz，總計的 28 個核心，48 x HT。  RAM：256 GB。  儲存體:1600 GB NVMe 快取，5760 GB 的資料 （全快閃）。 網路：2x25Gbe NIC
* **CS36 節點：** CPU 2 x 2.3 GHz，總 36 個核心，72 HT。  RAM：512 GB。  儲存體:3200 GB NVMe 快取 11,520 GB 的資料 （全快閃）。  網路：2x25Gbe NIC

**如何處理任何硬體失敗？**

CloudSimple 平台和我們的服務作業小組持續監視所有 CloudSimple 基礎結構。  如果偵測到硬體失敗時，新節點新增至您的私用雲端，並確保高可用性的私用雲端移除失敗的節點。

## <a name="storage"></a>儲存體

**私用雲端是否支援何種儲存體？**

提供 CloudSimple**全快閃 VMware 的 vSAN 儲存體**透過每個私用雲端。  每個 vSphere 會建立與自己 vSAN 資料存放區。  請參閱[私用雲端 VMware 元件-vSAN 的儲存體](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage)文章以取得詳細資料。

**是支援的資料加密？**
是。  您可以設定的 vSAN 存放裝置或在 Azure 上使用金鑰管理伺服器 (KMS) 也就是在內部部署私用雲端 vSAN 上儲存的資料加密

**如何處理失敗的磁碟？**

CloudSimple 監視會持續監視私用雲端的所有硬體的元件。  如果偵測到任何磁碟失敗或任何磁碟便會被視為失敗 （根據啟發學習法），新的節點會自動加入至私用雲端。  故障磁碟的節點會從私用雲端移除。

## <a name="vmware"></a>VMware

**如何從內部部署執行大規模的上傳/移轉應用程式和資料？**

CloudSimple 提供原生的 VMware vSphere 解決方案。  用於大量資料移轉的任何工具可以搭配 CloudSimple 私用雲端。  可用的選項包括：

1. VMware HCX 大量資料移轉。
2. 冷資料從內部部署至 CloudSimple 的儲存體 vMotion 移轉。

**可以安裝任何 VMware 工具嗎？**

CloudSimple 提供原生的 VMware vSphere 解決方案。  用來管理 vSphere 環境內部的任何工具可以用於 CloudSimple。  CloudSimple 安裝 VMware 工具支援攜帶您-自備授權 (BYOL) 模式。

**如何更新和升級管理？**

CloudSimple 管理，並以無縫式的非干擾性方式更新的私用雲端的所有基礎結構元件。  VMware] 或 [基礎結構的廠商發行任何更新或安全性修補程式將會排程進行更新，只要 CloudSimple 限定。

CloudSimple 不會執行升級或更新應用程式安裝在私用雲端。

## <a name="azure-integration"></a>Azure 整合

**支援哪些 Azure 服務？**

CloudSimple 提供 Azure 上的 Azure ExpressRoute 連線到您的訂用帳戶。  執行您的訂用帳戶中的任何服務具有私用雲端的網路連線，且可以連線到您的私用雲端。  範例：

1. **Azure Active Directory** CloudSimple vCenter 的身分識別來源為
2. **Azure 儲存體**來儲存備份、 影像和其他資料的私用雲端
3. **混合式應用程式**-您可以建立跨越公用和私人雲端的應用程式架構。  例如，您也可以存取應用程式和 CloudSimple 私用雲端上的資料庫伺服器的 Azure 中建立 webservers。
4. **Azure 監視器**並**Azure 資訊安全中心**-在 VMware 上執行的工作負載可以使用這些記錄、 效能度量和安全性管理。

**如何將我的 VMware 租用戶對應至 Azure？**

CloudSimple 提供獨特的功能，可從 Azure 入口網站中管理 VMware Vm 上的私用雲端。  VCenter 資源集區 （已設定具有所需的資源條件約束） 可以對應至您的訂用帳戶，全域系統管理員。  

**透過 Azure 取得哪些授權權益？**

CloudSimple，您可以利用 Azure 混合式使用權益與授權保留您的 Microsoft 授權，降低 TCO 相較於其他雲端投資節省高達 90%。 此外，您取得延伸安全性更新，適用於 Windows Server 2008 和 Microsoft SQL Server 2008。  保留針對常見的應用程式，例如 Veeam、 Zerto，及其他雲端使用自備授權 (BYOL) 的低成本。  