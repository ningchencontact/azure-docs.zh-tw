---
title: 單一 Azure 區域中的 SAP HANA | Microsoft Docs
description: Azure 原生 VM 上的 SAP Hana 作業
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
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>單一 Azure 區域中的 SAP HANA 可用性
本節中會示範幾個案例，說明單一 Azure 區域內的可用性狀況。 Azure 有許多分散在世界各地的區域。 如需 Azure 區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)一文。 若要在單一 Azure 區域內的 VM 上部署 SAP HANA，Microsoft 提供具有 HANA 執行個體的單一 VM 部署。 如需更多的可用性，您可以在 [Azure 可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中部署具有兩個 HANA 執行個體的兩個 VM ，以便使用 HANA 系統複寫來增加可用性。 Azure 有公開預覽版的 [Azure 可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)。 但目前不會詳細討論這些可用性區域。 但會說明在使用可用性設定組與可用性區域上的一些概念差異。

Azure 可用性設定組和可用性區域之間的差異為何？ 要在其中提供可用性區域的 Azure 區域中會有多個資料中心，而這些資料中心與電力來源、冷卻及網路無關。 之所以在單一 Azure 區域 (region) 中提供不同區域 (zone)，是要讓您可跨兩個或三個所提供的可用性區域來部署應用程式。 假設電力來源和/或網路中的問題只會影響一個可用性區域基礎結構，那麼 Azure 區域內的應用程式部署仍然可完全正常運作。 由於可能會失去單一區域內的一些 VM，因此最後會造成部分產能降低。 但其他兩個區域中的 VM 仍舊會繼續執行。 
 
反之，Azure 可用性設定組是一種可在 Azure 中使用的邏輯群組功能，用以確保其中所放置的 VM 資源在部署到 Azure 資料中心時會隔離彼此的失敗。 Azure 可確保您在可用性設定組中所放置的 VM，會橫跨多部實體伺服器、計算機架、儲存體單位和網路交換器來執行。 如同某些其他 Azure 文件中所述，這是指不同[更新網域和容錯網域](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)中的配置。 這些配置通常在 Azure 資料中心內。 假設電力來源和/或網路中的問題會影響您部署的資料中心，那麼單一 Azure 區域中的所有產能將會受到影響。

配置代表 Azure 可用性區域的資料中心，是在服務 (部署在大部分應用程式能接受的不同區域) 之間傳遞網路延遲與在資料中心之間有一定距離的折衷辦法。 因此，最理想的情況是，此區域內所有可用性區域的電力與網路供應及基礎結構，都不會受到自然災害的影響。 不過，從歷史上的重大自然災害看來，可用性區域不一定能永遠提供單一區域內所需的可用性。 例如 2017 年 8 月 20 日侵襲波多黎各島嶼的瑪莉亞 (Maria) 颶風，至少造成島上方圓 90 英里內的地區幾乎完全停電。   
  


## <a name="single-vm-scenario"></a>單一 VM 案例
在此案例中，您已建立 SAP HANA 執行個體的 Azure 虛擬機器。 您已使用 Azure 進階儲存體來裝載作業系統磁碟和所有資料磁碟。 Azure 99.9% 的執行時間 SLA 及其他 Azure 元件的 SLA，足以實現您要提供給客戶的可用性 SLA。 在此案例中，您不需要針對執行 DBMS 層的 VM 使用 Azure 可用性設定組。 在此案例中，您會依賴兩個不同的功能：

- Azure VM 自動重新啟動 (也稱為 Azure 服務修復)
- SAP HANA 自動重新啟動

Azure VM 自動重新啟動或「服務修復」是在以下兩個層級上運作的 Azure 功能：

- Azure 伺服器主機，檢查伺服器主機上裝載的 VM 健康情況
- Azure 網狀架構控制器，監視伺服器主機的健康情況和可用性

對於裝載在 Azure 伺服器主機上的每個 VM 而言，健康情況檢查功能就是監視所裝載 VM 的健康情況。 如果 VM 落到健康情況不良的狀態，檢查 VM 健康情況的 Azure 主機代理程式就會起始 VM 的重新開機作業。 Azure 網狀架構控制器會藉由檢查許多指出主機硬體問題的不同參數，來確認主機的健康情況，但也會透過網路檢查主機的協助工具。 指出主機問題可能引起的動作如下：

- 如果主機發出健康狀態不良的訊號，主機會重新開機，而主機上執行的 VM 會重新啟動
- 將主機重新開機，並在狀態良好的主機上重新啟動原本裝載在主機上的 VM，以避免主機重新開機後處於健康情況不良的狀態。 在此情況下，主機會標示為狀態不良，且在進行清除或更換之前，無法進行進一步的部署。
- 在狀態良好的主機上立即重新啟動 VM，以避免狀態不良的主機在重新開機程序中發生問題。 

Azure VM 有 Azure 提供的主機和 VM 監視功能，因此 Azure VM 受到主機問題的影響時會自動在狀態良好的 Azure 主機上重新啟動 

您在此案例中依賴的第二個功能，就是在這類重新啟動的 VM 上執行 HANA 服務時，HANA 服務會在 VM 重新開機後自動啟動。 [HANA 服務自動重新啟動](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)可透過不同 HANA 服務的監視程式服務來設定。

透過將冷容錯移轉節點新增至 SAP HANA 組態，即可提升單一 VM 案例的效能。 或者如 SAP HANA 文件中指出的 [Host Auto-Failover](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)。此組態適用於內部部署的情況，其中伺服器硬體受到限制，而您可以將單一伺服器節點作為一組生產主機的 Host Auto-Failover 節點。 但是針對 Azure 的情況，部署 SAP Host Auto-Failover 案例就毫無意義，因為 Azure 基礎結構已提供狀態良好的目標伺服器來成功重新啟動 VM。 

因此，我們沒有參考架構可用來預測 HANA Host Auto-Failover 的待用節點。 這也適用於 SAP HANA 向外擴充的組態。


## <a name="availability-scenarios-involving-two-different-vms"></a>涉及兩種不同 VM 的可用性案例
在 Azure 可用性設定組中使用兩個 Azure VM，可讓您增加這兩個 VM 之間的運作時間，但前提是這些 VM 放在單一 Azure 區域內的 Azure 可用性設定組中。 Azure 中的基礎設定如下圖所示：![兩個 VM 和所有層級](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

為了說明不同可用性案例，我們會刪減上方一些層級，並將圖示限制在 VM、主機、可用性設定組和 Azure 區域。 此說明案例中不會用到 Azure Vnet、資源群組及訂用帳戶。

### <a name="replicating-backups-to-second-virtual-machine"></a>將備份複寫到第二個虛擬機器
其中一個最基本的設定就是將備份 (特別是交易記錄備份) 從一個 VM 傳送至另一個 Azure 虛擬機器。 您可以選擇任何 Azure 儲存體類型。 您將負責使用指令碼將第一個 VM 上已排定的備份複製到第二個 VM。 如果使用時需要第二個 VM 的執行個體，您必須將完整備份、增量/差異備份與交易記錄備份還原至需要的時間。 架構將如下所示：![兩個 VM 與儲存體複寫](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

若要達到絕佳 RPO 和 RTO 時間，則不太適合此設定。 特別是 RTO 時間會因為需使用複製的備份來完整還原整個資料庫而犧牲。 不過此設定適合用來復原不小心從主要執行個體上刪除的資料。 透過此設定，您可以隨時還原至特定時間、擷取資料，以及將已刪除的資料匯入至您的主要執行個體。 因此，將這種備份複製方法與其他高可用性功能結合，可以更有意義。 只有複製備份時，您可能處理的是較小的 VM，而不是主要 SAP HANA 執行個體。 但請注意，較小的 VM 可連結的 VHD 數目也較少。 請查看 [Azure 中 Linux 虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)，了解個別 VM 類型的限制。

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>使用不含自動容錯移轉的 SAP HANA 系統複寫
在下列案例中，您將使用 SAP HANA 系統複寫。 若要尋找 SAP 發行的文件，可以從[系統複寫](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)一文開始。 單一 Azure 區域中的兩個 Azure VM 有兩種不同組態，因此在復原時間目標中會有一些差異。 一般情況下，不具有自動容錯移轉的案例可能與單一 Azure 區域內的案例不太相關。 原因是，針對 Azure 基礎結構中的大部分失敗情況，Azure 服務修復會在另一部主機上重新啟動主要 VM。 只有針對某些邊緣案例，這種組態可能對失敗情況有所幫助。 或是作為客戶的您想要了解一些案例，特別是效率方面的資訊。

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>使用不具有自動容錯移轉和資料預先載入的 HANA 系統複寫 
在此案例中，您會使用 SAP HANA 系統複寫來同步移動資料，以達到復原點目標 (RPO) 為 0。 另一方面，您有夠長的復原時間目標 (RTO)，因此不需要容錯移轉或預先將資料載入 HANA 執行個體快取。 在這種情況下，您可以透過以下方式，使組態更具經濟效益：

- 您可以在第二個 VM 中執行需耗用虛擬機器中大部分記憶體的 SAP HANA 執行個體。 通常這類執行個體就是容錯移轉至第二個 VM 時可能會停機的執行個體。 因此，在第二個 VM 中，複寫的資料可能會載入至鎖定的 HANA 執行個體快取。
- 您可以使用較小的 VM 大小來作為第二個 VM。 如果是容錯移轉，您在手動容錯移轉前，需將 VM 大小調整成來源 VM 的大小。 該案例將如下所示：

![兩個 VM 與儲存體複寫](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> 即使 HANA 系統複寫目標中沒有資料預先載入，您需要至少 64GB 記憶體以上，才能在目標執行個體記憶體中保存資料列存放區資料。

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>使用不具有自動容錯移轉但具有資料預先載入的 HANA 系統複寫
與前面介紹的案例相比，此案例的差異是，在第二個 VM 中，複寫到 HANA 執行個體的資料是預先載入的。 如此一來，在不用預先載入資料之案例中具備的兩個優勢將不存在。 在此情況下，您無法在第二個 VM 上執行另一個 SAP HANA 系統。 也不能使用較小的 VM 大小。 因此，這是難以向客戶實作的案例


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>使用具有自動容錯移轉的 SAP HANA 系統複寫

單一 Azure 區域內的標準可用性組態 (大部分的客戶會與 SAP HANA 一起實作)，就是其中的兩個 Azure 虛擬機器，會執行有定義容錯移轉叢集的 SLES Linux。 SLES 的 Linux 叢集會以 [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) 架構為基礎，並搭配 [STONITH](http://linux-ha.org/wiki/STONITH) 裝置。 如果從 SAP HANA 的角度來看，所使用的複寫模式已同步處理，且自動容錯移轉也已設定。 在第二個 VM 中，SAP HANA 執行個體會作為熱待命節點，其接收與主要 SAP HANA 執行個體同步的變更記錄資料流。 HANA 主要節點上的應用程式認可交易後，主要 HANA 節點會等候向應用程式確認該認可，直到次要 SAP HANA 節點確認接收到認可記錄為止。 SAP HANA 兩個不同的同步複寫模式。 如需有關兩種同步複寫模式的詳細資料和差異，請閱讀 [SAP HANA 系統複寫的複寫模式](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)一文

整體組態如下所示

![兩個 VM 與儲存體複寫及容錯移轉](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

之所以選擇此解決方案，是因為它可讓您達到 RPO = 0 且極低的 RTO 時間。 透過 SAP HANA 用戶端使用虛擬 IP 位址來連線至 HANA 系統複寫組態的方式，設定 SAP HANA 用戶端連線。 如此一來，在容錯移轉至第二個節點時，就不需要重新設定應用程式。 在此解決方案中，主要和次要節點的 Azure VM SKU 必須相同。  


## <a name="next-steps"></a>後續步驟
如果您需要如何在 Azure 中設定這類組態的逐步指引，請參閱文章：

- [在 Azure 虛擬機器中設定 SAP HANA 系統複寫](sap-hana-high-availability.md)
- [Azure 上的 SAP – 第 4 部分 – 使用系統複寫的 SAP HANA 高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

如果您需要跨 Azure 區域的 SAP HANA 可用性詳細資訊，請閱讀：

- [跨 Azure 區域的 SAP HANA 可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

