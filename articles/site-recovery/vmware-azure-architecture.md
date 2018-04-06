---
title: Azure Site Recovery 中的 VMware 至 Azure 複寫架構 | Microsoft Docs
description: 本文概述使用 Azure Site Recovery 將內部部署 VMware VM 複寫至 Azure 時，所使用的元件和架構
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: c1aa89f14edab7d0e560c20d6bc48480aff1631f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware 至 Azure 複寫架構

本文說明透過 [Azure Site Recovery](site-recovery-overview.md)，在內部部署 VMware 網站與 Azure 之間複寫、容錯移轉和復原 VMware 虛擬機器 (VM) 時所使用的架構和程序。


## <a name="architectural-components"></a>架構元件

下表和圖形提供將 VMware 複寫至 Azure 時所用元件的高層級檢視。

**元件** | **需求** | **詳細資料**
--- | --- | ---
**Azure** | Azure 訂用帳戶、Azure 儲存體帳戶及 Azure 網路。 | 從內部部署 VM 複寫的資料會儲存在儲存體帳戶中。 從內部部署環境容錯移轉至 Azure 時，會以複寫的資料建立 Azure VM。 Azure VM 在建立後會連線到 Azure 虛擬網路。
**組態伺服器電腦** | 單一的內部部署電腦。 建議您將其當作可從下載的 OVF 範本部署的 VMware VM 來執行。<br/><br/> 此機器會執行所有的內部部署 Site Recovery 元件，包括組態伺服器、處理序伺服器和主要目標伺服器。 | **組態伺服器**：負責協調內部部署與 Azure 之間的通訊，以及管理資料複寫。<br/><br/> **處理序伺服器**：依預設會安裝在組態伺服器上。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。 處理序伺服器也會在您要複寫的 VM 上安裝 Azure Site Recovery 行動服務，並自動探索內部部署機器。 隨著部署規模擴大，您可以新增額外的個別處理序伺服器，以處理日較大的複寫流量。<br/><br/> **主要目標伺服器**：預設會安裝在組態伺服器上。 負責處理從 Azure 進行容錯回復期間的複寫資料。 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。
**VMware 伺服器** | VMware VMs 會裝載於內部部署 vSphere ESXi 伺服器。 我們建議以 vCenter 伺服器管理主機。 | 在 Site Recovery 部署期間，您可將 VMware 伺服器新增至復原服務保存庫。
**複寫的機器** | 行動服務會安裝在您複寫的每個 VMware VM 上。 | 建議您允許從處理序伺服器自動安裝。 或者，您可以手動安裝服務，或使用 System Center Configuration Manager 等自動化部署方法。

**VMware 至 Azure 架構**

