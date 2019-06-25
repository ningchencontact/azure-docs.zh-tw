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
ms.openlocfilehash: a8cc6cf834c54ca25c12a6d66675e4290fd66136
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165823"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>關於 VMware CloudSimple 方案常見問題集

常見問題集的問與答 CloudSimple Azure VMware 解決方案可協助您了解服務以及如何使用它。 在下列分類排列的問題與解答：

* CloudSimple 服務
* 連線能力
* 網路功能
* 安全性
* 計算
* 儲存體
* VMware
* 與 Azure 整合
 
## <a name="cloudsimple-service"></a>CloudSimple 服務

**什麼是 Azure CloudSimple VMware 方案？**

Azure 的 VMware 解決方案由 CloudSimple 轉換，並擴充至私用、 專用的雲端，在 Azure 上的 VMware 工作負載，以分鐘為單位。 解決方案會佈建、 管理基礎結構，並協調內部部署與 Azure 之間的工作負載。 因為您的應用程式執行完全相同內部部署與在 Azure 中，您受益的彈性和雲端，以免除複雜的重新架構您的應用程式的服務。 CloudSimple 降低與雲端耗用量模型，可提供隨選佈建、 全額而定-做-您-成長，以及容量最佳化的擁有權總成本。 功能、 權益和案例，請參閱[什麼是 Azure 的 VMware 解決方案由 CloudSimple？](cloudsimple-vmware-solutions-overview.md)。

**什麼是 CloudSimple 私用雲端？**

您佈建私用、 專用的雲端所組成的高效能計算、 儲存和部署 Microsoft Azure 基礎結構 （硬體及資料中心空間） 上的 Azure 位置中的網路環境。 私用雲端提供原生的 VMware 平台即服務。 在 VMware 詞彙中，每個私人雲端會包含一個 vcenter Server 的執行個體。 VCenter 伺服器管理多個 ESXi 節點包含一或多個 vSphere 叢集，並使用對應的 vSAN 儲存體中。 CloudSimple 服務可以包含在您的 Azure 訂用帳戶中的多個私人雲端。 如需私人雲端的詳細資訊，請參閱[私人雲端概觀](cloudsimple-private-cloud.md)。

**其中是 CloudSimple 服務可用？**

CloudSimple 適用於美國東部和美國西部區域。

**如何啟用我的訂用帳戶，如 CloudSimple？**

請連絡您的 Microsoft 帳戶代表在[ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com)啟用 CloudSimple 服務的訂用帳戶。 提供您要為其啟用 CloudSimple 服務電子郵件中的訂用帳戶識別碼。 

**如何存取 CloudSimple 入口網站？**

