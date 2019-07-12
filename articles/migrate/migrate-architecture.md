---
title: Azure Migrate 架構 |Microsoft Docs
description: 提供 Azure Migrate 服務的概觀。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811423"
---
# <a name="azure-migrate-architecture-and-processes"></a>Azure Migrate 架構和程序

[Azure Migrate](migrate-overview.md)提供工具，可協助您探索、 評估及移轉至 Microsoft Azure 的應用程式、 基礎結構，以及工作負載的中樞。 中樞還包含 Azure Migrate 的工具和第三方獨立軟體廠商 (ISV) 供應項目。 

 本文摘要說明的評定及移轉架構和程序適用於 Azure Migrate Server 評量，Azure 移轉的伺服器移轉。

## <a name="assessment-with-azure-migrate-server-assessment"></a>評估 azure Migrate Server 評定

Azure Migrate Server 評估可以評定 VMware Vm 和 HYPER-V Vm 移轉至 Azure。 評估的運作方式，如下所示：

1. **準備評估**:內部部署，您設定使用 Azure Migrate 的輕量型 Azure Migrate 設備內部部署 VMware VM 或 HYPER-V VM 和應用裝置的註冊。
2. **探索 Vm**:Azure Migrate 設備會執行以探索內部部署 Vm。 
    - 不需要安裝在探索到的 Vm 上。
    - VM 中繼資料包括核心、 記憶體、 磁碟、 磁碟大小和網路介面卡的相關資訊。
    - 效能資料包含 CPU 和記憶體使用量、 磁碟 IOPS、 磁碟輸送量 (MBps)，以及網路輸出 (MBps) 的相關資訊
- **收集和評估機器**:探索完成之後，在 Azure 入口網站您收集探索到 Vm 通常包含您想要一起移轉的 Vm 分組。 您可以執行評量群組上。


## <a name="vmware-assessment"></a>VMware 評量 

圖摘要說明 VMware VM 評估如何使用 Azure Migrate Server 評量。

![評估架構 VMware](./media/migrate-architecture/sas-architecture-vmware.png)

程序如下：

1. **部署 Azure Migrate 設備**:

    a. 從 Azure 入口網站下載 (OVA) 範本。

    b. 匯入它的 vCenter Server 機器來建立 VM。

    c. 連線到此 VM、 設定基本設定，以及註冊使用 Azure Migrate。

2. **起始探索**:

    a. 連線到收集器應用程式來開始探索應用裝置上執行。

    b. 設備持續中繼資料和效能資料會從傳送 Vm 至 Azure。

    - 應用裝置一律會連接到 Azure Migrate 服務中。
    - 擷取持續探索期間的環境變更。 比方說在探索範圍中，新增 Vm，或新增 VM 磁碟或網路介面卡。

3. **評定機器**:

    a. 探索完成之後，在 Azure 入口網站您收集探索到 Vm 分組。  群組通常包含您想要一起移轉的 Vm。

    b. 執行每個群組的評估。

4. **檢閱評估**: 

    a. 評估完成之後，請在入口網站中檢視它。

    b. 以便進一步分析，下載 Excel 格式的評估。



### <a name="vmware-ports"></a>VMware 通訊埠
Azure Migrate 會使用下列連接埠，適用於 VMware:

**Source** | **目標** | **連接埠** | **詳細資料**
--- | --- | --- | ---
Azure Migrate 應用裝置 | Azure Migrate 服務 | Target-TCP 443 | 將中繼資料和效能資料傳送至 Azure Migrate。
Azure Migrate 應用裝置 | vCenter Server | Target-TCP 443 | 連線到 vCenter Server 的中繼資料和效能資料。 443 是預設值，但可以使用 vCenter 接聽不同連接埠上修改。 
RDP 用戶端 | Azure Migrate 應用裝置 | Target-TCP3389 | RDP 連線連到觸發程序探索從應用裝置。

### <a name="collected-vmware-metadata"></a>收集的 VMware 中繼資料

應用裝置中繼資料和效能相關的資料會從傳送 Vm 至 Azure。

