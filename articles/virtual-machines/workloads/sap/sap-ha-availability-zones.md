---
title: 使用 Azure 可用性區域的 SAP 工作負載設定 | Microsoft Docs
description: 適用於使用 Azure 可用性區域之 SAP NetWeaver 的高可用性架構和案例
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 409a304296d3fdff897a203177e2c150162755c6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746212"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>使用 Azure 可用性區域的 SAP 工作負載設定

Azure 針對改善 Azure 上之 SAP 工作負載的整體可用性，所提供的其中一個高可用性功能是 [Azure 可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)。 可用性區域 (Zone) 已可供在不同的 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/) (Region) 中使用。 該功能於未來將在更多區域中推出。 

SAP 高可用性的基本架構如下圖所示：

![標準 HA 設定](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP 應用程式層會部署在單一 Azure [可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)上。 針對 SAP 中央服務的高可用性，您會在個別的可用性設定組中部署兩部 VM，並使用 Windows 容錯移轉叢集服務或 Pacemaker (Linux) 來部署在發生基礎結構或軟體問題時允許自動容錯移轉的高可用性架構。 描述此類部署的文件如下：

- [使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [使用檔案共用於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SAP NetWeaver 在適用於 SAP 應用程式之 SUSE Linux Enterprise Server 上的 Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [SAP NetWeaver on Red Hat Enterprise Linux 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

類似的架構同樣也適用於 SAP NetWeaver、S/4HANA 或混合式系統的 DBMS 層。 您會搭配容錯移轉叢集解決方案以主動/被動模式部署 DBMS 層，該解決方案會使用 DBMS 特定容錯移轉架構、Windows 容錯移轉叢集服務或 Peacemaker 來在發生基礎結構或軟體失敗時起始容錯移轉活動。 

若要使用 Azure 可用性區域部署相同的架構，便必須對所述的架構做出某些變更。 這些變更將分別在本文件中不同的部分描述。

## <a name="considerations-deploying-across-availability-zones"></a>在可用性區域上進行部署的考量事項

使用可用性區域時有一些考量事項。 考量清單如下：

- Azure 可用性區域 (Zone) 並未保證同一 Azure 區域 (Region) 內的不同可用性區域 (Zone) 之間會相隔一定距離
- Azure 可用性區域並非理想的 DR 解決方案。 在全球某些區域 (Region) 因發生大規模天然災害而導致廣泛損壞 (包括電力基礎結構的重大損壞) 的情況下，不同區域 (Zone) 之間的距離可能無法大到符合作為適當 DR 解決方案的條件
- 在不同 Azure 區域 (Region) 中的不同 Azure 可用性區域 (Zone) 之間的網路延遲，會隨著所在的 Azure 區域 (Region) 而有所不同。 在某些情況下，您可以執行部署在不同區域的 SAP 應用程式層，因為從某個區域到作用中 DBMS VM 的網路延遲，對商務程序所造成的影響尚可以接受。 但在某些 Azure 區域 (Region) 中的案例中，還是會有某個區域 (Zone) 的作用中 DBMS VM 與另一個區域 (Zone) 的 SAP 應用程式執行個體之間的延遲太高，而不適合 SAP 商務程序運作。 因此，部署架構就必須要有適用於應用程式的主動/主動架構，或是適用於區域延遲過高之情況的主動/被動架構。
- 請針對您要將 Azure 可用性區域用於哪一個設定。 根據您在不同區域之間所測量到的網路延遲。 網路延遲在兩個不同的領域中皆扮演重要角色：
    - 兩個需要建立同步複寫的 DBMS 執行個體之間的延遲。 網路延遲越高，影響到您工作負載延展性的機會就越高
    - 在和作用中 DBMS 執行個體相同之區域中執行 SAP 對話執行個體的 VM，以及位於另一個區域中的相似 VM 之間的網路延遲差異。 此差異越大，對商務程序和批次作業之執行階段的影響便越大 (取決於它們是否在和 DBMS 相同的區域中執行)


至於針對 Azure 功能的使用情況，系統對於在不同區域 (Zone) 上部署 Azure VM，以及在相同 Azure 區域 (Region) 內的不同可用性區域 (Zone) 上建立容錯移轉解決方案等情況，則有可使用之功能上的限制。 這些限制如下：

- 必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)才能部署至 Azure 可用性區域 
- 針對實體區域的區域列舉對應是依 Azure 訂用帳戶來決定。 如果您是使用不同的訂用帳戶來部署 SAP 系統，您必須個別為每個訂用帳戶定義理想的區域
- 您無法在某個 Azure 可用性區域內部署 Azure 可用性設定組。 您必須選擇 Azure 可用性區域或 Azure 可用性設定組作為虛擬機器的部署架構。
- 您無法使用 [Azure 基本 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) 來建立以 Windows 容錯移轉叢集服務或 Linux Pacemaker 為基礎的容錯移轉叢集解決方案。 您必須改為使用 [Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-zone-combination"></a>理想的區域組合
若要決定利用可用性區域的方式，您必須執行調查以取得下列資訊：

- 某個 Azure 區域 (Region) 的三個不同區域 (Zone) 之間的網路延遲。 這讓您可以選擇在跨區域網路流量中具有最低網路延遲的區域
- 您所選擇之區域內的 VM 對 VM 延遲，與您所選擇之兩個區域間之網路延遲上的差異
- 指出您必須在可用性區域上部署的 VM 類型，是否在您所選擇的兩個區域中可供使用的陳述式。 特別是 M 系列的虛擬機器，您將會遇到不同的 M 系列 VM SKU 僅在三個區域之中的兩個區域中可供使用的情況

### <a name="network-latency-between-zones-and-within-zone"></a>區域之間及區域內的網路延遲
若要找出不同區域之間的延遲，您必須：

- 在全部三個區域中部署您想要用於 DBMS 執行個體的 VM SKU。 在執行此測量時，請確定已啟用 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) \(英文\)
- 在您尋找具有最低網路延遲的兩個區域時，請在三個可用性區域上，部署您想要作為應用程式層 VM 使用之 VM SKU 的另外三個 VM。 在您所選擇的兩個不同 'DBMS' 區域中，針對兩個 'DBMS VM' 測量網路延遲。 
- 使用 **niping** 作為測量工具。 此為來自 SAP 的工具，其運作方式已於 SAP 支援附註 [#500235](https://launchpad.support.sap.com/#/notes/500235) \(英文\) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) \(英文\) 中詳述。 請專注在 SAP 針對延遲測量所記錄的命令上。 **ping** 不是建議的工具，因為 **ping** 無法在 Azure 加速網路程式碼路徑中運作。

根據測量結果和 VM SKU 在不同區域中的可用情形，您必須做出下列決定：

- 定義適用於 DBMS 層的理想區域
- 決定您是否可以根據區域內或跨區域的網路延遲差異，將作用中的 SAP 應用程式層散發到一個、兩個或全部三個不同的區域上。
- 從應用程式的觀點，決定您是否想要部署主動/被動或主動/主動設定 (請見下文)

做出這些決定，同時參考記錄在 SAP 附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)(英文\) 中的 SAP 網路延遲建議事項。

### <a name="be-aware-of"></a>注意事項

> [!IMPORTANT]
> 您所做的測量和決定皆僅適用於您用來進行這些測量的 Azure 訂用帳戶。 使用另一個 Azure 訂用帳戶時，您必須重複這些測量，因為您訂用帳戶相依區域列舉的對應，在使用不同訂用帳戶時可能會出現變更


> [!IMPORTANT]
> 上面所執行的測量，在所有支援[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview) (Zone) 的 Azure 區域 (Region) 中預期都會顯示不同的結果。 即使您針對網路延遲的需求沒有變更，您在不同的 Azure 區域 (Region) 中可能也需要採用不同的部署策略，因為不同區域 (Zone) 之間的網路延遲可能會有所不同。 我們預期在某些 Azure 區域 (Region) 中，三個不同區域 (Zone) 之間的網路延遲可能會大幅不同。 而在其他區域 (Region) 中，三個不同區域 (Zone) 之間的網路延遲則為保持一致。 區域之間**一律**會存在 1 毫秒至 2 毫秒網路延遲的說法，是**錯誤**的。 Azure 區域 (Region) 中可用性區域 (Zone) 之間的網路延遲是無法一般化的。


## <a name="activeactive-deployment"></a>主動/主動部署
此部署架構稱為主動/主動，因為您會在二到三個區域上部署作用中的 SAP 對話執行個體。 使用加入佇列複寫的 SAP 中央服務會被部署到兩個區域之間。 DBMS 層也是如此，其會被部署到和 SAP 中央服務相同的區域上。

若要思考此類設定，您必須在您的區域 (Region) 中尋找能針對您的工作負載和同步 DBMS 複寫提供可接受之跨區域 (Zone) 網路延遲的兩個可用性區域 (Zone)。 此外，您所選取之區域內的網路延遲，以及跨區域網路延遲之間的差異也不能太大。 後者的原因在於，商務程序或批次程序的執行階段應該不能有太大的差異 (取決於作業是搭配 DBMS 伺服器以區域內的方式執行，或是以跨區域的方式執行)。 一些差異上可接受，但不能到達差的因數。

跨兩個區域之主動/主動部署的簡化結構描述如下所示：

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

此設定需要考慮下列事項：

- 您會將 Azure 可用性區域視為所有 VM 的容錯和更新網域，因為可用性設定組無法被部署到 Azure 可用性區域中
- 您針對 SAP 中央服務和 DBMS 層的容錯移轉叢集的所使用的 Azure 負載平衡器，必須是 [標準 SKU 負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本負載平衡器將無法跨區域運作
- 您部署來裝載 SAP 系統的 Azure 虛擬網路及其子網路會擴展到所有區域上。 您**不需要**針對每個區域準備個別的虛擬網路
- 請針對所有部署的虛擬機器使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署
- Azure 進階儲存體或 [Ultra SSD 儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支援跨區域進行任何類型的儲存體複寫。 因此，應用程式 (DBMS 或 SAP 中央服務) 必須負責複寫重要的資料
- 這對於共用的 sapmnt 目錄也相同，其為共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用能複寫此類共用磁碟或在區域之間進行共用的技術。 目前已支援的技術如下：
    - 針對 Windows，支援將使用 SIOS Datakeeper 的叢集解決方案 (如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)中所述) 用於跨區域案例
    - 針對 SUSE Linux，支援依[適用於 SUSE Linux Enterprise Server 之 Azure VM 上 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述的方式建置的 NFS 共用
    - 目前，使用 Windows 相應擴大檔案服務 (SOFS) (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述) **並不支援用於跨區域案例**
- 第三個區域是用來在您建置 [SUSE Linux pacemaker 叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或額外應用程式執行個體的情況下裝載 SBD 裝置
- 若要針對某些關鍵商務交易和/或作業達到執行階段一致性。 您可以嘗試使用 SAP 批次伺服器群組、登入群組或 RFC 群組，將特定批次作業和使用者直接導向至位於與作用中 DBMS 執行個體相同區域中的特定應用程式執行個體。 不過，在發生區域容錯移轉的情況下，您必須手動將這些群組移至對話執行個體 (其位於剩餘的區域中) 
- 決定您是否要在每個區域中部署一些休眠對話執行個體，以在您部署部分應用程式執行個體的區域服務中斷的情況下，能夠立即取得先前的資源容量


## <a name="activepassive-deployment"></a>主動/被動部署
在您無法於單一區域內和跨區域網路流量上的網路延遲之間取得可接受差異的情況下，您所能部署的架構，從 SAP 應用程式層的觀點來看，會具有主動/被動的特性。 您會定義「作用中」的區域，其為您部署完整應用程式層，以及您嘗試執行作用中 DBMS 執行個體和作用中 SAP 中央服務執行個體的區域。 在這種設定中，您可以確保商務交易和批次作業中不會有極端的執行階段差異 (取決於作業是否在和作用中 DBMS 執行個體相同的區域中執行)。

此類架構的基本配置如下所示：

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

此設定需要考慮下列事項：

- 可用性設定組不能部署到 Azure 可用性區域。 因此，在此情況下，您的應用程式層會有一個更新網域和一個容錯網域。 原因是它只會被部署到單一區域。 此設定與參考架構相比稍微慢了一些，因為參考架構能預見您將應用程式層部署到 Azure 可用性設定組。
- 操作此類架構時，您必須緊密監視並嘗試將作用中的 DBMS 和 SAP 中央服務執行個體維持在和已部署應用程式層相同的區域中。 在 SAP 中央服務或 DBMS 執行個體發生容錯移轉的情況下，您必須確保自己可以儘快手動容錯回復到部署 SAP 應用程式層的區域中
- 您針對 SAP 中央服務和 DBMS 層的容錯移轉叢集的所使用的 Azure 負載平衡器，必須是 [標準 SKU 負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本負載平衡器將無法跨區域運作
- 您部署來裝載 SAP 系統的 Azure 虛擬網路及其子網路會擴展到所有區域上。 您**不需要**針對每個區域準備個別的虛擬網路
- 針對所有部署的虛擬機器，您必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署
- Azure 進階儲存體或 [Ultra SSD 儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支援跨區域進行任何類型的儲存體複寫。 因此，應用程式 (DBMS 或 SAP 中央服務) 必須負責複寫重要的資料
- 這對於共用的 sapmnt 目錄也相同，其為共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用能複寫此類共用磁碟或在區域之間進行共用的技術。 目前已支援的技術如下：
    - 針對 Windows，支援將使用 SIOS Datakeeper 的叢集解決方案 (如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)中所述) 用於跨區域案例
    - 針對 SUSE Linux，支援依[適用於 SUSE Linux Enterprise Server 之 Azure VM 上 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述的方式建置的 NFS 共用
    - 目前，使用 Windows 相應擴大檔案服務 (SOFS) (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述) **並不支援用於跨區域案例**
- 第三個區域是用來在您建置 [SUSE Linux pacemaker 叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或額外應用程式執行個體的情況下裝載 SBD 裝置
- 將休眠 VM 部署到 (DBMS 觀點的) 被動區域，以在發生區域失敗時啟動應用程式資源
    - 您不能使用 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 來將作用中 VM 跨區域複寫到休眠 VM。 目前，Azure Site Recovery 並無法達成此功能
- 投資可讓您 (在發生區域失敗的情況下) 自動啟動次要區域中之 SAP 應用程式層的自動化

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>結合高可用性和災害復原的設定
雖然 Microsoft 並沒有針對在特定 Azure 區域 (Region) 中裝載不同 Azure 可用性區域 (Zone) 之設施之間的地理距離提供任何資訊，有些客戶則開始將區域運用於結合的 HA 和 DR 設定，其保證能提供值為零的復原點目標 (RPO)。 這代表您不會失去任何已認可的資料庫交易，即便在災害復原的案例下也一樣。 

> [!NOTE]
> 此類設定僅建議用於特定情況。 例如，資料因安全性和合規性因素而不能離開 Azure 區域的情況。 

此類設定的範例如下圖所示：

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

此設定需要考慮下列事項：

- 您可能是假設裝載可用性區域的設施之間具有顯著的距離。 或是您被迫待在特定的 Azure 區域中。 可用性設定組不能部署到 Azure 可用性區域。 因此，在此情況下，您的應用程式層會有一個更新網域和一個容錯網域。 原因是它只會被部署到單一區域。 這與參考架構相比稍微慢了一些，因為參考架構能預見您將應用程式層部署到 Azure 可用性設定組。
- 操作此類架構時，您必須緊密監視並嘗試將作用中的 DBMS 和 SAP 中央服務執行個體維持在和已部署應用程式層相同的區域中。 在 SAP 中央服務或 DBMS 執行個體發生容錯移轉的情況下，您必須確保自己可以儘快手動容錯回復到部署 SAP 應用程式層的區域中
- 您在執行作用中 QA 應用程式執行個體的 VM 中預先安裝生產環境應用程式執行個體。
- 在發生區域失敗的情況下，您會關閉 QA 應用程式執行個體，並改為啟動生產環境執行個體。 請記住，您需要處理應用程式執行個體的虛擬名稱才能達成此目的
- 您針對 SAP 中央服務和 DBMS 層的容錯移轉叢集的所使用的 Azure 負載平衡器，必須是 [標準 SKU 負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本負載平衡器將無法跨區域運作
- 您部署來裝載 SAP 系統的 Azure 虛擬網路及其子網路會擴展到所有區域上。 您**不需要**針對每個區域準備個別的虛擬網路
- 針對所有部署的虛擬機器，您必須使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。 非受控磁碟不支援區域部署
- Azure 進階儲存體或 [Ultra SSD 儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支援跨區域進行任何類型的儲存體複寫。 因此，應用程式 (DBMS 或 SAP 中央服務) 必須負責複寫重要的資料
- 這對於共用的 sapmnt 目錄也相同，其為共用磁碟 (Windows)、CIFS 共用 (Windows) 或 NFS 共用 (Linux)。 您必須使用能複寫此類共用磁碟或在區域之間進行共用的技術。 目前已支援的技術如下：
    - 針對 Windows，支援將使用 SIOS Datakeeper 的叢集解決方案 (如[在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)中所述) 用於跨區域案例
    - 針對 SUSE Linux，支援依[適用於 SUSE Linux Enterprise Server 之 Azure VM 上 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述的方式建置的 NFS 共用
    - 目前，使用 Windows 相應擴大檔案服務 (SOFS) (如[使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述) **並不支援用於跨區域案例**
- 第三個區域是用來在您建置 [SUSE Linux pacemaker 叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或額外應用程式執行個體的情況下裝載 SBD 裝置





## <a name="next-steps"></a>後續步驟
查看跨 Azure 可用性區域進行部署的後續步驟：

- [在 Azure 中使用叢集共用磁碟於 Windows 容錯移轉叢集上進行 SAP ASCS/SCS 執行個體叢集處理](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [使用 SAP ASCS/SCS 執行個體的 Windows 容錯移轉叢集和檔案共用，為 SAP 高可用性準備 Azure 基礎結構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






