---
title: 關於 Azure Migrate | Microsoft Docs
description: 提供 Azure Migrate 服務的概觀。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 03/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8f6c2c54dd20b663eb43c716a2bd82310cf0256a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="about-azure-migrate"></a>關於 Azure Migrate

Azure Migrate 服務會評估要移轉至 Azure 的內部部署工作負載。 此服務會評估內部部署機器的移轉適用性，以及如何根據效能來調整大小，並提供在 Azure 中執行內部部署機器的成本估計。 如果您正打算進行隨即移轉，或者正處於移轉的早期評估階段，此服務很適合您。 評估之後，您可以使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 和 [Azure 資料庫移轉服務](https://docs.microsoft.com/azure/dms/dms-overview)等服務，將機器移轉至 Azure。

## <a name="why-use-azure-migrate"></a>為何使用 Azure Migrate？

Azure Migrate 可協助您：

- **評估 Azure 整備程度**：評估您的內部部署機器是否適合在 Azure 中執行。 
- **取得大小建議**：根據內部部署 VM 的效能歷程記錄，取得 Azure VM 的大小建議。 
- **預估每月成本**：取得在 Azure 中執行內部部署機器的估計成本。  
- **安心地移轉**：將內部部署機器的相依性視覺化，以建立您將一起評估和移轉的機器群組。 

## <a name="current-limitations"></a>目前的限制

- 目前只能針對那些要移轉到 Azure VM 的內部部署 VMware 虛擬機器 (VM) 進行評估。 VMware VM 必須由 vCenter Server (5.5、6.0 或 6.5 版) 管理。
- 支援 HYPER-V 已在我們的計畫中。 在過渡時期，我們建議您使用 [Azure Site Recovery 部署規劃工具](http://aka.ms/asr-dp-hyperv-doc)來規劃 Hyper-V 工作負載的移轉。 
- 您可以在單一探索中探索多達 1500 個 VM，並在單一專案中探索多達 1500 個 VM。 此外，您還可以在單一評估中評估多達 1500 個 VM。
- 您只能在「美國中西部」或「美國東部」區域建立 Azure Migrate 專案。 不過，這不會影響您針對不同的目標 Azure 位置規劃移轉的能力。 移轉專案的位置只用於儲存在內部部署環境中發現的中繼資料。
- Azure Migrate 只支援將受控磁碟用於進行移轉評估。


## <a name="what-do-i-need-to-pay-for"></a>我需要支付多少費用？

[在此](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/)深入了解 Azure Migrate 定價。


## <a name="whats-in-an-assessment"></a>評估包含什麼？

評估可協助您識別內部部署 VM 的 Azure 適用性，取得在 Azure 中執行 VM 的適當大小建議和成本估計。 您可藉由變更評估的屬性，根據您的需求自訂評估。 以下是建立評估時所考量的屬性。 

**屬性** | **詳細資料**
--- | ---
**目標位置** | 要作為移轉目的地的 Azure 位置。<br/><br/>Azure Migrate 目前支援 30 個區域，包括澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、印度中部、美國中部、中國東部、中國北部、東亞、美國東部、德國中部、德國東北部、美國東部 2、日本東部、日本西部、韓國中部、韓國南部、美國中北部、北歐、美國中南部、東南亞、印度南部、英國南部、英國西部、美國中西部、西歐、印度西部、美國西部和美國西部 2。 根據預設，目標位置會設定為美國西部 2。 
**儲存體備援** | Azure VM 會在移轉後使用的[儲存體備援](https://docs.microsoft.com/azure/storage/common/storage-redundancy)類型。 [本地備援儲存體 (LRS)] 是預設值。 請注意，Azure Migrate 只支援以受控磁碟為基礎的評估，而受控磁碟只支援 LRS，因此此屬性目前只有 LRS 選項。 
**調整大小準則** | Azure Migrate 用於為 Azure 設定適當 VM 大小的準則。 您可以根據內部部署 VM 的「效能歷程記錄」調整大小，或將 Azure 的 VM 大小調整為「內部部署」，而不需考量效能歷程記錄。 預設值是根據效能調整大小。
**定價方案** | 計算成本時，評估會考量您是否具有軟體保證，以及是否享有 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 它也會考量您已註冊的 [Azure 優惠](https://azure.microsoft.com/support/legal/offer-details/)，並允許您指定該項優惠所能享有的任何訂用帳戶專屬折扣 (%)。 
**定價層** | 您可以指定目標 Azure VM 的[定價層 (基本/標準)](../virtual-machines/windows/sizes-general.md)。 例如，如果您打算移轉到生產環境，則可考慮標準層，這會提供低延遲的 VM，但成本可能比較高。 另一方面，如果您具有開發測試環境，則可以考慮基本層，其中有較高延遲和較低成本的 VM。 預設會使用[標準](../virtual-machines/windows/sizes-general.md)層。
**效能歷程記錄** | 只有調整大小準則是以效能為基礎時才適用。 根據預設，Azure Migrate 會使用最後一天的效能歷程記錄 (95% 百分位數值) 來評估內部部署機器的效能。 您可以在評估屬性中修改這些值。 
**緩和因數** | Azure Migrate 會在評估期間考量緩衝區 (緩和因數)。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 例如，10 核心的 VM，如果使用率只達 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。 預設緩和設定為 1.3x。


## <a name="how-does-azure-migrate-work"></a>Azure Migrate 如何運作？

1.  您可建立 Azure Migrate 專案。
2.  Azure Migrate 會使用稱為收集器設備的內部部署 VM，探索您的內部部署機器相關資訊。 若要建立設備，您可下載開放虛擬化設備 (.ova) 格式的安裝檔案，然後將它匯入為內部部署 vCenter Sever 上的 VM。
3.  您在 vCenter Server 中使用主控台連線來連線到 VM，在連線時為 VM 指定新密碼，然後在 VM 中執行收集器應用程式來開始探索。
4.  收集器會使用 VMware PowerCLI Cmdlet 來收集 VM 中繼資料。 探索是無代理程式的，而且不會在 VMware 主機或 VM 上安裝任何項目。 所收集的中繼資料包含 VM 資訊 (核心、記憶體、磁碟、磁碟大小和網路介面卡。 它也會收集 VM 的效能資料，包括 CPU 和記憶體使用量、磁碟 IOPS、磁碟輸送量 (MBps) 以及網路輸出 (MBps)。
5.  中繼資料會推送至 Azure Migrate 專案。 您可以在 Azure 入口網站中檢視它。
6.  基於評估目的，您會將探索到的 VM 集合成群組。 例如，您可將執行相同應用程式的 VM 群組在一起。 如需更精確的群組，您可以使用相依性視覺效果來檢視特定機器的相依性，或檢視群組中所有機器的相依性並調整群組。
7.  在群組形成後，您可建立群組的評估。 
8.  評估完成後，您可以在入口網站中進行檢視，或以 Excel 格式進行下載。



  ![Azure Planner 架構](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>連接埠需求為何？

下表摘要說明 Azure Migrate 通訊所需的連接埠。

|元件          |通訊對象     |所需的連接埠  |原因   |
|-------------------|------------------------|---------------|---------|
|收集器          |Azure Migrate 服務   |TCP 443        |收集器會透過 SSL 連接埠 443 連線至服務|
|收集器          |vCenter Server          |預設值 9443   | 根據預設，收集器會連線到連接埠 9443 上的 vCenter Server。 如果伺服器接聽不同的連接埠，則應該設定為收集器 VM 上的傳出連接埠。 |
|內部部署 VM     | Operations Management Suite (OMS) 工作區          |[TCP 443](../log-analytics/log-analytics-windows-agent.md) |MMA 代理程式會使用 TCP 443 來連線到 Log Analytics。 如果您正在使用相依性視覺效果功能，且正在安裝 Microsoft Monitoring Agent (MMA) 代理程式，您只需要此連接埠。 |


  
## <a name="what-happens-after-assessment"></a>評估後會發生什麼事？

在評估內部部署機器以便使用 Azure Migrate 服務移轉之後，您可以使用一些工具來執行移轉：

- **Azure Site Recovery**：您可以使用 Azure Site Recovery 來移轉至 Azure，如下所示：
  - 準備 Azure 資源，包括 Azure 訂用帳戶、Azure 虛擬網路和儲存體帳戶。
  - 準備內部部署 VMware 伺服器以便移轉。 您可確認 Site Recovery 的 VMware 支援需求、準備 VMware 伺服器以供探索，以及準備在您想要移轉的 VM 上安裝 Site Recovery 行動服務。 
  - 設定移轉。 您可設定復原服務保存庫、設定來源和目標移轉設定、設定複寫原則，以及啟用複寫。 您可以執行災害復原演練，以檢查 VM 移轉至 Azure 是否運作正常。
  - 執行容錯移轉以將內部部署機器移轉至 Azure。 
  - 在 Site Recovery 移轉教學課程中[深入了解](../site-recovery/tutorial-migrate-on-premises-to-azure.md)。

- **Azure 資料庫移轉**：如果您的內部部署機器正在執行 SQL Server、MySQL 或 Oracle 等資料庫，您可以使用 Azure 資料庫移轉服務將資料庫移轉到 Azure。 [深入了解](https://azure.microsoft.com/campaigns/database-migration/)。



## <a name="next-steps"></a>後續步驟 
[請遵循教學課程](tutorial-assessment-vmware.md)，以建立內部部署 VMware VM 的評估。