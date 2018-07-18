---
title: SAP HANA on Azure (大型執行個體) 的概觀和架構 | Microsoft Docs
description: SAP HANA on Azure (大型執行個體) 部署方式的架構概觀。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3918096a977cfd48e2128646d7c552e842ab8834
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063675"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Azure 上的 SAP HANA (大型執行個體) 概觀和架構

## <a name="what-is-sap-hana-on-azure-large-instances"></a>什麼是 SAP HANA on Azure (大型執行個體)？

SAP HANA on Azure (大型執行個體) 是 Azure 獨有的解決方案。 除了提供虛擬機器讓您部署和執行 SAP HANA 外，Azure 還可讓您在您專屬的裸機伺服器上執行並部署 SAP HANA。 SAP HANA on Azure (大型執行個體) 解決方案會建置在指派給您的非共用主機/伺服器裸機硬體上。 伺服器硬體會內嵌在更大的戳記中，該戳記會包含計算/伺服器、網路和儲存體基礎結構。 這是經過 HANA Tailored Data Center Integration (TDI) 認證的組合。 SAP HANA on Azure (大型執行個體) 提供不同的 SKU 或大小。 單位可具有 36 Intel CPU 核心和 768 GB 記憶體，並移至具有多達 480 Intel CPU 核心和多達 24 TB 記憶體的單位。

客戶在基礎結構戳記內是利用租用戶來區隔，如下所示：

- **網路**：透過每個被指派租用戶的客戶所擁有的虛擬網路，在基礎結構堆疊內區隔客戶。 一個租用戶只會指派給單一客戶。 但一個客戶可以擁有多個租用戶。 租用戶的網路區隔會在基礎結構戳記層級禁止租用戶之間的網路通訊，即使租用戶屬於相同客戶亦然。
- **儲存體元件**：透過已被指派存放磁碟區的存放虛擬機器來區隔。 存放磁碟區只能指派給一個存放虛擬機器。 一個存放虛擬機器只會指派給已通過 SAP HANA TDI 認證之基礎結構堆疊中的一個租用戶。 因此，指派給存放虛擬機器的存放磁碟區只能在一個特定且相關的租用戶中受到存取。 它們在已部署的不同租用戶之間是不可見的。
- **伺服器或主機**：伺服器或主機單位不會在客戶或租用戶之間共用。 部署給客戶的伺服器或主機是不可分割的裸機計算單位，並且只會指派給一個租用戶。 您*無法*使用任何可讓您與其他客戶共用主機或伺服器的硬體分割或軟體分割。 指派給特定租用戶之存放虛擬機器的存放磁碟區會掛接到這類伺服器。 一個租用戶可以獨佔方式獲得不同 SKU 的一個到多個伺服器單位。
- SAP HANA on Azure (大型執行個體) 的基礎結構戳記內會部署許多不同的租用戶，並透過租用戶概念在網路、儲存體和計算層級將這些租用戶彼此區隔。 


這些裸機伺服器單位只支援用來執行 SAP HANA。 SAP 應用程式層或工作負載中介軟體層會在虛擬機器中執行。 執行 SAP HANA on Azure (大型執行個體) 單位的基礎結構戳記會連線至 Azure 網路服務骨幹。 如此，即可提供 SAP HANA on Azure (大型執行個體) 單位與虛擬機器之間的低延遲連線。

在多份說明 SAP HANA on Azure (大型執行個體) 的文件中，本文件是其中之一。 本文件將介紹解決方案所提供的基本架構、責任和服務。 此外也會討論解決方案的高階功能。 其他四份文件會詳述並深入探討其他大多數的層面，例如網路和連線。 SAP HANA on Azure (大型執行個體) 的文件並未說明 SAP NetWeaver 安裝方面或在 VM 中部署 SAP NetWeaver 方面的內容。 Azure 上的 SAP NetWeaver 可在相同 Azure 說明文件容器中的個別文件中找到。 


HANA 大型執行個體指南的不同文件涵蓋下列領域：

