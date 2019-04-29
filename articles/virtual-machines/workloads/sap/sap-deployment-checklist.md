---
title: SAP 工作負載規劃和部署檢查清單 | Microsoft Docs
description: Azure 上 SAP 工作負載的規劃和部署檢查清單
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fef2d42282291bb0ea6afeea03e60234d3d47a4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648783"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Azure 上 SAP 工作負載的規劃和部署檢查清單 

此檢查清單的適用對象是要將其 SAP NetWeaver、S/4HANA 及 Hybris 應用程式移至「Azure 基礎結構即服務」的客戶。  客戶和/或 SAP 合作夥伴應該在專案持續期間檢閱此檢查清單。 請注意，許多檢查都是在專案開始時及規劃階段進行的。 在部署完成後，對已部署的 Azure 基礎結構或 SAP 軟體版本進行基礎變更會變得複雜。 請在整個專案的重大里程碑檢閱此檢查清單。  如此便可在小問題變成大問題之前，及時偵測到這些問題，並有充分的時間來重新設計及測試任何必要的變更。 此清單絕不保證完整。 視您個人的情況而定，可能必須進行許多檢查。 

這份組合的檢查清單並不包含與 Azure 無關的工作。  範例：SAP 應用程式在移至「Azure 公用雲端」或移至主機服務提供者期間的介面變更。    

此檢查清單也可用於已經部署的系統。 新功能 (例如「寫入加速器」、「可用性區域」及新的 VM 類型) 可能從您部署時便已新增。  因此，定期檢閱檢查清單以確保掌握 Azure 平台新功能訊息，會相當有幫助。 

## <a name="project-preparation-and-planning-phase"></a>專案準備和規劃階段
在這個階段，會規劃將 SAP 工作負載移轉至 Azure 公用雲端。 所討論及定義的一組最基本實體和項目清單如下：