**動作** | **詳細資料**
--- | ---
**收集的中繼資料** | vCenter VM 名稱<br/> vCenter VM 路徑 （主機/叢集資料夾）<br/> IP 和 MAC 位址<br/> 作業系統<br/> 核心/磁碟/Nic 數目<br/> 記憶體和磁碟的大小。
**收集的效能資料** | CPU/記憶體使用量<br/> 每一磁碟的資料 （磁碟讀取/寫入輸送量，磁碟讀取/寫入 / 秒）<br/> NIC 資料 （out 的網路中的網路）。<br/><br/> 應用裝置會連線到 vCenter Server 之後，持續收集效能資料。 不會收集歷程記錄資料。

## <a name="hyper-v-assessment"></a>HYPER-V 評估

圖摘要說明 HYPER-V 評估 works 登 Azure Migrate Server 評量的方式。

![評估架構 HYPER-V](./media/migrate-architecture/sas-architecture-hyper-v.png)

程序如下：

1. **建立 Azure Migrate 設備**:

    a. 從 Azure 入口網站下載壓縮的格式中的 VM。

    b. 它匯入 HYPER-V 主機。

    c. 連線至設備 VM。 設定基本設定，並向 Azure Migrate。

2. **起始探索**:

    a. 連線至 Azure Migrate 設備來開始探索。 不需要安裝在探索到的 Vm 上。

    b. 持續應用裝置會將 VM 中繼資料和效能資料傳送至 Azure Migrate。

    - 設備始終連線至 Azure Migrate 服務 （使用 CIM 工作階段）。
    - 擷取持續探索期間的環境變更。 比方說在探索範圍中，新增 Vm，或新增 VM 磁碟或網路介面卡。


3. **評定機器**:

    a. 探索完成之後，在 Azure 入口網站您收集探索到 Vm 分組。  群組通常包含您想要一起移轉的 Vm。

    b. 執行每個群組的評估。

4. **檢閱評估**:

    a. 評估完成之後，請在入口網站中檢視它。

    b. 以便進一步分析，下載 Excel 格式的評估。

### <a name="hyper-v-ports"></a>HYPER-V 通訊埠

Azure Migrate 服務會使用下列連接埠 for Hyper-v:

**Source** | **目標** | **連接埠** | **詳細資料**
--- | --- | --- | ---
Azure Migrate 應用裝置 | Azure Migrate 服務 | Target-TCP 443 | 將中繼資料和效能資料傳送至 Azure Migrate。
Azure Migrate 應用裝置 | HYPER-V 主機/叢集 | Target-Default WinRM ports-HTTP:5985; HTTPS:5986 | 連線到主機的中繼資料和效能資料。 連接所使用的 CIM 工作階段
RDP 用戶端 | Azure Migrate 應用裝置 | Target-TCP3389 | RDP 連線連到觸發程序探索從應用裝置。

## <a name="collected-hyper-v-vm-metadata"></a>收集的 HYPER-V VM 中繼資料

應用裝置中繼資料和效能相關的資料會從傳送 Vm 至 Azure。


**動作** | **詳細資料**
--- | ---
**收集的中繼資料** | VM 名稱<br/> VM 路徑 （主機/叢集資料夾）<br/> IP 和 MAC 位址<br/> 作業系統<br/> 核心/磁碟/Nic 數目<br/> 記憶體和磁碟大小<br/> 磁碟 IOPS、 磁碟輸送量 (MBps)，網路輸出 (MBps)
**收集的效能資料** |  CPU/記憶體使用量<br/> 每一磁碟的資料 （磁碟讀取/寫入輸送量，磁碟讀取/寫入 / 秒）<br/> NIC 資料 （out 的網路中的網路）。<br/><br/> 設備連線到 HYPER-V 主機之後，持續收集效能資料。 不會收集歷程記錄資料。




## <a name="migration-with-azure-migrate-server-migration"></a>使用 Azure 的移轉程序移轉伺服器移轉

使用 Azure 移轉的伺服器移轉，您可以將下列移轉至 Azure:

- 內部部署 VMware VM
- 在內部部署 HYPER-V Vm
- 在內部部署實體伺服器
- AWS Windows VM 執行個體
- GCP Windows VM 執行個體