- [Azure 上的 SAP HANA (大型執行個體) 概觀和架構](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA (大型執行個體) 的基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [在 Azure 上安裝和設定 SAP HANA (大型執行個體)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA (大型執行個體) 高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 上的 SAP HANA (大型執行個體) 疑難排解和監視](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [使用 STONITH 在 SUSE 中進行高可用性設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [類型 II SKU 的 OS 備份和還原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>定義

《架構和技術部署指南》中廣泛使用數個常見的定義。 請注意下列詞彙及其意義：

- **IaaS**：基礎結構即服務。
- **PaaS**：平台即服務。
- **SaaS**：軟體即服務。
- **SAP 元件**︰個別的 SAP 應用程式，例如 ERP Central Component (ECC)、Business Warehouse (BW)、Solution Manager 或企業版入口網站 (EP)。 SAP 元件可以傳統 ABAP 或 Java 技術為基礎，或以非 NetWeaver 應用程式 (例如商務物件) 為基礎。
- **SAP 環境**︰一或多個以邏輯方式分組的 SAP 元件，可執行像是開發、品質保證、訓練、災害復原或生產等商務功能。
- **SAP 架構**︰意指您 IT 架構中的整個 SAP 資產。 SAP 環境包含所有生產和非生產環境。
- **SAP 系統**︰DBMS 層與應用程式層的組合，例如 SAP ERP 開發系統、SAP BW 測試系統、SAP CRM 生產系統等。 Azure 部署不支援在內部部署與 Azure 之間分割這兩層。 SAP 系統可以在內部部署或在 Azure 中部署。 您可以將 SAP 環境的不同系統部署到 Azure 或內部部署中。 例如，您可以在 Azure 中部署 SAP CRM 開發和測試系統，而在內部部署 SAP CRM 生產系統。 就 SAP HANA on Azure (大型執行個體) 而言，您應在 VM 中裝載 SAP 系統的 SAP 應用程式層，並在 SAP HANA on Azure (大型執行個體) 戳記中的某個單位上裝載相關的 SAP HANA 執行個體。
- **大型執行個體戳記**︰經 SAP HANA TDI 認證，並專門用來執行 Azure 內的 SAP HANA 執行個體的硬體基礎結構堆疊。
- 
  **SAP HANA on Azure (大型執行個體)**：Azure 中的供應項目的正式名稱；此供應項目可在經 SAP HANA TDI 認證並部署在不同 Azure 區域的「大型執行個體」戳記中的硬體上執行 HANA 執行個體。 相關詞彙 *HANA 大型執行個體*是 *SAP HANA on Azure (大型執行個體)* 的簡稱，廣泛使用於本技術部署指南中。
- **跨單位**：說明將 VM 部署到在內部部署資料中心與 Azure 之間具有站對站、多網站或 Azure ExpressRoute 連線能力的 Azure 訂用帳戶的案例。 在一般 Azure 文件中，這類部署也會描述為跨單位案例。 之所以連線，是為了將內部部署網域、內部部署 Azure Active Directory/OpenLDAP 和內部部署 DNS 擴充到 Azure 中。 內部部署架構會擴充至 Azure 訂用帳戶的 Azure 資產。 透過此擴充，VM 可以是內部部署網域的一部分。 

   內部部署網域的網域使用者可以存取伺服器，並可在這些 VM 上執行服務 (例如 DBMS 服務)。 在內部部署的 VM 與 Azure 部署的 VM 之間可進行通訊和名稱解析。 這是部署大部分 SAP 資產時的典型案例。 如需詳細資訊，請參閱[規劃與設計 Azure VPN 閘道](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)和[使用 Azure 入口網站建立具有站對站連線的虛擬網路](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- **租用戶**：部署於 HANA 大型執行個體戳記的客戶會隔離到*租用戶*中。 每個租用戶的網路、儲存體和計算層會彼此區隔開來。 指派給不同租用戶的儲存體和計算單位無法看到彼此，也無法在 HANA 大型執行個體戳記層級上彼此通訊。 客戶可以選擇部署到不同的租用戶。 即使如此，租用戶彼此之間還是無法在 HANA 大型執行個體戳記層級上進行通訊。
- 
  **SKU 類別**：對於 HANA 大型執行個體，會提供下列兩種 SKU：
    - **類型 I 類別**：S72、S72m、S144、S144m、S192、S192m 和 S192xm
    - **類型 II 類別**：S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm 和 S960m


您可以透過其他多項資源了解如何在雲端中部署 SAP 工作負載。 如果您想要在 Azure 中執行 SAP HANA 的部署，您必須熟悉並了解 Azure IaaS 的主體及 Azure IaaS 上的 SAP 工作負載部署。 在繼續作業之前，請參閱[在 Azure 虛擬機器上使用 SAP 解決方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)以取得詳細資訊。 

## <a name="certification"></a>認證

除了 NetWeaver 認證之外，SAP 還需要特殊的 SAP HANA 認證，才能在特定的基礎結構 (例如 Azure IaaS) 上支援 SAP HANA。

NetWeaver 和 SAP HANA 認證 (就某種程度而言) 的核心 SAP 附註是 [SAP 附註 #1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533)。

SAP Hana on Azure (大型執行個體) 單位的憑證記錄位於 [SAP HANA 認證 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)網站中。 

SAP Hana 認證 IaaS 平台網站中提及的 SAP HANA on Azure (大型執行個體) 類型可讓 Microsoft 和 SAP 客戶能夠在 Azure 中部署大型 SAP Business Suite、SAP BW、S/4 HANA、BW/4HANA 或其他 SAP HANA 工作負載。 此解決方案是以經 SAP-HANA 認證的專用硬體戳記 ([SAP HANA tailored Data Center Integration - TDI](https://scn.sap.com/docs/DOC-63140)) 為基礎。 如果您執行 SAP HANA TDI 設定的解決方案，則所有的 SAP HANA 型應用程式 (例如 SAP Business Suite on SAP HANA、SAP BW on SAP HANA、S4/HANA 及 BW4/HANA) 都能在硬體基礎結構上運作。

相較於在 VM 中執行 SAP HANA，此解決方案有其優點。 它可提供大得多的記憶體磁碟區。 若要啟用此解決方案，您必須了解下列重要層面：

- SAP 應用程式層和非 SAP 應用程式是在裝載於一般 Azure 硬體戳記中的 VM 上執行。
- 客戶內部部署基礎結構、資料中心及應用程式部署都會透過 ExpressRoute (建議使用) 或虛擬私人網路 (VPN) 連線至雲端平台。 Active Directory 和 DNS 也會擴充到 Azure 中。
- 用於 HANA 工作負載的 SAP HANA 資料庫執行個體會在 SAP HANA on Azure (大型執行個體) 上執行。 「大型執行個體」戳記會連線至 Azure 網路中，讓在 VM 中執行的軟體能夠與在「HANA 大型執行個體」中執行的 HANA 執行個體互動。
- SAP HANA on Azure (大型執行個體) 的硬體是已預先安裝 SUSE Linux Enterprise Server 或 Red Hat Enterprise Linux 的 IaaS 中所提供的專用硬體。 和使用虛擬機器一樣，進一步的作業系統更新和維護也是由您負責。
- 安裝 HANA 或任何在 HANA 大型執行個體的單位上執行 SAP HANA 所需的額外元件，是您的負責。 您也須負責執行 SAP HANA on Azure 的所有個別進行中作業和管理。
- 除了這裡所述的解決方案之外，您也可以在連接到 SAP HANA on Azure (大型執行個體) 的 Azure 訂用帳戶中安裝其他元件。 範例包括可用來與 SAP HANA 資料庫通訊或直接通訊的元件，例如跳板伺服器、RDP 伺服器、SAP HANA Studio、適用於 SAP BI 案例的 SAP Data Services，或網路監視解決方案。
- 和在 Azure 中一樣，HANA 大型執行個體也可支援高可用性和災害復原功能。

## <a name="architecture"></a>架構

簡要來說，SAP HANA on Azure (大型執行個體) 解決方案會將 SAP 應用程式層放在 VM 中。 資料庫層則放在 SAP TDI 設定的硬體上，而此硬體是位於相同 Azure 區域中連線至 Azure IaaS 的「大型執行個體」戳記中。

> [!NOTE]
> 請在與 SAP DBMS 層相同的 Azure 區域中部署 SAP 應用程式層。 此規則詳載於已針對 Azure 上的 SAP 工作負載發佈的相關資訊中。 

SAP HANA on Azure (大型執行個體) 的整體架構會提供一個經 SAP TDI 認證的硬體組態 (適用於 SAP HANA 資料庫的非虛擬化、裸機、高效能伺服器)。 此外也提供 Azure 的能力與彈性來調整 SAP 應用程式層的資源，以符合您的需求。

![SAP HANA on Azure (大型執行個體) 的架構概觀](./media/hana-overview-architecture/image1-architecture.png)

顯示的架構分成三個部分：

- **右邊**：顯示一個在資料中心執行不同應用程式，讓使用者能夠存取 LOB 應用程式 (例如 SAP) 的內部部署基礎結構。 在理想的情況下，這個內部部署基礎結構會接著以 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 連線至 Azure。

- **中間**：顯示 Azure IaaS，在此案例中，會使用 VM 來裝載 SAP 或其他利用 SAP HANA 作為 DBMS 系統的應用程式。 以 VM 提供的記憶體運作的較小 HANA 執行個體會與其應用程式層一起部署在 VM 中。 如需虛擬機器的詳細資訊，請參閱[虛擬機器](https://azure.microsoft.com/services/virtual-machines/)。

   Azure 網路服務可用來將 SAP 系統及其他應用程式一起分組到虛擬網路中。 這些虛擬網路會連線至內部部署系統和 SAP HANA on Azure (大型執行個體)。

   對於可在 Azure 中執行的 SAP NetWeaver 應用程式和資料庫，請參閱 [SAP 支援附註 #1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533)。 如需關於如何在 Azure 上部署 SAP 解決方案的說明文件，請參閱：

  -  [在 Windows 虛擬機器上使用 SAP](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [在 Azure 虛擬機器上使用 SAP 解決方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左邊**：顯示「Azure 大型執行個體」戳記中經 SAP HANA TDI 認證的硬體。 「HANA 大型執行個體」單位會使用與從內部部署連線至 Azure 時的相同技術，連線至您訂用帳戶的虛擬網路。

「Azure 大型執行個體」戳記本身結合了下列元件：

- **運算**：以 Intel Xeon E7-8890v3 或 Intel Xeon E7-8890v4 處理器為基礎的伺服器，可提供必要的運算能力，且經過 SAP HANA 的認證。
- **網路**：將運算、儲存體及 LAN 元件互連的整合式高速網路網狀架構。
- **儲存體**：可透過整合式網路網狀架構存取的儲存體基礎結構。 根據已部署的特定 SAP HANA on Azure (大型執行個體) 組態提供的特定儲存體容量。 若要使用更多儲存體容量，則需每月額外付費。

在「大型執行個體」戳記的多租用戶基礎結構內，是將客戶部署成隔離的租用戶。 在租用戶的部署中，您必須在 Azure 註冊內為 Azure 訂用帳戶命名。 此 Azure 訂用帳戶將是 HANA 大型執行個體據以付費的訂用帳戶。 這些租用戶與 Azure 訂用帳戶具有一對一關係。 就網路而言，從分屬於不同 Azure 訂用帳戶的不同虛擬網路，存取在某個 Azure 區域的某個租用戶中部署的 HANA 大型執行個體單位，是可行的。 這些 Azure 訂用帳戶必須屬於相同的 Azure 註冊。 

和 VM 一樣，SAP HANA on Azure (大型執行個體) 會提供於多個 Azure 區域中。 若要提供災害復原功能，您可以選擇加入。 某個地理政治區域內不同的大型執行個體戳記會彼此互連。 例如，為了進行災害復原複寫，可以透過專用網路連結來連線美國西部和美國東部的 HANA 大型執行個體戳記。 

就像使用「Azure 虛擬機器」時可在不同的 VM 類型間做選擇一樣，您也可以從針對不同 SAP HANA 工作負載類型量身打造的不同「HANA 大型執行個體」SKU 中做選擇。 SAP 會根據 Intel 處理器世代，為不同的工作負載套用記憶體對處理器插槽的比例。 下表顯示提供的 SKU 類型。

SAP Hana on Azure (大型執行個體) 服務已可用於美國西部和美國東部、澳大利亞東部、澳大利亞東南部、西歐、北歐、日本東部及日本西部等 Azure 區域中的數個組態。

[HANA 大型執行個體的 SAP Hana 認證 SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 清單如下：

| SAP 解決方案 | CPU | 記憶體 | 儲存體 | 可用性 |
| --- | --- | --- | --- | --- |
| 已針對 OLAP 最佳化：SAP BW、BW/4HANA<br /> 或 SAP HANA (一般 OLAP 工作負載) | SAP HANA on Azure S72<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 個 CPU 核心和 72 個 CPU 執行緒 |  768 GB |  3 TB | 可用 |
| --- | SAP HANA on Azure S144<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 個 CPU 核心和 144 個 CPU 執行緒 |  1.5 TB |  6 TB | 不再提供 |
| --- | SAP HANA on Azure S192<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 個 CPU 核心和 192 個 CPU 執行緒 |  2.0 TB |  8 TB | 可用 |
| --- | SAP HANA on Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  4.0 TB |  16 TB | 可用 |
| 已針對 OLTP 最佳化：SAP Business Suite<br /> on SAP HANA 或 S/4HANA (OLTP)<br /> (一般 OLTP) | SAP HANA on Azure S72m<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 個 CPU 核心和 72 個 CPU 執行緒 |  1.5 TB |  6 TB | 可用 |
|---| SAP HANA on Azure S144m<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 個 CPU 核心和 144 個 CPU 執行緒 |  3.0 TB |  12 TB | 不再提供 |
|---| SAP HANA on Azure S192m<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 個 CPU 核心和 192 個 CPU 執行緒  |  4.0 TB |  16 TB | 可用 |
|---| SAP HANA on Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  6.0 TB |  18 TB | 可用 |
|---| SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  8.0 TB |  22 TB |  可用 |
|---| SAP HANA on Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 個 CPU 核心和 576 個 CPU 執行緒 |  12.0 TB |  28 TB | 可用 |
|---| SAP HANA on Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 個 CPU 核心和 768 個 CPU 執行緒 |  16.0 TB |  36 TB | 可用 |
|---| SAP HANA on Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 個 CPU 核心和 960 個 CPU 執行緒 |  20.0 TB |  46 TB | 可用 |


在 SAP Hana TDIv5 下，SAP 允許客戶專屬的大小調整及客戶專屬專案，可能會導致以下位置的伺服器設定未列為已認證：

- [SAP Hana 認證設備](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP Hana 認證 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

在許多情況下，這些客戶專屬的伺服器設定會比以 SAP 認證的伺服器單位執行更多記憶體。 使用 SAP 時，客戶可能會取得 SAP 支援，並為其客戶專屬大小的伺服器設定進行認證。 下列 HANA 大型執行個體標準 SKU 可於 Azure 中使用，且這類 TDIv5 客戶專屬的大小調整專案可在 Microsoft 價格清單中。


| 原始 SKU 可以 <br /> 在記憶體中擴充 | CPU | 記憶體 | 儲存體 | 可用性 |
| --- | --- | --- | --- | --- |
| S192m 可擴充到 | SAP HANA on Azure S192xm<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 個 CPU 核心和 192 個 CPU 執行緒 |  6.0 TB |  16 TB | 可用 |
| S384xm 可擴充到 | SAP HANA on Azure S384xxm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  12.0 TB |  28 TB | 可用 |
| S576m 可擴充到 | SAP HANA on Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 個 CPU 核心和 576 個 CPU 執行緒 |  18.0 TB |  41 TB | 可用 |
| S768m 可擴充到 | SAP HANA on Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 個 CPU 核心和 768 個 CPU 執行緒 |  24.0 TB |  56 TB | 可用 |

- CPU 核心 = 伺服器單位處理器總和的非超執行緒 CPU 核心總和。
- CPU 執行緒 = 伺服器單位處理器總和的超執行緒 CPU 核心所提供之計算執行緒的總和。 依預設會將大部分的單位設定為使用超執行緒技術。
- 根據供應商建議，S768m、S768xm 和 S960m 並未設定可使用超執行緒來執行 SAP Hana。


選擇的特定組態取決於工作負載、CPU 資源及所需的記憶體。 OLTP 工作負載可以使用已針對 OLAP 工作負載最佳化的 SKU。 

供應項目的硬體基底 (除了客戶專屬的大小調整專案單位) 已通過 SAP Hana TDI 認證。 兩種不同類別的硬體會將 SKU 分成：

- S72、S72m、S144、S144m、S192、S192m 和 S192xm，稱之為「類型 I 類別」的 SKU。
- S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm 和 S960m，稱之為「類型 II 類別」的 SKU。

完整的 HANA 大型執行個體戳記並非僅配置給單一客戶使用。 這項事實也適用於透過 Azure 中所部署的網路網狀架構連線的計算與儲存資源的機架。 「HANA 大型執行個體」基礎結構和 Azure 一樣，會在下列三個層面部署彼此隔離的不同客戶&quot;租用戶&quot;：

- **網路**：透過虛擬網路在 HANA 大型執行個體戳記內區隔。
- **儲存體**：透過已被指派存放磁碟區的存放虛擬機器和租用戶之間的獨立存放磁碟區來區隔。
- **計算**：將伺服器單位指派給單一租用戶專用。 不對伺服器單位進行硬體分割或軟體分割。 不在租用戶之間共用單一伺服器或主機單位。 

不同租用戶之間的 HANA 大型執行個體單位部署不會讓彼此看到。 部署在不同租用戶的 HANA 大型執行個體單位無法在 HANA 大型執行個體戳記層級上直接與其他單位進行通訊。 只有同一個租用戶內的 HANA 大型執行個體單位可以在 HANA 大型執行個體戳記層級上與其他單位進行通訊。

為了進行計費，「大型執行個體」戳記中已部署的租用戶會指派給一個 Azure 訂用帳戶。 就網路而言，從同一個 Azure 註冊內的其他 Azure 訂用帳戶的虛擬網路加以存取，是可行的。 如果您使用同一個 Azure 區域中的另一個 Azure 訂用帳戶來部署，則也可以選擇尋求個別的 HANA 大型執行個體租用戶。

在「HANA 大型執行個體」上執行 SAP HANA，與在部署於 Azure 中的 VM 上執行 SAP HANA，有顯著的不同：

- SAP HANA on Azure (大型執行個體) 沒有虛擬化層。 您會獲得基礎裸機硬體的效能。
- 與 Azure 不同，SAP HANA on Azure (大型執行個體) 伺服器是特定客戶專用。 您無法對伺服器單位或主機進行硬體分割或軟體分割。 因此，HANA 大型執行個體單位會整個指派給租用戶，並整體指派給您。 重新啟動或關閉伺服器並不會自動導致作業系統和 SAP HANA 被部署到另一部伺服器。 (針對類型 I 類別的 SKU，唯一的例外狀況，是如果伺服器發生問題而必須在另一部伺服器上執行重新部署的情況)。
- 與 Azure 中配合最佳性價比來選取主機處理器類型的方式不同，為 SAP HANA on Azure (大型執行個體) 選擇的處理器類型是 Intel E7v3 和 E7v4 處理器系列中效能最佳的類型。


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>在一個 HANA 大型執行個體單位上執行多個 SAP HANA 執行個體
您可以在 HANA 大型執行個體單位上，裝載多個使用中的 SAP HANA 執行個體。 若要提供儲存體快照集和災害復原等功能，這類設定在每個執行個體上都需要一個磁碟區組。 目前可使用下列方式來細分 HANA 大型執行個體單位：

- **S72、S72m、S144、S192**：遞增量為 256 GB，最小起始單位為 256 GB。 不同的遞增量 (例如 256 GB 和 512 GB) 可組合為該單位的記憶體最大值。
- **S144m 和 S192m**：遞增量為 256 GB，最小單位為 512 GB。 不同的遞增量 (例如 512 GB 和 768 GB) 可組合為該單位的記憶體最大值。
- **類型 II 類別**：遞增量為 512 GB，最小起始單位為 2 TB。 不同的遞增量 (例如 512 GB、1 TB 和 1.5 TB) 可組合為該單位的記憶體最大值。

執行多個 SAP HANA 執行個體的範例可能顯示如下。

| SKU | 記憶體大小 | 儲存體大小 | 具有多個資料庫的大小 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768 GB HANA 執行個體<br /> 或 1x512 GB 執行個體 + 1x256 GB 執行個體<br /> 或 3x256 GB 執行個體 | 
| S72m | 1.5 TB | 6 TB | 3x512GB HANA 執行個體<br />或 1x512 GB 執行個體 + 1x1 TB 執行個體<br />或 6x256 GB 執行個體<br />或 1x1.5 TB 執行個體 | 
| S192m | 4 TB | 16 TB | 8x512 GB 執行個體<br />或 4x1 TB 執行個體<br />或 4x512 GB 執行個體 + 2x1 TB 執行個體<br />或 4x768 GB 執行個體 + 2x512 GB 執行個體<br />或 1x4 TB 執行個體 |
| S384xm | 8 TB | 22 TB | 4x2 TB 執行個體<br />或 2x4 TB 執行個體<br />或 2x3 TB 執行個體 + 1x2 TB 執行個體<br />或 2x2.5 TB 執行個體 + 1x3 TB 執行個體<br />或 1x8 TB 執行個體 |


此外還有其他變化形式。 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>使用 SAP HANA 資料分層與擴充節點
對於不同 SAP NetWeaver 版本的 SAP BW 和 SAP BW/4HANA，SAP 可支援資料分層模型。 如需關於資料分層模型的詳細資訊，請參閱 SAP 文件：[具有 SAP HANA 擴充節點之 HANA 上的 SAP BW/4HANA 和 SAP BW](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)。
透過 HANA 大型執行個體，您可以使用 SAP HANA 擴充節點的 option-1 組態，其相關說明請見常見問題集和 SAP 部落格文件。 option-2 設定可以搭配下列 HANA 大型執行個體 SKU 來進行設定：S72m、S192、S192m、S384 和 S384m。 

當您檢視文件時，您可能不會立即看出其優勢。 但只要看看 SAP 大小調整指導方針，您就可以使用 option-1 和 option-2 SAP HANA 擴充節點看出其優勢。 範例如下：

- SAP HANA 大小調整指導方針通常需要兩倍的資料磁碟區數量來作為記憶體。 當您使用熱資料來執行 SAP HANA 執行個體時，最多只會有 50% 的記憶體用於資料。 理論上，剩餘的記憶體會保留下來供 SAP HANA 執行其工作。
- 這表示，在具有 2 TB 記憶體且執行 SAP BW 資料庫的 HANA 大型執行個體 S192 單位中，只有 1 TB 會作為資料磁碟區。
- 如果您使用其他採用 option-1 的 SAP HANA 擴充節點 (同樣是 S192 HANA 大型執行個體 SKU)，您將額外獲得 2 TB 容量的資料磁碟區。 在 option-2 組態中，您會額外獲得 4 TB 的暖資料磁碟區。 相較於熱節點，「暖」擴充節點的記憶體容量全都可以用來儲存資料 (若為 option-1)。 在 option-2 的 SAP HANA 擴充節點組態中，則會有兩倍的記憶體可用於資料磁碟區。
- 您最終會有 3 TB 的容量可供資料使用，且熱資料與暖資料的比率為 1:2 (若為 option-1)。 使用 option-2 的擴充節點組態時，則會有 5 TB 的資料容量和 1:4 的比率。

相較於記憶體的資料磁碟區容量越高，您想要的暖資料越有可能會儲存在磁碟儲存體上。


## <a name="operations-model-and-responsibilities"></a>作業模型和職責

SAP HANA on Azure (大型執行個體) 提供的服務可與 Azure IaaS 服務合作。 您會獲得一個「HANA 大型執行個體」執行個體，其中已安裝針對 SAP HANA 最佳化的作業系統。 就像使用 Azure IaaS VM 時一樣，大多數強化 OS、安裝其他軟體、安裝 HANA、操作 OS 和 HANA，以及更新 OS 和 HANA 的工作，都是由您負責。 Microsoft 不會強制您進行 OS 更新或 HANA 更新。

![SAP HANA on Azure (大型執行個體) 的職責](./media/hana-overview-architecture/image2-responsibilities.png)

如圖所示，SAP HANA on Azure (大型執行個體) 是一個多租用戶的 IaaS 供應項目。 大部分的職責劃分皆以 OS 基礎結構為界。 Microsoft 負責作業系統線以下的所有服務層面。 您則負責該線以上的各個服務層面。 作業系統是您的責任。 您可以繼續使用目前針對合規性、安全性、應用程式管理、基礎與 OS 管理可能採用的大多數內部部署方法。 這些系統在所有方面都會顯得它們彷彿就在您的網路中一樣。

這項服務已針對 SAP HANA 進行最佳化，因此有些部分您需要與 Microsoft 合作，才能使用根本基礎結構功能獲得最佳結果。

以下清單提供有關每一層及您職責的更多詳細資料：

**網路**：執行 SAP HANA 的大型執行個體戳記的所有內部網路。 您的責任包括對儲存體的存取、執行個體之間的連線 (用於相應放大及其他功能)、對架構的連線，以及連線至在 VM 中裝載 SAP 應用程式層的 Azure。 它也包含用於災害復原用途複寫的「Azure 資料中心」之間的 WAN 連線。 所有網路都會依租用戶分割，並已套用服務品質。

**儲存體**：除了用於快照的虛擬化分割儲存體之外，也包括用於 SAP HANA 伺服器所需之所有磁碟區的虛擬化分割儲存體。 

**伺服器**：用以執行指派給租用戶之 SAP HANA DB 的專用實體伺服器。 類型 I 類別 SKU 的伺服器是抽象的硬體。 使用這些類型的伺服器，即會在設定檔中收集和維護伺服器設定，而您可以將其從一個實體硬體移至另一個實體硬體。 透過操作對設定檔進行的這類 (手動) 移動，與 Azure 服務修復有一點類似。 類型 II 類別 SKU 的伺服器不提供這類功能。

**SDDC**：可將資料中心當作一個軟體定義的實體來管理的管理軟體。 它可讓 Microsoft 基於延展、可用性及效能等理由將資源集中存放。

**O/S**：您所選擇在伺服器上執行的 OS (SUSE Linux 或 Red Hat Linux)。 提供給您的 OS 映像，是個別 Linux 廠商提供給 Microsoft 執行 SAP HANA 的映像。 您必須有 Linux 廠商的訂用帳戶，才能取得特定的 SAP HANA 最佳化映像。 向 OS 廠商註冊映像是您的責任。 

自 Microsoft 交付時開始，您即須負責對 Linux 作業系統採取進一步的修補措施。 這項修補包括成功安裝 SAP HANA 可能需要、而特定 Linux 廠商的 SAP HANA 最佳化 OS 映像並未包含的其他套件。 (如需詳細資訊，請參閱 SAP 的 HANA 安裝文件和 SAP 附註)。 

您必須負責執行 OS 的運作問題或最佳化所需的 OS 修補，以及與特定伺服器硬體有關的驅動程式。 OS 的任何安全性或功能性修補，也是您的責任。 

您的責任還包括下列項目的監視和容量規劃︰

- CPU 資源耗用量。
- 記憶體耗用量。
- 與可用空間、IOPS 及延遲相關的磁碟區。
- 「HANA 大型執行個體」與 SAP 應用程式層之間的網路磁碟區流量。

「HANA 大型執行個體」的根本基礎結構提供備份和還原 OS 磁碟區的功能。 使用這項功能也是您的職責所在。

**中介軟體**：主要是「SAP HANA 執行個體」。 管理、操作及監視是您的職責。 您可以利用隨附的功能，使用儲存體快照來進行備份和還原以及災害復原。 這些功能是由基礎結構所提供的。 您的職責還包括使用這些功能來設計高可用性或災害復原、利用它們，以及監視並判斷儲存體快照集是否已順利執行。

**資料**：您受 SAP HANA 管理的資料，以及其他位於磁碟區或檔案共用上的資料 (例如備份檔案)。 您的職責包括監視磁碟可用空間和管理磁碟區上的內容。 您也必須負責監視磁碟區和儲存體快照的備份是否已順利執行。 將資料順利複寫到災害復原網站則是 Microsoft 的職責。

**應用程式**：SAP 應用程式執行個體，或就非 SAP 應用程式而言，則是這些應用程式的應用程式層。 您的責任包括部署、管理、操作及監視這些應用程式。 您必須負責對 CPU 資源耗用量、記憶體耗用量、Azure 儲存體耗用量和虛擬網路內的網路頻寬耗用量進行容量規劃。 您也須負責對資源耗用量進行容量規劃，舉凡虛擬網路到 SAP HANA on Azure (大型執行個體)，皆包含在內。

**WAN**：您為工作負載建立的從內部部署到 Azure 部署的連線。 所有具有 HANA 大型執行個體的客戶都會使用 Azure ExpressRoute 連線。 此連線不是 SAP HANA on Azure (大型執行個體) 解決方案的一部分。 您必須負責此連線的設定。

**封存**：您可能會偏好使用自己的方法在儲存體帳戶中封存資料複本。 封存需要管理、合規性、成本及操作。 您須負責在 Azure 上產生封存複本和備份，並以符合規範的方式加以儲存。

請參閱 [SAP HANA on Azure (大型執行個體) SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)。

## <a name="sizing"></a>調整大小

「HANA 大型執行個體」的大小調整與 HANA 的大小調整大致上沒有差別。 針對您想要就現有的和已部署的系統從其他 RDBMS 移到 HANA 的情況，SAP 提供一些可在您現有 SAP 系統上執行的報告。 如果將資料庫移到 HANA，這些報告就會檢查資料並計算 HANA 執行個體的記憶體需求。 若要取得有關如何執行這些報告和取得其最新修補程式或版本的詳細資訊，請參閱下列 SAP 附註：

- [SAP 附註 #1793345 - SAP Suite on HANA 的大小調整 (英文)](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP 附註 #1872170 - Suite on HANA 與 S/4 HANA 大小調整報告 (英文)](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP 附註 #2121330 - 常見問題集：SAP BW on HANA 大小調整報告](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP 附註 #1736976 - BW on HANA 的大小調整報告](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP 附註 #2296290 - 新的 BW on HANA 大小調整報告](https://launchpad.support.sap.com/#/notes/2296290)

針對嶄新的實作，可使用 SAP Quick Sizer 來計算在 HANA 上實作 SAP 軟體時的記憶體需求。

Hana 的記憶體需求會隨著資料磁碟區擴大而增加。 請留意您目前的記憶體耗用量，以利預測其未來的狀況。 根據記憶體需求，您可以接著將您的需求對應至其中一個「HANA 大型執行個體」SKU。

## <a name="requirements"></a>需求

此清單彙編了執行 SAP HANA on Azure (大型執行個體) 的需求。

**Microsoft Azure**

- 可連結到 SAP HANA on Azure (大型執行個體) 的 Azure 訂用帳戶。
- Microsoft 頂級支援合約。 如需有關在 Azure 中執行 SAP 的特定資訊，請參閱 [SAP 支援附註 #2015553 - Microsoft Azure 上的 SAP：支援的必要條件](https://launchpad.support.sap.com/#/notes/2015553)。 如果您使用 HANA 大型執行個體單位搭配 384 和更多 CPU，您也需要擴充頂級支援合約以包含 Azure 快速回應。
- 在執行 SAP 大小調整演練之後，知道您所需的 HANA 大型執行個體 SKU。

**網路連線**

- 內部部署與 Azure 之間的 ExpressRoute：若要將您的內部部署資料中心連線到 Azure，請務必向您的 ISP 訂購一個至少 1 Gbps 的連線。 

**作業系統**

- SUSE Linux Enterprise Server 12 for SAP Applications 的授權。

   > [!NOTE] 
   > Microsoft 提供的作業系統未向 SUSE 註冊。 它不會連線至 Subscription Management Tool 執行個體。

- 在 Azure 中部署於 VM 上的 SUSE Linux Subscription Management Tool。 此工具提供可讓 SUSE 註冊 SAP HANA on Azure (大型執行個體) 並對其分別進行更新的功能。 (在「HANA 大型執行個體」資料中心內無法存取網際網路)。 
- Red Hat Enterprise Linux for SAP Hana 6.7 或 7.x 的授權。

   > [!NOTE]
   > Microsoft 提供的作業系統未向 Red Hat 註冊。 它不會連線至 Red Hat Subscription Manager 執行個體。

- 在 Azure 中部署於 VM 上的 Red Hat Subscription Manager。 Red Hat Subscription Manager 提供可讓 Red Hat 註冊 SAP HANA on Azure (大型執行個體) 並對其分別進行更新的功能。 (在 Azure 大型執行個體戳記上部署的租用戶內無法進行直接的網際網路存取)。
- SAP 會要求您與 Linux 提供者之間有一份支援合約。 這項需求並不會因「HANA 大型執行個體」解決方案或您在 Azure 中執行 Linux 的事實而消除。 與使用一些 Linux Azure 資源庫映像時不同，服務費用*並未*包含在「HANA 大型執行個體」的解決方案內容中。 您必須負責達到 SAP 對 Linux 散發者之支援合約的相關需求。 
   - 針對 SUSE Linux，請查閱 [SAP 附註 #1984787 - SUSE Linux Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787)和 [SAP 附註 #1056161 - SAP 應用程式的 SUSE 優先支援](https://launchpad.support.sap.com/#/notes/1056161)中的支援合約需求。
   - 針對 Red Hat Linux，您必須擁有包含支援和服務 (HANA 大型執行個體的作業系統更新) 的正確訂用帳戶層級。 Red Hat 建議使用 Red Hat Enterprise Linux for [SAP Solutions] https://access.redhat.com/solutions/3082481 訂用帳戶。 

如需具有不同 Linux 版本之 SAP HANA 版本的支援矩陣，請參閱 [SAP 附註 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。

如需作業系統與 HLI 韌體/驅動程式版本的相容矩陣，請參閱 [HLI 的作業系統升級](os-upgrade-hana-large-instance.md)。


**資料庫**

- SAP HANA (平台或企業版) 的授權和軟體安裝元件。

**應用程式**

- 任何連線至 SAP HANA 及相關 SAP 支援合約之 SAP 應用程式的授權和軟體安裝元件。
- 所使用與 SAP HANA on Azure (大型執行個體) 環境及相關支援合約有關的任何非 SAP 應用程式的授權和軟體安裝元件。

**技能**

- Azure IaaS 及其元件的相關經驗與知識。
- 在 Azure 中部署 SAP 工作負載的相關經驗與知識。
- 經認證的 SAP HANA 安裝人員。
- 可設計以 SAP HANA 為中心之高可用性和災害復原的 SAP 架構師技能。

**SAP**

- 預期您是 SAP 客戶且與 SAP 有支援合約。
- 針對在類型 II 類別的 HANA 大型執行個體 SKU 上的實作，請洽詢 SAP 以取得 SAP HANA 的版本以及大型調升規模硬體的最終設定。


## <a name="storage"></a>儲存體

SAP HANA on Azure (大型執行個體) 的儲存體配置是由傳統部署模型透過 SAP 建議的指引進行設定。 這些指引列載於 [SAP HANA 儲存體需求](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)白皮書中。

類型 I 類別的 HANA 大型執行個體通常隨附存放磁碟區四倍的記憶體磁碟區。 針對類型 II 類別的 HANA 大型執行個體單位，存放裝置將不會超過四倍。 這些單位會隨附一個用來儲存 HANA 交易記錄備份的磁碟區。 如需詳細資訊，請參閱[在 Azure 上安裝和設定 SAP HANA (大型執行個體)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

關於儲存區配置，請參閱下表。 該表格列出了利用不同 HANA 大型執行個體單位提供之不同磁碟區的粗估容量。

| HANA 大型執行個體 SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S192 | 4,608 GB | 1,024 GB | 1,536 GB | 1,024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2,048 GB | 4,096 GB |
| S768m | 28,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4,096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


實際部署的磁碟區可能會因為部署和用來顯示磁碟區大小的工具而不同。

如果您要細分 HANA 大型執行個體 SKU，以下提供一些可能的劃分片段範例：

| 記憶體磁碟分割，以 GB 為單位 | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


這些大小是粗略的磁碟區數目，會根據部署和用來查看磁碟區的工具而有些微不同。 此外還有其他的磁碟分割大小，例如 2.5 TB。 這些儲存體大小會使用上述磁碟分割所使用的類似公式來計算。 「磁碟分割」一詞不表示作業系統、記憶體或 CPU 資源會以任何方式進行磁碟分割。 它表示適用於您可能想要在單一 HANA 執行個體單位上部署之不同 HANA 執行個體的儲存體磁碟分割。 

您可能需要更多儲存體。 您可以購買額外的儲存體 (以 1 TB 為單位)，以增加儲存體。 此額外儲存體可新增為額外的磁碟區。 它也可用來擴充一或多個現有磁碟區。 您無法將磁碟區的大小降低為原始部署的大小以及上述表格中多數記載的大小。 您也無法變更磁碟區的名稱或掛接的名稱。 上述的存放磁碟區已連接至 HANA 大型執行個體單位以作為 NFS4 磁碟區。

您可以使用儲存體快照來進行備份和還原以及災害復原。 如須詳細資訊，請參閱 [Azure 上的 SAP Hana (大型執行個體) 高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如需適用於您的案例的儲存配置詳細資料，請參閱 [HLI 支援案例](hana-supported-scenario.md)。

### <a name="encryption-of-data-at-rest"></a>待用資料加密
用於「HANA 大型執行個體」的儲存體可允許在資料儲存於磁碟上時，對其進行透明加密。 部署 HANA 大型執行個體單位時，您可以啟用這種加密。 您也可以在部署就緒變更為加密磁碟區。 從非加密到已加密磁碟區的轉移過程是透明的，並不需要停機。 

利用類型 I 類別的 SKU，就會將儲存開機 LUN 的磁碟區加密。 對於類型 II 類別的 HANA 大型執行個體 SKU，您需要使用 OS 方法來將開機 LUN 加密。 如需詳細資訊，請連絡 Microsoft 服務管理小組。


## <a name="networking"></a>網路功能

Azure 網路服務的架構是在 HANA 大型執行個體上成功部署 SAP 應用程式的關鍵元件。 一般而言，SAP HANA on Azure (大型執行個體) 部署具有較大的 SAP 架構，其中包含數個擁有不同資料庫大小、CPU 資源耗用量及記憶體使用量的不同 SAP 解決方案。 這些 SAP 系統可能並非全都以 SAP HANA 為基礎。 您的 SAP 環境可能會混用下列項目：

- 部署在內部部署環境中的 SAP 系統。 因大小的緣故，這些系統目前無法裝載在 Azure 中。 例如，如果生產 SAP ERP 系統在 SQL Server (作為資料庫) 上執行，且所需的 CPU 或記憶體資源高於 VM 所能提供，就是如此。
- 部署在內部部署環境中以 SAP HANA 為基礎的 SAP 系統。
- 部署在 VM 中的 SAP 系統。 根據資源耗用量與記憶體需求，這些系統可以是任何可在 Azure (在 VM 上) 順利部署之 SAP NetWeaver 型應用程式的開發、測試、沙箱或生產環境執行個體。 這些系統也可能以 SQL Server 之類的資料庫為基礎。 如需詳細資訊，請參閱 [SAP 支援附註 1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533/E)。 此外，這些系統也可能以 SAP HANA 之類的資料庫為基礎。 如需詳細資訊，請參閱[經 SAP HANA 認證的 IaaS 平台](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。
- 部署在 Azure (在 VM 上) 中並利用「Azure 大型執行個體」戳記中 SAP HANA on Azure (大型執行個體) 的 SAP 應用程式伺服器。

混合式 SAP 架構 (含有四個以上的不同部署案例) 為典型架構。 此外也有許多在 Azure 中完成 SAP 架構執行的客戶案例。 由於 VM 的功能愈來愈強大，將所有 SAP 解決方案都移至 Azure 的客戶數目也隨之增加。

在部署於 Azure 中之 SAP 系統內容中的 Azure 網路並不複雜。 其依循原則如下：

- Azure 虛擬網路必須連線至會連接到內部部署網路的 ExpressRoute 線路。
- 將內部部署連線至 Azure 的 ExpressRoute 線路通常應有 1 Gbps 或更高的頻寬。 此最低頻寬可讓您有足夠的頻寬在內部部署系統與執行於 VM 的系統之間傳輸資料。 它也讓內部部署使用者有足夠的頻寬可連線至 Azure 系統。
- Azure 中的所有 SAP 系統都必須在虛擬網路中設定妥當，才能彼此通訊。
- 裝載於內部部署環境中的 Active Directory 與 DNS 會透過 ExpressRoute 從內部部署環境延伸到 Azure。


> [!NOTE] 
> 從計費的觀點來看，在特定 Azure 區域中，只有一個 Azure 訂用帳戶可連結至大型執行個體戳記中的一個租用戶。 反之，單一大型執行個體戳記租用戶也只能連結至一個 Azure 訂用帳戶。 Azure 中其他可計費的物件也有此需求。

如果將 SAP HANA on Azure (大型執行個體) 部署在多個不同的 Azure 區域中，則會在大型執行個體戳記中部署個別的租用戶。 只要這些執行個體都是相同 SAP 架構的一部分，您便可以在同一個 Azure 訂用帳戶下執行兩者。 

> [!IMPORTANT] 
> 使用 SAP HANA on Azure (大型執行個體) 時，僅支援 Azure Resource Manager 部署。

 

### <a name="additional-virtual-network-information"></a>其他虛擬網路資訊

若要將虛擬網路連線至 ExpressRoute，必須建立 Azure 閘道。 如需詳細資訊，請參閱[關於 ExpressRoute 的虛擬網路閘道](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

Azure 閘道可以與 ExpressRoute 搭配用於 Azure 以外的基礎結構或 Azure 大型執行個體戳記。 Azure 閘道也可用於虛擬網路之間的連線。 如需詳細資訊，請參閱[使用 PowerShell 設定資源管理員的網路對網路連線](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 您可以將 Azure 閘道連線至最多四個不同的 ExpressRoute 連線，只要這些連線來自不同的 Microsoft Enterprise Edge 路由器即可。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> Azure 閘道為這兩個使用案例提供的輸送量是不同的。 如需詳細資訊，請參閱[關於 VPN 閘道](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 在使用 ExpressRoute 連線的情況下，虛擬網路閘道可達到的最大輸送量是 10 Gbps。 在位於虛擬網路中的 VM 與內部部署系統之間複製檔案 (以單一複製串流的形式)，並不會達到不同閘道 SKU 的最大輸送量。 若要使用虛擬網路閘道的完整頻寬，請使用多個資料流。 或者，您必須以單一檔案的平行資料流複製不同的檔案。


### <a name="networking-architecture-for-hana-large-instance"></a>適用於 HANA 大型執行個體的網路架構
適用於 HANA 大型執行個體的網路架構，可區分為四個不同的部分：

- 內部部署網路和 Azure 的 ExpressRoute 連線。 這個部分是客戶網域，會透過 ExpressRoute 連線至 Azure。 請參閱下圖右下方的區域。
- 如先前所討論，Azure 網路服務與同樣也有閘道的虛擬網路搭配使用。 這個部分是您需要針對應用程式需求、安全性及合規性需求尋找適當設計的區域。 是否使用 HANA 大型執行個體，是您在虛擬網路的數目和要選擇的 Azure 閘道 SKU 方面所應考量的另一個要點。 請查看圖中的右上方。
- HANA 大型執行個體透過 ExpressRoute 技術連線至 Azure。 這部分已部署且會由 Microsoft 來處理。 您唯一需要做的，是在將資產部署至 HANA 大型執行個體後提供一些 IP 位址範圍，再將 ExpressRoute 線路連線至虛擬網路。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 
- HANA 大型執行個體中的網路功能，對您而言大部分是透明的。

![連線至 SAP HANA on Azure (大型執行個體) 與內部部署環境的虛擬網路](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

由於您使用 HANA 大型執行個體，因此內部部署資產必須透過 ExpressRoute 連線至 Azure 的需求並不會變更。 以一或多個虛擬網路執行 VM 的需求也不會變更 (這些 VM 會裝載應用程式層，連線至裝載於 HANA 大型執行個體單位中的 HANA 執行個體)。 

與 Azure 中的 SAP 部署的差異為：

- 客戶租用戶的 HANA 大型執行個體單位會透過另一個 ExpressRoute 線路連線至您的虛擬網路。 為了區隔負載狀況，內部部署到虛擬網路 ExpressRoute 連結，以及虛擬網路與 HANA 大型執行個體之間的連結，並不會共用相同的路由器。
- SAP 應用程式層和 HANA 大型執行個體之間的工作負載設定檔是不同性質的許多小型要求與高載，例如從 SAP HANA 到應用程式層的資料傳輸 (結果集)。
- 相較於在內部部署與 Azure 之間交換資料的典型案例，SAP 應用程式架構對於網路延遲更敏感。
- 虛擬網路閘道至少會有兩個 ExpressRoute 連線。 這兩個連線會共用虛擬網路閘道之連入資料的最大頻寬。

在 VM 與 HANA 大型執行個體單位之間經歷的網路延遲，可能大於典型的 VM 對 VM 網路來回延遲。 相依於 Azure 區域，測量到的值可超過 0.7 毫秒的來回延遲，這在 [SAP 附註 #1100926 - 常見問題集：網路效能](https://launchpad.support.sap.com/#/notes/1100926/E)中已歸類為低於平均值。 不過，客戶可順利在 SAP HANA 大型執行個體上部署以 SAP HANA 為基礎的生產環境 SAP 應用程式。 部署的客戶藉由使用 HANA 大型執行個體單位，在 SAP HANA 上執行其 SAP 應用程式，回報表示有很顯著的改善。 請務必在 Azure HANA 大型執行個體中徹底測試您的商務程序。
 
若要提供 VM 和 HANA 大型執行個體之間具決定性的網路延遲，則必須選擇虛擬網路閘道 SKU。 不同於內部部署與 VM 之間的流量模式，VM 和 HANA 大型執行個體之間的流量模式可以開發很小但高載的要求和資料磁碟區來進行傳輸。 為了妥善處理這類高載，強烈建議您使用 UltraPerformance 閘道 SKU。 針對類型 II 類別的 HANA 大型執行個體 SKU，使用 UltraPerformance 閘道 SKU 作為虛擬網路閘道是必要的。

> [!IMPORTANT] 
> 考慮到 SAP 應用程式與資料庫層之間的整體網路流量，因此僅支援使用虛擬網路的 HighPerformance 或 UltraPerformance 閘道 SKU 來連線至 SAP HANA on Azure (大型執行個體)。 針對 HANA 大型執行個體類型 II SKU，僅支援使用 UltraPerformance 閘道 SKU 作為虛擬網路閘道。



### <a name="single-sap-system"></a>單一 SAP 系統

先前顯示的內部部署基礎結構會透過 ExpressRoute 連線至 Azure。 ExpressRoute 線路會連線至 Enterprise Edge 路由器。 如需詳細資訊，請參閱 [ExpressRoute 技術概觀](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 路由在建立後，將會連線至 Microsoft Azure 骨幹，而讓所有 Azure 區域都可供存取。

> [!NOTE] 
> 若要在 Azure 中執行 SAP 架構，請連線至距離 SAP 架構中的 Azure 區域最近的 Enterprise Edge 路由器。 「Azure 大型執行個體」戳記會透過專用 Enterprise Edge 路由器裝置連線，以將 Azure IaaS 中的 VM 與「大型執行個體」戳記之間的網路延遲降到最低。

裝載 SAP 應用程式執行個體之 VM 的虛擬網路閘道會連線至 ExpressRoute 線路。 相同的虛擬網路會連線至專門用來連接到大型執行個體戳記的個別 Enterprise Edge 路由器。

此系統是單一 SAP 系統的簡明範例。 SAP 應用程式層裝載於 Azure 中。 SAP HANA 資料庫執行於 SAP HANA on Azure (大型執行個體) 上。 我們的假設是虛擬網路閘道頻寬 2 Gbps 或 10 Gbps 輸送量並不代表瓶頸。

### <a name="multiple-sap-systems-or-large-sap-systems"></a>多個 SAP 系統或大型 SAP 系統

如果將多個 SAP 系統或大型 SAP 系統部署成連線至 SAP HANA on Azure (大型執行個體)，虛擬網路閘道的輸送量就可能成為瓶頸。 在這種情況下，請將應用程式層分成多個虛擬網路。 針對如下的情況，您也可以建立會連線至 HANA 大型執行個體的特殊虛擬網路：

- 直接從「HANA 大型執行個體」中的 HANA 執行個體備份到 Azure 中裝載 NFS 共用的 VM。
- 將大型備份或其他檔案從「HANA 大型執行個體」單位複製到在 Azure 中管理的磁碟空間。

使用個別的虛擬網路來裝載管理儲存體的 VM。 這種配置可避免在透過虛擬網路閘道 (此閘道可為執行 SAP 應用程式層的 VM 提供服務) 將大型檔案或資料從 HANA 大型執行個體傳輸到 Azure 時造成影響。 

讓網路架構更具彈性：

- 針對單一的較大 SAP 應用程式層，運用多個虛擬網路。
- 相較於將所部署的 SAP 系統結合在相同虛擬網路下的個別子網路中，為這些 SAP 系統中的每一個系統部署一個個別的虛擬網路。

 適用於 SAP HANA on Azure (大型執行個體) 的更有彈性網路架構：

![跨多個虛擬網路部署 SAP 應用程式層](./media/hana-overview-architecture/image4-networking-architecture.png)

此圖顯示透過多個虛擬網路進行部署的 SAP 應用程式層或元件。 此組態會在這些虛擬網路所裝載的應用程式之間進行通訊期間，產生無法避免的延遲額外負荷。 根據預設，在此組態中，位於不同虛擬網路中的 VM 之間的網路流量會透過 Enterprise Edge 路由器來傳送。 若要最佳化並縮短兩個虛擬網路之間的通訊延遲，做法是將相同區域內的虛擬網路對等互連。 即使這些虛擬網路在不同的訂用帳戶中，此方法仍適用。 透過虛擬網路對等互連，兩個不同虛擬網路中 VM 之間的通訊就可以使用 Azure 網路骨幹來彼此直接通訊。 此時會出現延遲狀況，如同 VM 位於相同虛擬網路中。 以透過 Azure 虛擬網路閘道器連線的 IP 位址範圍來定址的流量，將透過虛擬網路的個別虛擬網路閘道器進行路由傳送。 

如需關於虛擬網路對等互連的詳細資訊，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


### <a name="routing-in-azure"></a>Azure 中的路由

SAP HANA on Azure (大型執行個體) 有三項重要的網路路由考量：

* SAP HANA on Azure (大型執行個體) 只能透過 VM 和專用 ExpressRoute 連線來存取，而無法從內部部署環境直接存取。 從內部部署環境對 HANA 大型執行個體單位的直接存取 (如 Microsoft 所提供)，是無法立即執行的。 可轉移的路由會因為目前用於 SAP HANA 大型執行個體的 Azure 網路架構而受限。 有些系統管理用戶端及所有需要直接存取權的應用程式 (例如在內部部署環境中執行的 SAP Solution Manager) 會無法連線至 SAP HANA 資料庫。

* 如果您在兩個不同 Azure 區域中部署了 HANA 大型執行個體單位以供災害復原之用，則系統將會套用相同的暫時性路由限制。 換句話說，HANA 大型執行個體單位在一個區域 (例如美國西部) 內的 IP 位址不會路由至在另一個地區 (例如美國東部) 部署的HANA 大型執行個體單位。 這限制與跨區域使用 Azure 網路對等互連或交叉連接 ExpressRoute 線路 (將 HANA 大型執行個體單位連線至虛擬網路) 無關。 如需以圖形檢視，請參閱「在多個區域中使用 HANA 大型執行個體單位」一節中的圖表。 部署架構所隨附的這項限制，會禁止立即使用 HANA 系統複寫作為災害恢復功能。

* SAP HANA on Azure (大型執行個體) 單位會被指派一個 IP 位址，且該位址來自於您所提交的伺服器 IP 集區為址範圍。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 透過 Azure 訂用帳戶和將虛擬網路連線至 HANA on Azure (大型執行個體) 的 ExpressRoute，即可存取此 IP 位址。 從該伺服器 IP 集區位址範圍指派的 IP 位址會直接指派給硬體單位。 它*不會*再經過 NAT 處理，因為這是此解決方案的第一次部署才會有的情況。 

> [!NOTE] 
> 若要克服前兩個清單項目中所述之暫時性路由的限制，請使用其他元件進行路由。 可用來克服這項限制的元件可能為：

> * 路由資料的反向 Proxy，往返方向皆有可能。 例如，搭配部署在 Azure 中作為虛擬防火牆/流量路由解決方案之「流量管理員」的 F5 BIG-IP、NGINX。
> * 在 Linux VM 中使用 [IPTables 規則](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)，以啟用在內部部署位置和 HANA 大型執行個體單位之間的路由，或不同區域中 HANA 大型執行個體單位之間的路由。

> 請注意，Microsoft 並不提供與協力廠商網路設備或 IPTables 相關自定義解決方案的實作和支援。 必須由所使用元件的廠商或整合者提供支援。 

### <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型執行個體的網際網路連線
HANA 大型執行個體*無法*直接連線至網際網路。 此限制可能會使您的一些能力受到限制，例如，直接向 OS 廠商註冊 OS 映像的能力。 您可能需要使用本機 SUSE Linux Enterprise Server Subscription Management Tool 伺服器或 Red Hat Enterprise Linux Subscription Manager。

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 與 HANA 大型執行個體之間的資料加密
HANA 大型執行個體與 VM 之間的資料傳輸並未加密。 不過，您可以純粹針對 HANA DBMS 端與 JDBC/ODBC 型應用程式之間的交換，啟用流量加密。 如需詳細資訊，請參閱 [SAP 提供的這份文件](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)。

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多個區域中使用 HANA 大型執行個體單位

除了災害復原之外，您可能還有其他理由促使您在多個 Azure 區域中部署 SAP HANA on Azure (大型執行個體)。 也許您想要從部署在這些區域之不同虛擬網路中的每個 VM 存取「HANA 大型執行個體」。 指派給不同 HANA 大型執行個體單位的 IP 位址，不會在透過閘道直接連線的虛擬網路以外傳播至執行個體。 因此，就會在虛擬網路設計中導入些微變更。 虛擬網路閘道可處理四個來自不同 Enterprise Edge 路由器的不同 ExpressRoute 線路。 每個連線至其中一個大型執行個體戳記的虛擬網路，都可以連線至另一個 Azure 區域中的大型執行個體戳記。

![連線至不同 Azure 區域中的 Azure 大型執行個體戳記的虛擬網路](./media/hana-overview-architecture/image8-multiple-regions.png)

此圖顯示兩個區域中的不同虛擬網路如何連線至兩個不同的 ExpressRoute 線路，而這些線路是用來連線至這兩個 Azure 區域中的 SAP HANA on Azure (大型執行個體)。 新導入的連線是矩形的紅線。 透過這些來自虛擬網路的連線，在其中一個虛擬網路中執行的 VM 便可存取部署在這兩個區域中每一個不同的 HANA 大型執行個體單位。 如上圖所示，這是假設您有兩條從內部部署環境連到這兩個 Azure 區域的 ExpressRoute 連線。 基於災害復原的用途，建議您使用此做法。

> [!IMPORTANT] 
> 如果您使用多個 ExpressRoute 線路，就應該在前面加上「AS 路徑」和使用「本機喜好 BGP」設定來確保流量路由正確。


