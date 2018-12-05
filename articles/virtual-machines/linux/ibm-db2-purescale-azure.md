---
title: Azure 上的 IBM Db2 pureScale
description: 在此文章中，我們會顯示用於在 Azure 上執行 IBM Db2 pureScale 環境的架構。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 5a7ae96a3730df2d0dae04dde6d7609ce69cdee5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977994"
---
# <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM Db2 pureScale

IBM Db2 pureScale 環境提供適用於 Azure 的資料庫叢集解決方案，此解決方案可在 Linux 作業系統上提供高可用性與延展性。 在此文章中，我們會顯示用於在 Azure 上執行 Db2 pureScale 的架構。

## <a name="overview"></a>概觀

企業長久以來都使用傳統關聯式資料庫管理系統 (RDBMS) 平台來滿足線上交易處理 (OLTP) 需求。 如今，有許多企業都已將其大型主機型資料庫環境移轉到 Azure 做為延伸處理能力、降低成本及維持穩定之作業成本結構的方式。

移轉通常是將傳統平台現代化的第一個步驟。 例如，一個企業最近將其在 z/OS 上的 IBM Db2 環境改交由 Azure 上的 IBM Db2 pureScale 代管。 Linux 上的 IBM Db2 pureScale 雖然與原始環境不同，但針對在大型主機上之 Parallel Sysplex 設定中執行的 z/OS 提供與 IBM Db2 類似的高可用性與延展性。