移轉程序稍有不同，視您要移轉。


## <a name="migrate-vmware-vms"></a>將 VMware Vm 移轉

Azure 移轉的伺服器移轉內部部署 VMware vm 移轉，提供兩種方法：

- **無代理程式的複寫**:將 Vm 移轉而不需要在其上安裝任何項目。
- **代理程式為基礎的複寫**。 複寫的 VM 上安裝代理程式。

雖然無代理程式的複寫從部署觀點來看更容易，但它目前有一些限制。 [了解更多](server-migrate-overview.md)關於這些限制。
 

### <a name="agent-based-migration"></a>代理程式為基礎的移轉

代理程式為基礎的移轉 VMware vm 需要多個要部署的元件，如表摘要說明。

**元件** | **需求** | **詳細資料**
--- | --- | ---
**組態伺服器電腦** | 單一內部部署 VMware VM 從已下載的 OVF 範本部署。<br/><br/> 此機器會執行所有的內部部署 Site Recovery 元件，包括組態伺服器和處理序伺服器。 | **組態伺服器**︰負責協調內部部署與 Azure 之間的通訊，以及管理資料複寫。<br/><br/> **處理序伺服器**：預設會安裝在組態伺服器上。 它會接收複寫資料;快取、 壓縮和加密，以最佳化並將它傳送至 Azure。 處理序伺服器也會在 Vm 上，安裝 Azure Site Recovery 行動服務，並執行自動探索內部部署機器。
**行動服務** | 必須在您複寫的每部 VMware VM 上安裝行動服務。 | 建議您允許從處理序伺服器自動安裝。 或者，您可以手動安裝服務，或使用 System Center Configuration Manager 等自動化部署方法。





### <a name="agent-based-replication-process"></a>代理程式為基礎的複寫程序

![複寫程序](./media/migrate-architecture/v2a-architecture-henry.png)

1. 當您啟用 VM 複寫時，便會開始初始複寫至 Azure。 
    - 複寫是區塊層級 」、 「 幾乎連續，使用行動服務代理程式在 VM 上執行。
    - 複寫原則設定會套用：
        - **RPO 閾值**。 此設定不會影響複寫。 其可協助進行監視。 如果目前的 RPO 超過您指定的閾值限制，則系統會引發事件並選擇性傳送電子郵件。
        - **復原點保留**。 發生中斷時，此設定會指定您所希望回溯的時間。 進階儲存體中的保留期上限為 24 小時。 標準儲存體則為 72 小時。 
        - **應用程式一致快照集**。 應用程式快照集會視應用程式的需求每隔 1 至 12 小時拍攝一次。 快照集為標準的 Azure blob 快照集。 在 VM 執行的流動性代理程式會根據此設定要求 VSS 快照集，並在複寫串流中將該時間點標記為應用程式一致時間點。

2. 流量會透過網際網路複寫到 Azure 儲存體的公用端點。

    - 或者，您可以使用 Azure ExpressRoute 搭配[Microsoft 對等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)。
    - 不支援從內部部署網站透過站對站虛擬私人網路 (VPN) 將流量複寫至 Azure。
3. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 機器的追蹤變更會傳送至流程伺服器。
2. 進行通訊的過程如下：

    - VM 會在輸入連接埠 HTTPS 443 上與內部部署設定伺服器進行通訊，以進行複寫管理。
    - 設定伺服器會透過輸出連接埠 HTTPS 443 與 Azure 協調複寫。
    - VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
    - 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。
5. 複寫資料會記錄在 Azure 中的快取儲存體帳戶中的首。 處理這些記錄檔和資料儲存在 Azure 受控的磁碟 （Azure Site Recovery 種子磁碟）。 在此磁碟上建立復原點。







## <a name="next-steps"></a>後續步驟

- 針對 Azure Migrate [檢閱相關常見問題](resources-faq.md)。
- 來自社群的協助，請瀏覽[Azure 移轉的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc)或是[Stack Overflow](https://stackoverflow.com/search?q=azure+migrate)。