1. 概要設計文件 – 本文件應該包含：
    1. Azure 上目前的 SAP 元件和應用程式清查，以及目標應用程式清查
    2. 建立並使用責任指派矩陣 (RACI)，以定義各個相關合作夥伴的責任和指派。 從規劃和第一個部署的最高層級開始，然後往愈來愈細微的層級進行
    2. 概要的解決方案架構
    3. 關於 Azure 部署區域的決策。 如需 Azure 區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。 如需了解每個 Azure 區域中可用的服務，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)一文
    4. 網路從內部部署連線至 Azure 的架構。 請開始熟悉 [Azure 的虛擬資料中心藍圖](https://docs.microsoft.com/azure/architecture/vdc/)
    5. 在 Azure 中執行高業務影響性資料的安全性原則。 如需閱讀相關資料，請從 [Azure 安全性文件](https://docs.microsoft.com/azure/security/)開始
2.  技術設計文件 – 其中包含：
    1.  解決方案區塊圖 
    2.  Azure 中計算、儲存體及網路元件的大小。 如需調整 Azure VM 的 SAP 大小，請參閱 SAP 支援附註 [#1928533](https://launchpad.support.sap.com/#/notes/1928533) \(英文\) 
    3.  商務持續性和災害復原架構
    4.  詳細的 OS、DB、核心及 SAP 支援套件版本。 並不假設 SAP NetWeaver 或 S/4HANA 所支援的任何 OS 版本在 Azure VM 中都受到支援。 此情況也同樣適用於 DBMS 版本。 下列來源必須經過檢查並視需要升級：SAP 版本、DBMS 版本或 OS 版本，以便符合及處於 SAP 和 Azure 支援的範圍。 您必須在 SAP 和 Azure 支援的版本組合內，才能獲得 SAP 和 Microsoft 的完整支援。 必要時，您必須為升級某些軟體元件做規劃。 如需有關所支援 SAP、OS 及 DBMS 軟體的詳細資料，請參閱下列位置所記載的內容：
        1.  SAP 支援附註 [#1928533](https://launchpad.support.sap.com/#/notes/1928533) \(英文\)。 此附註定義了 Azure VM 中支援的最低 OS 版本。 此外，它也定義了大多數非 HANA 資料庫所需的最低資料庫版本。 此附註也顯示各種不同支援 SAP 之 Azure VM 類型的 SAP 大小。
        2.  SAP 支援附註 [#2039619](https://launchpad.support.sap.com/#/notes/2039619) \(英文\)。 此附註定義了 Azure 上的 Oracle 支援對照表。 請注意，在適用於 SAP 的 Azure 工作負載中，Oracle 僅支援以 Windows 和 Oracle Linux 作為客體 OS。 這個支援聲明也適用於執行 SAP 執行個體的 SAP 應用程式層。 不過，在 Oracle Linux 中，Oracle 並不是透過 Pacemaker 支援 SAP Central Services 的高可用性。 如果您需要 Oracle Linux 上的 ASCS 高可用性，將需要利用適用於 Linux 的 SIOS 保護套件。 如需詳細的 SAP 認證資料，請參閱 SAP 支援附註[#1662610 - 適用於 Linux 的 SIOS 保護套件的支援詳細資料](https://launchpad.support.sap.com/#/notes/1662610)。 針對 Windows，Windows 容錯移轉叢集的容錯移轉解決方案可由 SAP 支援且適用於 SAP Central Services，並可與作為 DBMS 層的 Oracle 搭配使用。 
        3.  SAP 支援附註 [#2235581](https://launchpad.support.sap.com/#/notes/2235581) \(英文\) 提供各種不同 OS 版本上 SAP HANA 的支援對照表
        4.  支援 SAP HANA 的 Azure VM 和 [HANA 大型執行個體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 皆列在[這裡](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [SAP 產品可用性對照表](https://support.sap.com/en/) \(英文\)
        6.  適用於其他 SAP 特定產品的 SAP 附註  
    5.  建議您針對 SAP 生產環境系統採用嚴格的 3 層式設計。 不建議在同一個 VM 上結合使用 ASCS + APP 伺服器。  在 Azure 上搭配 Windows 作為客體 OS 時，支援使用 SAP Central Services 的多重 SID 叢集設定。 不過，在 Azure 上搭配 Linux 作業系統時，則不支援 SAP Central Services 多重 SID 叢集設定。 如需 Windows 客體 OS 案例的相關文件，請參閱：
        1.  [在 Azure 上搭配 Windows Server 容錯移轉叢集和共用磁碟的 SAP ASCS/SCS 執行個體多重 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [在 Azure 上搭配 Windows Server 容錯移轉叢集和檔案共用的 SAP ASCS/SCS 執行個體多重 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  高可用性和災害復原架構
        1.  根據 RTO 和 RPO 定義高可用性和災害復原架構架構應有的樣貌
        2.  針對相同時區內的高可用性，請檢查所需的 DBMS 在 Azure 中必須提供的功能。 大多數 DBMS 都會提供同步熱待命伺服器的同步方法，這也是我們針對生產環境系統建議採用的方法。 也請查看 SAP 相關文件來了解不同資料庫，請從[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)及相關文件開始查看
            1.  若 Windows 容錯移轉叢集服務搭配使用適用於 DBMS 層的共用磁碟組態 (如[此處](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)所述的 SQL Sever 狀況)，則「無法」受到支援。 替代解決方案：
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle 資料保護](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [HANA 系統複寫](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  針對跨不同 Azure 區域的災害復原，請檢查不同 DBMS 廠商所提供的可能性。 這些廠商大多數都支援非同步複寫或記錄傳送
        4.  針對 SAP 應用程式層，請定義您是要在相同的 Azure 區域中還是 DR 區域中，執行業務迴歸測試系統 (這些最好是您生產環境部署的複本)。 在後者的情況下，您可以將該業務迴歸系統作為您生產環境的目標
        5.  如果您決定不要在 DR 網站中放置非生產環境系統，請研究如何以 Azure Site Recovery 作為將 SAP 應用程式層複寫至 Azure DR 區域的可行方法。 另請參閱[設定多層式 SAP NetWeaver 應用程式部署的災害復原](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  如果您決定使用運用 [Azure 可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)的組合式 HA/DR 設定，請熟悉可提供「可用性區域」的 Azure 區域，並熟悉可能因兩個「可用性區域」間的網路延遲變長而造成的限制  
3.  客戶/合作夥伴應該建立一份所有 SAP 介面 (SAP 和非 SAP) 的清查。 
4.  基礎服務設計的設計 - 此設計會包含的項目如下
    1.  Active Directory 和 DNS 設計
    2.  Azure 內的網路拓撲和不同 SAP 系統的指派
    3.  適用於管理 Azure 中基礎結構和 SAP 應用程式之各個不同小組的[角色型存取](https://docs.microsoft.com/azure/role-based-access-control/overview)結構
    3.  資源群組拓撲 
    4.  [標記策略](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  VM 的命名慣例及其他基礎結構元件和/或邏輯名稱
5.  Microsoft 頂級支援合約 – 識別 MS 技術支援專案經理 (TAM)。 如需了解 SAP 的支援需求，請參閱 SAP 支援附註 [#2015553](https://launchpad.support.sap.com/#/notes/2015553) \(英文\) 
6.  定義 Azure 訂用帳戶數目和不同訂用帳戶的核心配額。 請視需要[開啟支援要求以增加 Azure 訂用帳戶的配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) 
7.  將 SAP 資料移轉至 Azure 的資料減量和資料移轉計劃。 針對 SAP NetWeaver 系統，SAP 具有關於如何限制大量資料之數量的指導方針。 SAP 發佈了[這份深入指南](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) \(英文\)，說明 SAP ERP 系統中的資料管理。 不過，有些內容確實適用於大部分 NetWeaver 和 S/4HANA 系統。
8.  定義並決定自動化部署方法。 Azure 上基礎結構部署背後的自動化目標是要以決定性的方式進行部署，並獲得決定性的結果。 許多客戶都使用 Power Shell 或 CLI 型指令碼。 但有各式各樣的開放原始碼技術可用來部署適用於 SAP 的 Azure 基礎結構，甚至是安裝 SAP 軟體。 可以在 GitHub 中找到範例：
    1.  [Azure Cloud 雲端中的自動化 SAP 部署](https://github.com/Azure/sap-hana) \(英文\)
    2.  [SAP HANA 安裝](https://github.com/AzureCAT-GSI/SAP-HANA-ARM) \(英文\)
9.  定義您 (客戶)、系統整合者、Microsoft 及其他相關對象之間定期的設計和部署檢閱步調

 
## <a name="pilot-phase-strongly-recommended"></a>試驗階段 (強烈建議)
 
試驗可以在專案規劃和準備之前或以平行方式執行。 此階段也可用來測試在規劃和準備階段中所制定的方法和設計。 試驗階段可延展至真實的概念證明。 建議您在試驗部署期間，除了安全性設計之外，也設定及驗證完整的 HA/DR 解決方案。 在某些客戶案例中，也可以在這個階段進行延展性測試。 其他客戶則會使用 SAP 沙箱系統的部署作為試驗階段。 因此，我們會假設您已識別出要移轉至 Azure 以執行試驗的系統。

1. 將對 Azure 進行的資料傳輸最佳化。 高度依賴透過客戶的情況下傳輸[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)內部部署的最快，如果 「 Express 」 電路有足夠的頻寬。 就其他客戶而言，則透過網際網路更為快速
2. 如果是涉及匯出和匯入資料庫資料的 SAP 異質平台移轉，請對匯出和匯入階段進行測試和最佳化。 針對涉及以 SQL Server 作為目的地平台的大型移轉，可在[這裡](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070) \(英文\) 找到相關建議。 當您將移轉與 SAP 版本升級合併，並依據記載 (例如 [SUM 2.0 SP03 的資料庫移轉選項 (DMO)](https://launchpad.support.sap.com/#/notes/2631152)(英文\)) 滿足特定來源和目標 DBMS 平台組合時，如果不需要合併的版本升級或 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 程序，您可以採用 Migration Monitor/SWPM 方法。 
   1.  匯出至來源、將匯出檔案上傳至 Azure 及匯入效能。  將匯出與匯入之間的重疊最大化
   2.  評估目標與目的地平台之間資料庫的磁碟區，以便反映在基礎結構的大小調整    
   3.  對時機進行驗證和最佳化 
3. 技術驗證 
   1. VM 類型
      1.  再次驗證 SAP 支援附註、SAP HANA 硬體目錄及 SAP PAM 上的資源，以確定在針對 Azure 支援的 VM、針對這些 VM 類型支援的 OS 版本，以及支援的 SAP 和 DBMS 版本方面，沒有任何變更
      2.  再次驗證您在 Azure 上部署之應用程式和基礎結構的大小。 如果是移動目前的應用程式，您通常可以從所使用的基礎結構和 [SAP 效能評定網頁](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) \(英文\) 衍生所需的 SAPS，然後將其與 SAP 支援附註 [#1928533](https://launchpad.support.sap.com/#/notes/1928533) \(英文\) 中所列的 SAPS 編號做比較。 此外，也請記住[這篇文章](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
      3.  評估和測試與您在規劃階段中所選不同 VM 類型的最大儲存體輸送量及網路輸送量相關的 Azure VM 大小。 下列位置提供資料：
          1.  [Azure 中 Windows 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 調整大小時，請務必考慮**最大未快取磁碟輸送量**
          2.  [Azure 中的 Linux 虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。調整大小時，請務必考慮**最大未快取磁碟輸送量**
   2. 儲存體
      1.  使用[Azure 標準 SSD 儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)最低代表 SAP 應用程式層 Vm 和非效能敏感的 DBMS 部署
      2.  我們建議不要將[Azure 標準 HDD 磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)一般
      2.  使用[Azure 進階儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)任何會從遠端效能敏感的 DBMS vm
      2.  使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)
      3.  透過 M 系列將「Azure 寫入加速器」用於 DBMS 記錄磁碟機。 請注意[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)中所記載的寫入加速器限制和使用方式
      4.  針對不同的 DBMS 類型，請參閱[一般 SAP 相關 DBMS 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)，以及一般文件帶您前往的 DBMS 特定文件
      5.  針對 SAP HANA，有更多詳細資料記載於 [SAP HANA 在 Azure 上的基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  請絕對避免透過裝置識別碼將 Azure 資料磁碟掛接到 Azure Linux VM。 相反地，請使用全域唯一識別碼 (UUID)。 舉例來說，使用圖形工具來掛接 Azure 資料磁碟時應多加留意。 請仔細檢查 /etc/fstab 中的項目，以確定磁碟是使用 UUID 來掛接
          1.  在 [這裡](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. 網路功能
      1.  測試和評估 VNet 基礎結構，以及跨不同 Azure 虛擬網路或在這些虛擬網路內的 SAP 應用程式分佈
          1.  根據下列條件，評估單一 Azure 虛擬網路內，中樞與輪輻虛擬網路架構或微分割的方法：
              1.  因[對等互連的 Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 之間資料交換而產生的成本。 如需了解成本，請參閱[虛擬網路價格](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  相較於在虛擬網路之子網路中所裝載應用程式或 VM 變成安全性風險時，變更 NSG 以隔離虛擬網路內子網路的做法，Azure 虛擬網路間對等互連的快速中斷連線優點
              3.  內部部署環境、外部環境及您在 Azure 中建立之虛擬資料中心之間網路流量的集中記錄和稽核
          2.  評估和測試 SAP 應用程式層與 SAP DBMS 層之間的資料路徑。 
              1.  完全不支援在 SAP NetWeaver、Hybris 或 S/4HANA 型 SAP 系統的 SAP 應用程式與 DBMS 層之間的通訊路徑中放置任何 [Azure 網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)
              2.  不支援將 SAP 應用程式層與 SAP DBMS 放置在未對等互連的不同 Azure 虛擬網路中
              3.  支援使用 [Azure ASG 和 NSG 規則](https://docs.microsoft.com/azure/virtual-network/security-overview)來定義 SAP 應用程式層與 SAP DBMS 層之間的路由
          3.  確定在於 SAP 應用程式層和 SAP DBMS 層使用的 VM 上已啟用 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)功能。 請記住，若要支援 Azure 中的「加速網路」，需要有各種不同的 OS 層級：
              1.  Windows Server 2012 R2 或更新版本
              2.  SUSE Linux 12 SP3 或更新版本
              3.  RHEL 7.4 或更新版本
              4.  Oracle Linux 7.5。 使用 RHCKL 核心時，版本必須是 be 3.10.0-862.13.1.el7。 使用 Oracle UEK 核心時，則需要第 5 版
          4.   根據 SAP 支援附註 [#500235](https://launchpad.support.sap.com/#/notes/500235) \(英文\) 和 SAP 支援附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) \(英文\)，測試和評估 SAP 應用程式層 VM 與 DBMS VM 之間的網路延遲。 依據 SAP 支援附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) \(英文\) 的網路延遲指引來評估結果。 網路延遲應該在適中和良好範圍。 例外狀況適用於 VM 與 HANA 大型執行個體之間的流量 (如[這裡](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所記載)
          5.   確定將 ILB 部署設定成使用「伺服器直接回傳」。 在將 Azure ILB 用於 DBMS 層上高可用性設定的情況下，此設定將可縮短延遲
          6.   如果您在 Linux 客體作業系統檢查 Linux 網路參數搭配使用 Azure Load Balancer **net.ipv4.tcp_timestamps**設為**0**。 針對建議在較舊版本的 SAP 附註[#2382421](https://launchpad.support.sap.com/#/notes/2382421)。 SAP 附註同時會更新以反映參數必須設為 0 的 Azure Load Balancer 搭配運作的事實。
   4. 高可用性和災害復原部署。 
      1. 如果您部署 SAP 應用程式層，但未定義特定的「Azure 可用性區域」，請確定執行單一 SAP 系統之 SAP 對話執行個體或中介軟體執行個體的所有 VM 都部署在一個[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)中。 
         1.   如果您不需要 SAP Central Services 和 DBMS 的高可用性，則可以將這些 VM 部署至與 SAP 應用程式層相同的「可用性設定組」中
      2. 如果您使用被動複本來保護 SAP Central Services 和 DBMS 層以提供高可用性，請將兩個 SAP Central Services 節點放在一個個別的「可用性設定組」中，然後將兩個 DBMS 節點放在另一個「可用性設定組」中
      3. 如果您部署至「Azure 可用性區域」，則無法利用「可用性設定組」。 不過，您將必須確定將主動和被動 Central Services 節點，部署至區域間延遲最短的兩個不同「可用性區域」。
         1.   請記住，如果要為跨「可用性區域」的 DBMS 和 SAP Central Services 層建立 Windows 或 Pacemaker 容錯移轉叢集，您必須使用 [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) 無法用於區域性部署 
   5. 逾時設定
      1. 查看不同 SAP 執行個體的 SAP NetWeaver 開發人員追蹤，並確定當中未指出排入佇列伺服器與 SAP 工作程序之間有發生任何連線中斷情況。 您可以透過設定下列兩個登錄參數，來避免這些連線中斷情況：
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 - 另請參閱[這篇文章](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) \(英文\)
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 - 另請參閱[這篇文章](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) \(英文\) 
      2. 為了避免內部部署 SAP GUI 介面與部署在 Azure 中的 SAP 應用程式層之間發生 GUI 逾時，請檢查 default.pfl 或執行個體設定檔中是否已設定下列參數：
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. 如果您使用「Windows 容錯移轉叢集」設定，請確定已為 Azure 設定正確的無回應節點反應時間。 Microsoft 的[調整容錯移轉叢集網路閾值](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/) \(英文\) 一文中列出參數，以及這些參數如何影響容錯移轉敏感性。 在所列出的參數當中，下列兩個參數應設定成以下的值：
         1.   SameSubNetDelay = 2
         2.   SameSubNetThreshold = 15
4. 測試您的高可用性和災害復原程序
   1. 藉由關閉 VM (Windows 客體 OS) 或讓作業系統進入緊急模式 (Linux 客體 OS)，以了解您的容錯移轉設定是否按照設計的方式運作，來模擬容錯移轉情況。 
   2. 測量執行容錯移轉所花費的時間。 如果花費的時間太長，請考慮：
      1.   針對 SUSE Linux，使用 SBD 裝置而不是「Azure 隔離代理程式」來加速容錯移轉
      2.   針對 SAP HANA，如果重新載入資料花費的時間太長，請考慮佈建更多儲存體頻寬
   3. 測試備份/還原順序及時間，並視需要進行調整。 請不只確定備份時間是否足夠。 也請測試還原，並了解還原活動的時間。 請確定還原時間在您的 RTO SLA 內，而您的 RTO 取決於資料庫或 VM 還原程序
   4. 完整測試區域 DR 功能和架構
5. 安全性檢查
   1.  測試您所實作 Azure 角色型存取 (RBAC) 架構的有效性。 目標是要對不同小組的存取和權限進行區分和限制。 舉例來說，SAP 基礎小組成員應該要能夠部署 VM，並將磁碟從 Azure 儲存體指派給指定的 Azure 虛擬網路。 不過，SAP 基礎小組不應該能夠建立自己的虛擬網路，或變更現有虛擬網路的設定。 另一方面，網路小組的成員則不應該能夠將 VM 部署至 SAP 應用程式和 DBMS VM 執行所在的虛擬網路中。 網路小組的成員也不應該能夠變更 VM 的屬性，或甚至刪除 VM 或磁碟。  
   2.  確認 [NSG 和 ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) 規則如預期般運作並防護受保護的資源
   3.  確定所有需要加密的資源都已加密。 定義並執行程序來備份憑證、儲存和存取這些憑證，以及還原已加密的實體。 
   4.  使用 [Azure 磁碟加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq)和/或從 OS 支援觀點，針對 OS 磁碟，儘可能使用此功能
   5.  確定未使用太多層加密。 在使用 Azure 磁碟加密之外再使用其中一種 DBMS「透明資料加密」方法，確實沒有太大的意義
6. 效能測試
   1.  在 SAP 中，根據 SAP 追蹤和度量，將前 10 名線上報告與目前的實作做比較 (如果適用) 
   2.  在 SAP 中，根據 SAP 追蹤和度量，將前 10 名批次作業與目前的實作做比較 (如果適用) 
   3.  在 SAP 中，根據 SAP 追蹤和度量，比較透過介面對 SAP 系統進行的資料傳輸。 焦點放在您知道，現在即將傳輸不同的位置，例如從內部部署移至 Azure 之間的介面 


## <a name="non-production-phase"></a>非生產環境階段 
在這個階段，我們會假設在試驗或 PoC 成功之後，您要開始將非生產環境 SAP 系統部署至 Azure。 來自概念證明的所有知識和經驗都應配合這類部署。 PoC 中列出的所有準則和步驟也都確實適用於此類型的部署。 在這個階段，您通常會將開發系統、單元測試系統及業務迴歸測試系統部署至 Azure。 建議一個 SAP 應用程式系列中至少要有一個非生產環境系統具有完整的高可用性設定，因為未來的生產環境系統將會有此設定。 在該階段期間，您需考量的額外步驟包括：  

1.  在將系統從舊平台移至 Azure 之前，先收集資源耗用量資料，例如 CPU 使用率、儲存體輸送量及 IOPS 資料。 特別是從 DBMS 層單位收集，但也從應用程式層單位收集。 此外，也請測量網路和儲存體延遲。
2.  記錄系統的可用性使用情況時間模式。 目標是要了解非生產環境系統是否必須全天候可供使用，或是否有非生產環境系統可在某週或某月的特定階段期間關閉
3.  測試並定義您是要為 Azure 中的 VM 建立自己的 OS 映像，還是要使用來自 Azure 映像資源庫的映像。 如果您要使用來自 Azure 資源庫的映像，請務必使用可反映與您 OS 廠商之支援合約的正確映像。 針對某些 OS 廠商，Azure 資源庫提供自備授權映像。 其他 OS 映像的支援則包含在 Azure 的報價中。 如果您決定建立自己的 OS 映像，您可以在下列文章中找到相關文件：
    1.  您可以根據[這份文件](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)，建立 Azure 中所部署 Windows VM 的一般化映像
    2.  您可以根據[這份文件](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)，建立 Azure 中所部署 Linux VM 的一般化映像
3.  如果您使用 Azure VM 資源庫中的 SUSE 和 Red Hat Linux 映像，您必須使用 Azure VM 資源庫中由 Linux 廠商提供的 SAP 適用映像
4.  確定您滿足 SAP 的 Microsoft 支援合約相關支援需求。 如需相關資訊，請參閱 SAP 支援附註 [#2015553](https://launchpad.support.sap.com/#/notes/2015553) \(英文\)。 針對 HANA 大型執行個體，請參閱[上架需求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)文件
4.  確定正確的人員會收到[計劃性維護通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)，以便您能夠及時選擇 VM 的停機和重新開機
5.  不斷查看 [Channel9](https://channel9.msdn.com/) 這類頻道上 Microsoft 簡報的 Azure 文件，以了解可能適用於您部署的新功能
6.  查看與 Azure 相關的 SAP 附註 (例如支援附註 [#1928533](https://launchpad.support.sap.com/#/notes/1928533))，以了解新的 VM SKU 或新支援的 OS 和 DBMS 版本。 比較新 VM 類型與舊 VM 類型的定價，以便您能夠部署具有最佳性價比的 VM
7.  再次驗證 SAP 支援附註、SAP HANA 硬體目錄及 SAP PAM 上的資源，以確定在針對 Azure 支援的 VM、這些 VM 中支援的 OS 版本，以及支援的 SAP 和 DBMS 版本方面，沒有任何變更
8.  查看[這裡](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) \(英文\) 以了解 Azure 中通過 HANA 認證的新 SKU，並將價格與您已規劃的 SKU 做比較，然後最終變更以取得具有較佳性價比的單位 
9.  調整您的部署指令碼，以利用新的 VM 類型及合併您想要使用的 Azure 新功能
10. 在部署基礎結構之後，根據 SAP 支援附註 [#500235](https://launchpad.support.sap.com/#/notes/500235) \(英文\) 和 SAP 支援附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) \(英文\)，測試和評估 SAP 應用程式層 VM 與 DBMS VM 之間的網路延遲。 依據 SAP 支援附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) \(英文\) 的網路延遲指引來評估結果。 網路延遲應該在適中和良好範圍。 例外狀況適用於 VM 與 HANA 大型執行個體之間的流量 (如[這裡](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所記載)。 請確定[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations)及 [SAP HANA 在 Azure 上的基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)中提及的限制都不適用於您的部署
11. 在套用工作負載之前，先執行「概念證明」階段中列出的所有其他檢查
12. 隨著工作負載的套用，記錄 Azure 中這些系統的資源耗用量，然後與您從舊系統取得的記錄做比較。 如果您看到較大的差異，請調整未來部署的 VM 大小。 請記住，如果縮減大小，VM 的儲存體和網路頻寬也會一併縮減：
    1.  [Azure 中 Windows 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 
    2.  [Azure 中的 Linux 虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 處理系統複製功能和程序。 目標是要讓您能夠輕鬆複製開發系統或測試系統，以便專案小組能夠快速取得新系統。 請考慮使用 [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) 作為執行這類工作的工具。
14. 對您小組的 Azure 角色型存取、權限及程序進行最佳化和訓練，以確定您已在一方面做好分工。 另一方面，您則想要讓所有小組都能夠在 Azure 基礎結構中執行其工作。
15. 練習、測試及記載高可用性和災害復原程序，以讓您的員工能夠執行這類工作。 識別缺點，然後調整您要整合到部署中的新 Azure 功能

 
## <a name="production-preparation-phase"></a>生產環境準備階段 
在這個階段中，您要收集非生產環境部署的所有經驗和知識，並將其套用在未來的生產環境部署中。 除了先前的階段之外，您還需要準備目前裝載位置與 Azure 之間資料傳輸的工作。 

1.  先進行您生產環境系統的必要 SAP 版本升級，再移至 Azure
2.  針對在移轉生產環境系統後所必須進行的功能和業務測試，與業務擁有者達成一致意見
    1.  確定是使用目前裝載位置中的來源系統進行所有這些測試。 您想要避免在系統移至 Azure 後才第一次執行測試
2.  測試移轉至 Azure 的生產環境移轉程序。 如果不打算在同一個時間範圍內將所有生產環境系統都移至 Azure，請為必須在相同裝載位置的生產環境系統建立群組。 練習並測試資料移轉。 常見的方法清單如下：
    1.  將 DBMS 方法 (例如備份/還原) 與 SQL Server AlwaysOn、HANA 系統複寫或記錄傳送搭配使用，以在 Azure 中植入及同步資料庫內容
    2.  針對較小的資料庫使用備份/還原
    3.  使用已在 SAP SWPM 工具中實作的 SAP Migration Monitor 來執行異質移轉
    4.  如果您需要結合 SAP 版本升級，請使用 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) \(英文\) 程序。 請記住，並非所有來源與目標 DBMS 之間的組合都受到支援。 如需詳細資訊，請參閱適用於不同 DMO 版本的特定 SAP 支援附註。 例如 [SUM 2.0 SP04 的資料庫移轉選項 (DMO)](https://launchpad.support.sap.com/#/notes/2644872) \(英文\)
    5.  測試在需要移動備份或 SAP 匯出檔案的情況下，透過網際網路進行資料傳輸與透過 ExpressRoute 進行資料傳輸，何者的輸送量較佳。 針對透過網際網路移動資料的情況，您可能需要變更某些您必須為未來生產環境備妥的 NSG/ASG 安全性規則
3.  在將系統從舊平台移至 Azure 之前，先收集資源耗用量資料，例如 CPU 使用率、儲存體輸送量及 IOPS 資料。 特別是從 DBMS 層單位收集，但也從應用程式層單位收集。 此外，也請測量網路和儲存體延遲。
4.  再次驗證 SAP 支援附註、SAP HANA 硬體目錄及 SAP PAM 上的資源，以確定在針對 Azure 支援的 VM、這些 VM 中支援的 OS 版本，以及支援的 SAP 和 DBMS 版本方面，沒有任何變更 
4.  調整部署指令碼以配合您對 VM 類型和 Azure 功能決定的最新變更
5.  部署基礎結構和應用程式之後，完成一系列檢查來進行驗證：
    1.  已將正確的 VM 類型搭配正確的屬性與儲存體大小進行部署
    2.  檢查 VM 是否使用正確和所需的 OS 版本與修補程式且一致
    3.  檢查 VM 是否依所需的方式強化且一致
    4.  檢查是否已安裝並部署正確的應用程式版本與修補程式
    5.  已將 VM 依照規劃部署至「Azure 可用性設定組」
    6.  已針對對延遲敏感的磁碟或要求[單一 VM SLA 達到 99.9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 的情況，使用「Azure 進階儲存體」
    7.  檢查是否已正確部署「Azure 寫入加速器」
        1.  確定在 VM 內，已在各個需要「Azure 寫入加速器」支援的磁碟上正確建置儲存空間或等量磁碟區
            1.  查看[在 Linux 上設定軟體 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  查看[在 Azure 中的 Linux VM 上設定 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  已採用獨佔方式使用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)
    9.  已將 VM 部署至正確的「可用性設定組」和「可用性區域」
    10. 確定在於 SAP 應用程式層和 SAP DBMS 層使用的 VM 上已啟用 [Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)功能
    11. 不支援在 SAP NetWeaver、Hybris 或 S/4HANA 型 SAP 系統的 SAP 應用程式與 DBMS 層之間的通訊路徑中放置任何 [Azure 網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)
    12. ASG 和 NSG 規則可依需求和規劃允許通訊，並在必要時封鎖通訊
    13. 已正確設定先前所述的逾時設定
    14. 根據 SAP 支援附註 [#500235](https://launchpad.support.sap.com/#/notes/500235) \(英文\) 和 SAP 支援附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) \(英文\)，測試和評估 SAP 應用程式層 VM 與 DBMS VM 之間的網路延遲。 依據 SAP 支援附註 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) \(英文\) 的網路延遲指引來評估結果。 網路延遲應該在適中和良好範圍。 例外狀況適用於 VM 與 HANA 大型執行個體之間的流量 (如[這裡](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所記載)
    15. 檢查是否已在必要的位置並搭配必要的加密方法來部署加密
    16. 檢查介面及其他應用程式是否能夠連線至新部署的基礎結構
6.  建立因應 Azure 計劃性維護的劇本。 定義進行計劃性維護時要重新啟動的系統和 VM 順序
    

## <a name="go-live-phase"></a>上線階段
針對上線階段，您必須確定依照在先前階段開發的劇本進行操作。 執行您已測試和訓練的步驟。 請勿接受在最後時刻才進行的設定和程序變更。 除此之外，請套用下列量值：

1. 確認 Azure 入口網站監視功能及其他監視工具正常運作。  建議使用的工具為 Perfmon (Windows) 或 SAR (Linux)： 
    1.  CPU 計數器 
        1.  平均 CPU 時間 – 總計 (所有 CPU)
        2.  平均 CPU 時間 – 每個個別處理器 (所以在 m128 VM 上會有 128 個處理器)
        3.  CPU 時間核心 – 每個個別處理器
        4.  CPU 時間使用者 – 每個個別處理器
    5.  記憶體 
        1.  可用記憶體
        2.  記憶體置入分頁/秒
        3.  記憶體移出分頁/秒
    4.  磁碟 
        1.  磁碟讀取 kb/秒 – 每一個別磁碟 
        2.  磁碟讀取/秒 – 每一個別磁碟
        3.  磁碟讀取毫秒/讀取 – 每一個別磁碟
        4.  磁碟寫入 kb/秒 – 每一個別磁碟 
        5.  磁碟寫入/秒 – 每一個別磁碟
        6.  磁碟寫入毫秒/讀取 – 每一個別磁碟
    5.  網路 
        1.  網路輸入封包/秒
        2.  網路輸出封包/秒
        3.  網路輸入 kb/秒
        4.  網路輸出 kb/秒 
2.  移轉資料之後，執行您與業務擁有者一致同意的所有驗證測試。 請只接受針對原始來源系統有結果的驗證測試結果
3.  檢查介面是否正常運作，以及其他應用程式是否能夠與新部署的生產環境系統進行通訊
4.  透過 SAP 交易 STMS 檢查傳輸和修正系統
5.  在針對生產環境發行系統之後，立即執行資料庫備份
6.  在針對生產環境發行系統之後，立即為 SAP 應用程式層 VM 執行 VM 備份
7.  如果有 SAP 系統未參與目前的上線階段，但會與在此上線階段移至 Azure 的 SAP 系統進行通訊，您就必須重設 SM51 中的主機名稱緩衝區。 此步驟會針對已移至 Azure 的應用程式執行個體，移除與其名稱相關之舊的已快取 IP 位址  


## <a name="post-production"></a>進入生產環境後
這個階段都是與監視、操作及管理系統相關。 從 SAP 的觀點來看，您對舊裝載位置所需執行的一般工作都適用。 您會想要進行的 Azure 特定工作包括：

1. 針對高收費系統進行 Azure 發票分析
2. 將 VM 端和儲存體端的性價比效率最佳化
3. 將可關閉系統的時間最佳化  


## <a name="next-steps"></a>後續步驟
參閱文件：

- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

