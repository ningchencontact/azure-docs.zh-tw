---
title: Azure 的 Lsv2 系列虛擬機器儲存體上的效能最佳化 |Microsoft Docs
description: 了解如何將您的解決方案 Lsv2 系列虛擬機器上的效能最佳化。
services: virtual-machines-linux
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 7be86c8934b8766217f9fca432327d254204f0c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738939"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Lsv2 系列虛擬機器上的效能最佳化

Lsv2 系列虛擬機器支援各種不同的工作負載需要高 I/O 和在本機儲存體的輸送量，跨各種不同的應用程式和產業廣為採用。  Lsv2 系列適用於巨量資料、 SQL、 NoSQL 資料庫、 資料倉儲和大型交易的資料庫，包括 Cassandra、 MongoDB、 Cloudera 和 Redis。

Lsv2 系列虛擬機器 (Vm) 的設計可最大化 AMD EPYC™ 7551 處理器提供最佳效能之間的處理器、 記憶體、 NVMe 裝置和 Vm。 除了硬體將效能最大化，Lsv2 系列 Vm 被設計用於搭配較佳的效能和硬體與軟體的 Linux 作業系統的需求。

調整的軟體與硬體的最佳化版本會導致[Canonical 的 Ubuntu 18.04 和 16.04](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer?tab=Overview)發行至 Azure Marketplace 中的 NVMe 裝置支援最大效能的年 12 月 2018 年初Lsv2 系列 Vm。

這篇文章提供的秘訣和建議，以確保您的工作負載和應用程式達到最大效能設計到 Vm。 當更多的 Lsv2 最佳化映像新增至 Azure Marketplace，就會持續更新此頁面上的資訊。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ 晶片組架構

Lsv2 系列 Vm 使用 AMD EYPC™ 伺服器處理器的 Zen 微架構。 AMD 開發無限大 Fabric (IF) EYPC™ 為可擴充的互連其 NUMA 的模型，可用於晶圓上、 在套件，以及多封裝通訊。 相較於 QPI （快速路徑互連） 」 和 「 UPI （超級路徑互連） 用於 Intel 現代的單體式骰子處理器、 AMD 之多 NUMA 小型內建架構可能也會導致這兩個效能優勢和挑戰。 記憶體頻寬和延遲條件約束的實際影響，可以視執行的工作負載類型而有所不同。

## <a name="tips-to-maximize-performance"></a>為了達到最佳效能的秘訣

* 如果您要上傳自訂的 Linux GuestOS，為您的工作負載，請注意，將會加速網路**OFF**預設。 如果您想要啟用加速網路，則會啟用在建立 VM 時，為了達到最佳效能。

* 提供 Lsv2 系列 Vm 的硬體會使用具有 8 個 I/O 佇列組 (QP) s 的 NVMe 裝置。 NVMe 裝置 I/O 的每個佇列都是實際組： 提交佇列和一個完成。 NVMe 驅動程式並將設定為將我來最佳化這些八個 I/O QPs 的使用量/O 的循環配置資源排程。 若要獲得最大效能，執行每個裝置，以符合八個工作。

* 避免混用使用中的工作負載期間的 NVMe 系統管理命令 （比方說，智慧型 NVMe info 查詢等） 使用 NVMe I/O 命令。 Lsv2 NVMe 裝置是由任何 NVMe 系統管理員命令暫止時，會切換為 「 變慢模式 」 中的 HYPER-V NVMe 直接技術支援。 Lsv2 使用者可看到大幅效能 NVMe I/O 效能中卸除，如果發生這種情況。

* Lsv2 使用者不應依賴資料磁碟機的 vm 來決定其應用程式的 NUMA 親和性的報告從裝置 NUMA 資訊 (所有 0)。 為了達到最佳效能的建議的方式是盡可能分配 Cpu 的工作負載。

* 每個 I/O 佇列組 Lsv2 VM NVMe 裝置支援的最大佇列深度為 1024 （與。Amazon i3 QD 32 的限制）。 Lsv2 使用者應該要限制其 （綜合） 的效能評定工作負載至 1024年或更低，若要避免觸發佇列完整條件，可能會降低效能的佇列深度。

## <a name="utilizing-local-nvme-storage"></a>使用本機 NVMe 儲存體

暫時 $1.92 TB NVMe 磁碟上所有 Lsv2 Vm 上的本機儲存體。 在 VM 的成功標準重新開機，期間會保存本機 NVMe 磁碟上的資料。 如果重新部署、 解除配置，或刪除 VM，資料將不會保存在 NVMe。 如果另一個問題會導致 VM 或它正在執行變得狀況不良的硬體，將不會保存資料。 當發生這種情況時，安全地清除任何舊的主機上的資料。

