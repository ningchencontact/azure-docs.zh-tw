---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: adf99b941a775f105d8c65da3ac6c11dc7257120
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416266"
---
Azure 會定期更新平台來提升虛擬機器之主機基礎結構的可靠性、效能及安全性。 這些更新的範圍涵蓋修補主控環境中的軟體元件、升級網路元件，以及硬體解除委任。 這些更新大多數都不影響所裝載的虛擬機器。 不過，在某些情況下，更新的確會造成影響，而 Azure 會選擇影響最小的更新方法：

- 如果可以執行無須重新開機的更新，在更新主機時將會暫停 VM，或是將其即時移轉至已經更新的主機。

- 如果維護需要重新開機，您會在規劃維護時收到通知。 Azure 也會提供一個時間範圍，供您在適合您的時間自行開始進行維護。 自我維護時間範圍通常是 30 天，除非它是迫切需要執行維護工作。 Azure 也投資技術，以降低的情況下，當 Vm 重新啟動規劃的平台的維護。 

本頁說明 Azure 如何執行這兩種類型的維護。 如需有關非計劃性事件 (中斷) 的詳細資訊，請參閱管理 [Windows](../articles/virtual-machines/windows/manage-availability.md) 或 [Linux](../articles/virtual-machines/linux/manage-availability.md) 虛擬機器的可用性。

您可以使用適用於 [Windows](../articles/virtual-machines/windows/scheduled-events.md) 或 [Linux](../articles/virtual-machines/linux/scheduled-events.md) 的 Scheduled Events，來取得有關即將進行之維護的 VM 內通知。

如需管理計劃性維護的「作法」資訊，請參閱適用於 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 或 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) 的「處理計劃性維護通知」。

## <a name="maintenance-not-requiring-a-reboot"></a>不需要重新開機的維護

如上所述，大部分的平台更新完成客戶 Vm 沒有任何影響。 零影響更新是不可能時 Azure 便會選擇是最低的影響力，客戶 vm 的更新機制。 大部分的這些非零衝擊維護會導致小於 10 秒暫停 vm。 在某些情況下，記憶體保留維護機制使用，它會暫停 30 秒的 VM，並會保留在 RAM 中的記憶體。 VM 會接著繼續執行，並會自動同步處理其時鐘。 記憶體保留維護適用於高於 90 %g、 M、 N 和 H 系列除外的 Azure Vm。 Azure 正逐漸增加使用即時移轉技術，以及改進記憶體保留維護機制，以縮短暫停持續時間。  

這些不需要重新開機的維護作業會逐個容錯網域套用，而且如果收到任何警告健康情況訊號，進度就會停止。 

這些類型的更新會對某些應用程式造成影響。 如果是將 VM 即時移轉至不同的主機，有些敏感性工作負載可能會在該幾分鐘內出現些微的效能降低，而導致 VM 暫停。 對於這類應用程式，使用適用於 [Windows](../articles/virtual-machines/windows/scheduled-events.md) 或 [Linux](../articles/virtual-machines/linux/scheduled-events.md) 的 Scheduled Events 來為 VM 維護做準備會很有幫助，可在 Azure 維護期間不造成任何影響。 Azure 也正致力於為這類超敏感的應用程式開發維護控制功能。 

### <a name="live-migration"></a>即時移轉

即時移轉非 rebootful 操作，以保留記憶體的 vm，並在有限的結果暫停 」 或 「 凍結，通常維時不能超過 5 秒。 現在，所有的基礎結構即服務 (IaaS) 虛擬機器，除了 G、 M、 N 和 H 系列，可進行即時移轉。 這相當於超過 90%的部署到 Azure 團隊的內的 IaaS Vm。 

由 Azure 網狀架構，在下列情況下起始即時移轉：
- 計劃性維護
- 硬體失敗
- 配置最佳化

即時移轉會運用在某些規劃的維護案例中，而且已排定事件可用來了解預先當即時移轉作業開始。

將虛擬機器移出即將發生的預測失敗，當我們的機器學習服務演算法所偵測到的硬體及最佳化的虛擬機器配置，也會使用即時移轉。 若要深入了解我們預測性模型化所偵測到的已降級的硬體執行個體，請參閱我們的部落格文章標題[預測 ML 和即時移轉的改善 Azure 虛擬機器復原](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 客戶一律會收到其在監視器中的 Azure 入口網站中的即時移轉通知 / 服務健全狀況記錄，或透過排程的事件是否正在使用這些。

## <a name="maintenance-requiring-a-reboot"></a>維護需要重新開機

在罕見的情況下 VM 會需要針對計劃性維護重新啟動，但您會事先收到通知。 計劃性維護有兩個階段：自助式期間和排程維護期間。

**自助時段**可讓您在您的 VM 上開始進行維護。 在此期間，這通常是四週，您可以查詢每個 VM，以查看其狀態，並檢查上次維護要求的結果。

當您開始進行自助維護時，您的 VM 會重新部署至已經更新的節點。 因為 VM 會重新開機，所以暫存磁碟會遺失，而系統會更新與虛擬網路介面關聯的動態 IP 位址。

如果您開始自助維護且在此過程中發生錯誤，此作業就會停止，系統不會更新 VM，而且您可以選擇重試自助維護。 

當自助式期間過後，**排程的維護期間**隨即開始。 在這段期間內，您仍然可以查詢維護時段，但是無法再自行開始進行維護。

如需管理需要重新開機的維護作業的相關資訊，請參閱 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 或 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) 的「處理預定進行的維修作業通知」。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>已排程的維護期間的可用性考量 

如果您決定等到排程維護期間，為了維持 VM 的最高可用性，需考慮下列事項。 

#### <a name="paired-regions"></a>配對的區域

每個 Azure 區域都會與相同地理位置內的另一個區域配對，以共同形成區域配對。 在排定的維護階段中，Azure 只會更新區域配對中單一區域的 VM。 舉例來說，在更新美國中北部的 VM 時，Azure 並不會同時更新任何美國中南部的 VM。 不過，像是北歐等其他區域可以和美國東部一同進行維護。 了解區域配對的運作方式可協助您將 VM 更妥善地分散於各區域。 如需詳細資訊，請參閱 [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性設定組和擴展集

在 Azure VM 上部署工作負載時，您可以在一個可用性設定組內建立 VM，以便為應用程式提供高可用性。 這可確保在發生中斷情況或需要重新開機的維護事件期間，至少有一部 VM 可供使用。

在可用性設定組內，個別 VM 會散佈在多達 20 個更新網域 (UD)。 在排定的維護期間，在任何指定時間都只會更新單一的更新網域。 要更新之更新網域的順序不一定會循序發生。 

虛擬機器擴展集是一個可讓您以單一資源的形式，部署和管理一組相同 VM 的 Azure 計算資源。 擴展集會自動部署於更新網域，如可用性設定組中的 VM。 就像可用性設定組一樣，使用擴展集時，在排定的維護期間內，於任何指定時間都只會更新單一更新網域。

如需有關設定 VM 以取得高可用性的詳細資訊，請參閱管理 [Windows](../articles/virtual-machines/windows/manage-availability.md) 或 [Linux](../articles/virtual-machines/linux/manage-availability.md) 虛擬機器的可用性。