此文章說明用於此 Azure 移轉的架構。 我們使用 Red Hat Linux 7.4 來測試設定。 您可以從 Azure Marketplace 取得此版本。 選擇 Linux 發行版本之前，請務必確認目前支援的版本。 如需詳細資訊，請參閱 [IBM Db2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) 與 [GlusterFS](https://docs.gluster.org/en/latest/) 文件 \(英文\)。

此文章只是您 Db2 實作計畫的起點。 您的商業需求將會不同，但相同的模式皆適用。 此架構模式也可以用於 Azure 上的線上分析處理 (OLAP) 應用程式。

此文章不涵蓋從適用於 z/OS 的 IBM Db2 資料庫移轉到 Linux 上執行之 IBM Db2 pureScale 的差異與可能的移轉工作。 它也不提供從 Db2 z/OS 移轉到 Db2 pureScale 的對等大小估計與工作負載分析。 為協助您判斷適用於您環境的最佳 Db2 pureScale 架構，我們強烈建議您完成完整大小估計練習並建立假說。 在其他因素之間，在來源系統上，務必考慮 Db2 z/OS Parallel Sysplex 搭配資料共用架構 (Data Sharing Architecture)、耦合工具 (Coupling Facility) 與 DDF 使用狀況統計資料。

> [!NOTE]
> 此文章說明進行 Db2 移轉的其中一個方法，但還有其他方法。 例如，Db2 pureScale 也可以在虛擬內部部署環境中執行。 IBM 在各種設定中支援 Microsoft Hyper-V 上的 Db2。 如需詳細資訊，請參閱 IBM 知識中心中的 [Db2 pureScale 虛擬化架構](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) \(英文\)。

## <a name="architecture"></a>架構

為在 Azure 上支援高可用性與延展性，可以為 Db2 pureScale 使用相應放大的共用資料結構。 我們為我們的客戶移轉使用下列範例架構。

![](media/db2-purescale-on-azure/pureScaleArchitecture.png "Azure 虛擬機器上的Db2 pureScale，其中顯示儲存與網路功能")


架構圖說明 Db2 pureScale 叢集所需的邏輯層。 這些包括適用於用戶端、適用於管理、適用於快取、適用於資料庫引擎與適用於共用儲存體的虛擬機器。 除了資料庫引擎節點之外，該圖也包括用於稱為叢集快取工具 (CF) 的兩個節點。 我們為資料庫引擎本身使用最少兩個節點；屬於 pureScale 叢集的 Db2 伺服器稱為成員。 叢集透過 iSCSI 連線到三個節點的 GlusterFS 共用儲存體叢集以提供相應放大的儲存體與高可用性。 Db2 pureScale 是安裝在執行 Linux 的 Azure 虛擬機器上。

此方法只是一個您可以修改以符合您組織所需之大小與規模的範本，而且是以下列項目為基礎：

-   兩個或多個資料庫成員會與至少兩個 CF 節點合併，這兩個節點會管理全域緩衝區集區 (GBP)，以讓共用記憶體與全域鎖定管理員 (GLM) 服務控制共用存取並鎖定來自多個作用中成員的內容。 一個 CF 節點做為主要，而另一個節點做為次要容錯移轉 CF 節點。 為避免建立具有單一失敗點的環境，Db2 pureScale 叢集至少需要四個節點。

-   高效能共用儲存體 (在圖 1 中以 P30 大小顯示)，這將由每個 Gluster FS 節點使用。

-   適用於資料成員與共用儲存體的高效能網路功能。

### <a name="compute-considerations"></a>計算考量

此架構會在 Azure 虛擬機器上執行應用程式、儲存體與資料層。 [部署安裝程式指令碼](http://aka.ms/db2onazure)會建立下列項目：

-   Db2 pureScale 叢集。 您在 Azure 上需要的計算資源類型取決於您的安裝程式。 一般而言，您可以使用下列兩種方式：

    -   使用多節點、高效能計算 (HPC) 樣式的網路，在這裡，多個小型到中型執行個體會存取共用儲存體。 針對設定的此 HPC 類型，Azure 記憶體最佳化 E 系列或儲存體最佳化 L 系列[虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)提供所需的計算資源。

    -   為資料引擎使用較少的大型虛擬機器執行個體。 針對大型執行個體，最大的記憶體最佳化 [M 系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)虛擬機器適用於繁重的記憶體內工作負載，但視用來執行 Db2 的邏輯分割區 (LPAR) 而定，可能需要專屬執行個體。

-   Db2 CF 使用記憶體最佳化虛擬機器，例如 E 系列或 L 系列。

-   GlusterFS 儲存體使用執行 Linux 的標準 \_DS4\_v2 虛擬機器。

-   GlusterFS jumpbox 使用執行 Linux 的標準 \_DS2\_v2 虛擬機器。

-   用戶端是執行 v 的標準 \_DS3\_v2 虛擬機器 (用於測試)。

-   見證伺服器是執行 Linux 的標準 \_DS3\_v2 虛擬機器 (用於 Db2 pureScale)。

> [!NOTE]
> 在 Db2 pureScale 叢集中，至少要有兩個 Db2 執行個體。 也需要「快取」執行個體與「鎖定管理員」執行個體。

### <a name="storage-considerations"></a>儲存體考量

就像 Oracle RAC 一樣，Db2 pureScale 是高效能區塊 I/O、相應放大資料庫。 我們建議您使用符合您需求的的最大可用 [Azure 進階儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)。 例如，較小的儲存體選項可能適用於開發及測試環境，而生產環境通常需要較大的儲存體容量 範例架構因為 [P30](https://azure.microsoft.com/pricing/details/managed-disks/) 的 IOPS 速率與大小和價格的原因而使用它。 不論大小為何，使用進階儲存體都能獲得最佳效能。

Db2 pureScale 使用共用所有項目架構，其中資料可供所有叢集節點存取。 進階儲存體必須在多個執行個體之間共用，不論是透過隨選方式或專屬執行個體方式。

大型的 Db2 pureScale 叢集可以要求 200 TB 或更高的進階共用儲存體 (具有 100,000 的 IOPS)。 Db2 pureScale 支援可在 Azure 上使用的 iSCSI 區塊介面。 iSCSI 介面需要共用儲存體叢集，這可使用 GlusterFS、S2D 或另一個工具來實作。 此類型的解決方案會在 Azure 中建立虛擬儲存區域網路 (vSAN) 裝置。 Db2 pureScale 會使用 vSAN 來安裝用來在多部虛擬機器之間共用資料的叢集檔案系統。

針對此架構，我們使用 GlusterFS 檔案系統，這是免費、可調整規模、開放原始碼分散式檔案系統，此檔案系統已特別針對雲端儲存體最佳化。

### <a name="networking-considerations"></a>網路考量

IBM 建議為 Db2 pureScale 叢集中的所有成員使用 InfiniBand 網路功能；Db2 pureScale 也會在可能的情況下為使用的 CF 使用遠端直接記憶體存取 (RDMA)。

在設定期間，會建立一個 Azure [資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)以包含所有虛擬機器。 一般而言，會根據資源的存留期以及將管理資源的人員來將資源組成群組。 此架構中的虛擬機器需要[加速網路功能](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)，這個 Azure 功能可透過單一根目錄 I/O 虛擬化 (SR-IOV) 提供一致的超低網路延遲給虛擬機器。

每部 Azure 虛擬機器都是部署到分段為多個子網路的虛擬網路中：主要、Gluster FS 前端 (gfsfe)、Gluster FS 後端 (bfsbe)、Db2 pureScale (db2be) 與 Db2 purescale 前端 (db2fe)。 安裝指令碼也會在主要子網路中的虛擬機器上建立主要 [NIC](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)。

[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) 會用來限制虛擬網路內的網路流量並隔離不同的子網路。

在 Azure 上，Db2 pureScale 必須使用 TCP/IP 做為儲存體的網路連線。

## <a name="next-steps"></a>後續步驟

-   [在 Azure 上部署此架構](deploy-ibm-db2-purescale-azure.md)