將也有案例當 VM 需要移至不同的主機電腦，例如，在規劃的維護作業期間。 預定進行的維修作業和有些硬體失敗可以預期有[已排定事件](scheduled-events.md)。 排定的事件應該用來掌握任何預測的維護和復原作業中。

在計劃性的維護事件之前，含有空白的本機磁碟的新主機上重新建立 VM 的情況下，資料將需要重新同步處理 （同樣地，使用安全地清除舊的主機上的任何資料）。 這是因為 Lsv2 系列 Vm 目前不支援即時移轉本機 NVMe 磁碟上。

有兩種模式，因維護計劃。

### <a name="standard-vm-customer-controlled-maintenance"></a>標準 VM 客戶控制維護

- 在 30 天期間，VM 會移至更新的主機。
- Lsv2 本機儲存體的資料可能遺失，因此建議在事件之前的備份資料。

### <a name="automatic-maintenance"></a>自動維護

- 如果客戶不會執行由客戶控制的維護，或發生緊急程序，例如安全性的零時差事件時，就會發生。
- 要保留客戶的資料，但會有小型 VM 凍結或重新開機的風險。
- Lsv2 本機儲存體的資料可能遺失，因此建議在事件之前的備份資料。

對於任何即將推出的服務事件，使用受控制的維護程序來選取要更新的最方便的時間。 在事件中之前, 您可能會備份進階儲存體中的資料。 維護事件完成之後，您可以傳回您的資料重新整理 Lsv2 Vm 本機 NVMe 儲存體。

維護本機 NVMe 磁碟上的資料的案例包括：

- VM 是執行且狀況良好。
- VM 會就地重新啟動，且 （由您或 Azure）。
- VM 已暫停 （而不需要取消配置已停止）。
- 大部分的服務作業的計劃性維護。

安全地清除來保護客戶資料的案例包括：

- 重新部署 VM 時，已停止 （取消配置），或刪除 （由您）。
- VM 會變為狀況不良，並對服務修復因為硬體問題的另一個節點。
- 需要 VM 重新配置到另一部主機服務的一小部分的服務作業的計劃性維護。

若要了解本機儲存體中資料的備份選項的詳細資訊，請參閱[Azure IaaS 磁碟的備份和災害復原](backup-and-disaster-recovery-for-azure-iaas-disks.md)。

## <a name="frequently-asked-questions"></a>常見問題集

* **如何開始部署 Lsv2 系列 Vm？**  
   較常就像任何其他 VM，使用[入口網站](quick-create-portal.md)， [Azure CLI](quick-create-cli.md)，或[PowerShell](quick-create-powershell.md)建立 VM。

* **將單一 NVMe 磁碟失敗會導致失敗主機上的所有 Vm？**  
   如果硬體節點上偵測到的磁碟失敗時，硬體會處於失敗狀態。 當發生這種情況時，在節點上的所有 Vm 會自動取消配置，並移至狀況良好的節點。 針對 Lsv2 系列 Vm，這表示失敗節點上的客戶資料安全地清除，而且必須由客戶在新的節點上重新建立。 如所述，即時移轉才會用於 Lsv2，失敗節點上的資料將會主動移動的 vm 才會傳送到另一個節點。

* **我是否需要進行任何調整 rq_affinity 效能？**  
   Rq_affinity 設定時使用的絕對最大的輸入/輸出作業 (iops) 略做調整。 一旦一切運作正常，然後再次嘗試將 rq_affinity 設定為 0 並檢查它是否能讓一項差異。

* **我需要變更 blk_mq 設定嗎？**  
   RHEL/CentOS 7.x 會自動使用 blk mq NVMe 裝置。 任何組態變更或設定所需。 Scsi_mod.use_blk_mq 設定僅適用於 SCSI，和 NVMe 裝置顯示在客體 Vm 為 SCSI 裝置因為 Lsv2 預覽期間使用。 目前，NVMe 裝置會顯示為 NVMe 裝置，因此 SCSI blk mq 設定無關。

* **我是否需要變更 「 fio 」？**  
   若要取得最大值 IOPS 測量工具，例如 'fio' L64v2 和 L80v2 VM 大小的效能，設定為 0"rq_affinity 」 每個 NVMe 裝置上。  比方說，這個命令列將 L80v2 VM 中設定 「 rq_affinity"設為零的所有 10 個 NVMe 裝置：

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   也請注意，I/O 是直接向每個原始 NVMe 裝置沒有分割時，沒有檔案系統中，沒有 RAID 0 設定等等時取得最佳效能。開始之前測試的工作階段，請確定設定不是已知的全新/清除狀態執行`blkdiscard`上每個 NVMe 裝置。
   
## <a name="next-steps"></a>後續步驟

* 請參閱所有的規格[以儲存體效能最佳化的 Vm](sizes-storage.md)在 Azure 上