![元件](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>組態步驟

設定 VMware 至 Azure 災害復原或移轉的廣泛步驟如下：

1. **設定 Azure 元件**。 您需要具有正確權限的 Azure 帳戶、Azure 儲存體帳戶、Azure 虛擬機器，以及復原服務保存庫。 [深入了解](tutorial-prepare-azure.md)。
2. **設定內部部署**。 這些包括在 VMware 伺服器上設定帳戶以便讓 Site Recovery 能夠自動探索要複寫的 VM、設定可用來在所要複寫之 VM 上安裝行動服務元件的帳戶，以及確認 VMware 伺服器和 VM 符合先決條件規範。 您也可以視需要準備在容錯移轉後連線至這些 Azure VM。 Site Recovery 會將 VM 資料覆寫至 Azure 儲存體帳戶，然後使用您容錯移轉至 Azure 的資料來建立 Azure VM。 [深入了解](vmware-azure-tutorial-prepare-on-premises.md)。
3. **設定複寫**。 您需選擇複寫目的地。 您需藉由設定執行所需之所有內部部署 Site Recovery 元件的單一內部部署 VMware VM (設定伺服器)，來設定來源複寫環境。 設定之後，您需在「復原服務」保存庫中註冊設定伺服器電腦。 接著，您需選取目標設定。 [深入了解](vmware-azure-tutorial.md)。
4. **建立複寫原則**。 您需建立一個指定應如何進行複寫的複寫原則。 
    - **RPO 臨界值**：這個監視設定指示如果在指定的時間內未進行複寫，便會發出警示 (並依選擇發出電子郵件)。 例如，如果您將 [RPO 臨界值] 設定為 30 分鐘，而有問題導致複寫在 30 分鐘內都無法進行，系統就會產生事件。 此設定不會影響複寫。 複寫是連續的，而且每隔幾分鐘就會建立一次復原點
    - **保留期**：復原點保留期指定了復原點在 Azure 中的保存期限。 針對進階儲存體，您可以指定介於 0 到 24 個小時的值，針對標準儲存體，則可以指定最多 72 小時的值。 您可以容錯移轉至最新的復原點，或是預存的點 (如果您設定大於零的值)。 過了復原時間範圍之後，系統就會將復原點清除。
    - **當機時保持一致快照集**：根據預設，Site Recovery 會建立當機時保持一致快照集，並每隔幾分鐘建立其相關復原點。 如果復原點之所有相互關聯資料元件的寫入順序都與建立復原點時保持一致，復原點就會在當機時保持一致。 為了更容易了解，請想像在發生電源中斷或類似事件之後，您電腦硬碟上的資料狀態。 如果您的應用程式是設計成從當機復原並保持資料完全一致，通常當機時保持一致復原點便足以應付需求。
    - **應用程式一致快照集**如果此值不是零，在 VM 上執行的行動服務將會嘗試產生檔案系統一致快照集和復原點。 在完成初始複寫之後，就會建立第一個快照集。 接著，會以您指定的頻率建立快照集。 如果除了寫入順序一致以外，執行中的應用程式完成其所有作業並將其緩衝區排清至磁碟 (應用程式靜止)，復原點就會具有應用程式一致性。 建議您針對資料庫應用程式 (例如 SQL、Oracle 及 Exchange) 使用應用程式一致復原點。 如果當機時保持一致快照集已足以應付需求，則可以將此值設定為 0。  
    - **多部 VM 一致性**：您可以視需要建立複寫群組。 接著，當您啟用複寫時，即可將 VM 收集至該群組中。 複寫群組中的 VM 會一起複寫，並且在容錯移轉時會有共用的當機時保持一致和應用程式一致復原點。 您應該謹慎使用此選項，因為需要跨多部電腦收集快照集，所以此選項會影響到工作負載效能。 請只在 VM 執行相同工作負載而需要保持一致，且 VM 具有相同的變換時，才這麼做。 您最多可以將 8 個 VM 新增至群組。 
5. **啟用 VM 複寫**。 最後，您需為內部部署 VMware VM 啟用複寫。 如果您已建立帳戶來安裝行動服務，並已指定 Site Recovery 應執行推送安裝，則行動服務將會安裝在您啟用複寫功能的每個 VM 上。 [深入了解](vmware-azure-tutorial.md#enable-replication)。 如果您已針對多部 VM 一致性建立複寫群組，便可以將 VM 新增至該群組。
6. **測試容錯移轉**。 設定好所有項目之後，您可以執行測試容錯移轉，以確認 VM 會如預期般容錯移轉至 Azure。 [深入了解](tutorial-dr-drill-azure.md)。
7. **容錯移轉**。 如果您只是要將 VM 移轉至 Azure - 您可以執行完整容錯移轉來達到該目的。 如果您要設定災害復原，則可以視需要執行完整容錯移轉。 針對完整災害復原，在容錯移轉至 Azure 之後，您可以在內部部署網站可供使用時，容錯回復至該網站。 [深入了解](vmware-azure-tutorial-failover-failback.md)。

## <a name="replication-process"></a>複寫程序

1. 為 VM 啟用複寫時，它會開始根據複寫原則進行複寫。 
2. 流量會透過網際網路複寫到 Azure 儲存體的公用端點。 或者，您也可以使用 Azure ExpressRoute 搭配[公用對等互連](../expressroute/expressroute-circuit-peerings.md#azure-public-peering)。 不支援從內部部署網站透過站對站虛擬私人網路 (VPN) 將流量複寫至 Azure。
3. VM 資料的初始複本會複寫至 Azure 儲存體。
4. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 機器的追蹤變更會保存在 .hrl 檔案中。
5. 進行通訊的過程如下：

    - VM 會在輸入連接埠 HTTPS 443 上與內部部署設定伺服器進行通訊，以進行複寫管理。
    - 設定伺服器會透過輸出連接埠 HTTPS 443 與 Azure 協調複寫。
    - VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
    - 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。


**VMware 到 Azure 複寫程序**

![複寫程序](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

設定複寫並執行災害復原演練 (測試容錯移轉) 來檢查一切如預期般運作之後，您可以視需要執行容錯移轉和容錯回復。

1. 您可以容錯移轉單一電腦，或建立復原方案來同時容錯移轉多個 VM。 與單一電腦容錯移轉相比，復原方案的優點包括	：
    - 您可以藉由將應用程式的所有 VM 都納入單一復原方案中，來建立應用程式相依性模型。
    - 您可以新增指令碼、Azure Runbook 及暫停來進行手動動作。
2. 觸發初始容錯移轉之後，您要認可讓它開始從 Azure VM 存取工作負載。
3. 當主要內部部署網站恢復可用狀態時，您就可以準備容錯回復。 若要進行容錯回復，您必須設定容錯回復基礎結構，包括：

    * **Azure 中的暫時處理序伺服器**︰若要從 Azure 容錯回復，您可以設定 Azure VM 作為處理序伺服器，以處理來自 Azure 的複寫。 容錯回復完成後，您可以刪除此 VM。
    * **VPN 連線**：若要容錯回復，您必須要有從 Azure 網路到內部部署網站的 VPN 連線 (或 ExpressRoute)。
    * **個別的主要目標伺服器**︰根據預設，隨組態伺服器安裝的主要目標伺服器 (在內部部署 VMware VM 上) 會處理容錯回復。 如果您需要容錯回復大量流量，請針對此用途設定個別的內部部署主要目標伺服器。
    * **容錯回復原則**︰若要複寫回到內部部署網站，您需要容錯回復原則。 此原則會在您建立從內部部署環境到 Azure 的複寫原則時自動建立。
4. 備妥元件之後，容錯回復會分三個階段進行：

    - 第 1 階段：重新保護 Azure VM，使其能從 Azure 複寫回到內部部署 VMware VM。
    -  第 2 階段：執行容錯移轉至內部部署網站。
    - 第 3 階段：工作負載進行容錯回復之後，您必須重新啟用內部部署 VM 的複寫。
    
 
**從 Azure 容錯回復 VMware**

![容錯回復](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>後續步驟

請遵循[此教學課程](vmware-azure-tutorial.md)，以啟用 VMware 至 Azure 的複寫。