您從 Azure 入口網站存取 CloudSimple 入口網站。 如需如何存取 CloudSimple 入口網站的資訊，請參閱[CloudSimple 入口網站中從 Azure 入口網站來存取 VMware 解決方案](https://docs.azure.cloudsimple.com/access-cloudsimple-portal)。

**如何增加私人雲端容量？**

您從 Azure 入口網站佈建節點，然後展開 CloudSimple 入口網站從您私用雲端。 藉由新增節點至現有的 vSphere 叢集或建立新的 vSphere 叢集，您可以展開您的私人雲端。 如需此程序資訊，請參閱[展開 CloudSimple 私用雲端](https://docs.azure.cloudsimple.com/expand-private-cloud)。

**會發生什麼事我的私人雲端在維護期間？**

CloudSimple 提供定期通知排程維護的天數。 維護完成無干擾的方式，以確保您的私用雲端的可用性。 維護可以是下列類型：

- **CloudSimple 基礎結構**:CloudSimple 基礎結構被設計為高可用性。 在維護期間，連線能力和可用性的私人雲端被確保藉由更新備援元件一次而造成的影響。 您可以存取您的私用雲端 vCenter、 所有虛擬機器、 從您的私人雲端的網際網路連線和連線到內部部署或 Azure。
- **CloudSimple 入口網站**:在維護期間，CloudSimple 入口網站上的某些功能可能無法存取，或可能會停用。 維護通知會包含項目可以在入口網站的相關資訊。

## <a name="connectivity"></a>連線能力

**我 CloudSimple 的區域網路的連線選項有哪些？**

CloudSimple 提供三種不同的連接選項，來連接到 CloudSimple 區域網路。 所有的三個選項可以一起使用：

- Azure ExpressRoute 從內部部署資料中心 CloudSimple 區域網路連線：高速低延遲安全私人連線，可用來銜接內部部署 ExpressRoute 線路對 CloudSimple ExpressRoute 線路使用觸及全球範圍。 若要設定的連線，請參閱[連接從內部部署到使用 ExpressRoute CloudSimple](https://docs.azure.cloudsimple.com/on-premises-connection)。
- ExpressRoute 連線從 Azure 虛擬網路到 CloudSimple 區域網路：高速、 低延遲安全私人連線，可用來銜接 CloudSimple ExpressRoute 線路與虛擬網路在 Azure 上的使用虛擬網路閘道。 若要設定的連線，請參閱[CloudSimple 私用雲端環境使用連接到 Azure 虛擬網路 ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection)。
- 站對站 VPN 連線從內部部署資料中心到 CloudSimple 區域網路：安全的虛擬私人網路從您的內部部署 VPN 裝置到 CloudSimple 私用雲端區域。 若要設定的連線，請參閱[設定您的內部部署網路與 CloudSimple 私用雲端之間的 VPN 連線](https://docs.azure.cloudsimple.com/set-up-vpn)。

**如何連接至私人雲端？**

CloudSimple 入口網站中，您可以檢視您的私人雲端的詳細資料。 若要連線到對應至您的私人雲端的 vCenter，請確定建立網路連線時，會使用站對站、 點對站或 ExpressRoute。 接著，啟動 CloudSimple 入口網站中的，從 Azure 入口網站。 選取 **啟動 vSphere 用戶端**的首頁上，或在私人雲端的 詳細資料 頁面上。

**ExpressRoute 線路的優點為何？**

Azure ExpressRoute 線路提供的高速、 低延遲的安全連線。 CloudSimple 提供專用的 ExpressRoute 線路，每個區域每位客戶。 使用這個循環，您可以建立從內部部署和 Azure 訂用帳戶的安全連線。

**若要連線進出 CloudSimple 的網路成本為何？是否有與 Azure 的 CloudSimple 任何出口流量費用？跨區域是否有任何出口流量費用？**

沒有任何網路出口流量費用。 Azure 的標準費率適用於任何的輸出流量從您的虛擬網路，或從內部部署 ExpressRoute 線路。

## <a name="networking"></a>網路功能

**網路功能可供我的私用雲端？**

您可以佈建 Vlan 和子網路和防火牆的資料表。 您可以指派公用 IP 位址，並對應至您的私人雲端中執行的虛擬機器。 如需詳細資訊，請參閱 < [Vlan 和子網路的概觀](cloudsimple-vlans-subnets.md)，[防火牆表格概觀](cloudsimple-firewall-tables.md)，並[公用 IP 位址概觀](cloudsimple-public-ip-address.md)。

**如何設定不同的子網路設定我的應用程式在我的私人雲端中？**

您可以從您 CloudSimple 入口網站來建立您的私人雲端上的 Vlan。 建立 VLAN 之後，您可以使用 VLAN，私用雲端 vCenter 上建立分散式的連接埠群組，並建立虛擬機器連線至分散式通訊埠群組。 您可以啟用 VLAN] 或 [子網路的防火牆資料表，並定義要保護的網路流量的防火牆規則。

**哪些防火牆設定可供我的私人雲端？**

您可以設定北南向調整大小和東-西流量的規則。 防火牆資料表中定義的規則。 防火牆資料表可以附加至您的私人雲端上的 Vlan。 安裝程序中，請參閱[設定防火牆的資料表和規則的私用雲端](https://docs.azure.cloudsimple.com/firewall)。

**可以指派公用 IP 位址的 Vm 在私用雲端環境中？**

CloudSimple 入口網站中，您可以輕鬆地配置新的公用 IP 位址，並與您的虛擬機器或設備的私人 IP 位址建立關聯。 您也可以建立新的防火牆規則，或套用現有的防火牆規則以允許流量從特定連接埠與特定設定的入口網站中的 IP 位址。 安裝程序中，請參閱[配置的私用雲端環境的公用 IP 位址](https://docs.azure.cloudsimple.com/public-ips)。

## <a name="security"></a>安全性

**我在 CloudSimple 上的安全性選項有哪些？**

CloudSimple 私人雲端可提供下列安全性功能來保護您的私用雲端環境：

- **待用資料加密：** 您可以加密位於 vSAN 儲存體，在您的私人雲端之待用資料。 vSAN 支援外部金鑰管理伺服器，可以部署在 Azure 虛擬網路或內部部署環境。 如需詳細資訊，請參閱 <<c0> [ 設定為 CloudSimple 私人雲端的 vSAN 加密](https://docs.azure.cloudsimple.com/vsan-encryption)。
- **網路安全性：** 控制網路流量從和到網際網路，在內部與私用雲端是使用防火牆規則的子網路內私人雲端。
- **安全的私人連線：** 安全、 內部部署網路與您 Azure 訂用帳戶之間的私人連線。

## <a name="compute"></a>計算

**哪種主機可供使用？**

CloudSimple 提供兩種主機類型：

* **CS28 節點**:CPU:2 2.2 GHz，總計的 28 個核心，48 x HT。 RAM：256 GB。 儲存體:1600 GB NVMe 快取，5760 GB 的資料 （全快閃）。 網路：2x25Gbe nic。
* **CS36 節點**:CPU 2 x 2.3 GHz，36 的核心總數，72 HT。 RAM：512 GB。 儲存體:3200 GB NVMe 快取 11,520 GB 資料 （全快閃）。 網路：2x25Gbe nic。

**如何處理的硬體故障？**

所有 CloudSimple 基礎結構會持續都監視 CloudSimple 平台和其服務營運團隊。 如果偵測到硬體失敗時，新的節點會加入您的私人雲端中。 失敗的節點會移除以確保您的私人雲端的高可用性。

## <a name="storage"></a>儲存體

**私用雲端是否支援何種儲存體？**

提供 CloudSimple**全快閃 VMware 的 vSAN 儲存體**與每個私人雲端。 每個 vSphere 會建立與自己 vSAN 資料存放區。 如需詳細資訊，請參閱 <<c0> [ 私人雲端之 VMware 元件-vSAN 的儲存體](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage)。

**是支援的資料加密？**
是。 您可以設定的 vSAN 存放裝置使用金鑰管理伺服器 (KMS)，也就是在內部部署私人雲端或在 Azure 上的 vSAN 上儲存的資料加密。

**如何處理失敗的磁碟？**

CloudSimple 監視會持續監視私人雲端的所有硬體的元件。 如果偵測到磁碟失敗或磁碟被識別為無法根據啟發學習法，則新的節點會自動加入到私人雲端中。 故障磁碟的節點會從私人雲端移除。

## <a name="vmware"></a>VMware

**如何從內部部署執行大規模的上傳和應用程式和資料移轉？**

CloudSimple 提供原生的 VMware vSphere 解決方案。 用於大量資料移轉的任何工具可以搭配 CloudSimple 私用雲端。 可用的選項包括：

- VMware HCX 大量資料移轉。
- 冷資料使用 CloudSimple 來從內部部署的儲存體 vMotion 移轉。

**可以安裝任何 VMware 工具嗎？**

CloudSimple 提供原生的 VMware vSphere 解決方案。 用來管理 vSphere 環境 CloudSimple 可用於在內部部署的任何工具。 CloudSimple 安裝 VMware 工具支援自備-您擁有的授權 (BYOL) 模式。

**如何更新和升級管理？**

CloudSimple 管理，並更新您的私用雲端的所有基礎結構元件順暢無干擾的方式。 VMware] 或 [基礎結構的廠商發行任何更新或安全性修補程式已排程進行更新，因為它由 CloudSimple 限定。

CloudSimple 不會執行升級或更新應用程式安裝在私人雲端。

## <a name="azure-integration"></a>與 Azure 整合

**支援哪些 Azure 服務？**

CloudSimple 提供 Azure 上的 Azure ExpressRoute 連線到您的訂用帳戶。 在您的訂用帳戶中執行的任何服務有網路連線到您的私人雲端，且可以連線到私人雲端。 範例：

- **Azure Active Directory**：使用 Azure Active Directory 做為身分識別來源，CloudSimple vcenter。
- **Azure 儲存體**：您可以使用儲存體儲存的備份、 影像和其他資料，從您的私人雲端。
- **混合式應用程式**:您可以建立跨越公用和私人雲端的應用程式架構。 比方說，您可以建立 web 伺服器在 Azure 中該應用程式的存取和資料庫伺服器 CloudSimple 私人雲端上。
- **Azure 監視器**並**Azure 資訊安全中心**:在 VMware 執行的工作負載可以使用監視和資訊安全中心記錄、 效能度量和安全性管理。

**如何將我的 VMware 租用戶對應至 Azure？**

CloudSimple 提供獨特的功能，可從 Azure 入口網站中管理您在私人雲端上的 VMware Vm。 VCenter 資源集區設定您想要可以對應到您的訂用帳戶，全域系統管理員的資源限制。 

**透過 Azure 取得哪些授權權益？**

CloudSimple，您可以利用 Azure Hybrid Benefit 與節省授權，才能保留您的 Microsoft 授權投資，並且降低相較於其他雲端的擁有權總成本達 90%。 您也取得延伸的 Windows Server 2008 和 Microsoft SQL Server 2008 安全性更新。 保留像是 Veeam、 Zerto，以及其他常見的應用程式的雲端使用 BYOL 的低成本。 
