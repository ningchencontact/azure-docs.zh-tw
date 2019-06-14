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
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66155917"
---
Azure 會定期更新其平台來改善可靠性、 效能和安全性的虛擬機器之主機基礎結構。 這些更新的目的範圍中修補軟體元件，在裝載環境中升級網路元件，或解除委任的硬體。 

更新很少會影響裝載的 Vm。 更新沒有作用，Azure 就可以選擇更新的影響最大方法：

- 如果更新不需要重新開機，VM 會暫停，而主機已更新，或 VM 即時移轉至已更新的主機。

- 如果維護需要重新開機，規劃的維護會通知您。 Azure 也提供在其中您可以自行開始維護，適用於您一次的時間範圍。 自我維護視窗通常是 30 天，除非維護非常緊急。 Azure 會投資技術以減少的計劃的平台的維護需要重新啟動 Vm 的案例數目。 

本頁說明 Azure 如何執行這兩種類型的維護。 如需非計劃性事件 （中斷） 的詳細資訊，請參閱 [管理的 Windows Vm 的可用性](../articles/virtual-machines/windows/manage-availability.md)或對應的發行項，如[Linux](../articles/virtual-machines/linux/manage-availability.md)。

VM 內，您可以取得有關即將進行維護的通知[使用的 Windows 排定的事件](../articles/virtual-machines/windows/scheduled-events.md)若是[Linux](../articles/virtual-machines/linux/scheduled-events.md)。

如需管理計劃性的維護的指示，請參閱[處理計劃性維護通知適用於 Linux](../articles/virtual-machines/linux/maintenance-notifications.md)或對應的發行項，如[Windows](../articles/virtual-machines/windows/maintenance-notifications.md)。

## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新開機的維護

如稍早所述，大部分的平台更新不會影響客戶的 Vm。 不受影響的更新方法不可行，Azure 就可以選擇是最低的影響力，客戶 vm 的更新機制。 

大部分的非零值影響維護會小於 10 秒暫停 VM。 在某些情況下，Azure 會使用記憶體保留維護機制。 這些機制會暫停 30 秒的 VM，並保留在 RAM 中的記憶體。 VM 會接著繼續執行，並會自動同步處理其時鐘。 

記憶體保留維護適用於超過 90%的 Azure Vm。 它不適用於 G、 M、 N 和 H 系列。 越來越多，azure 會使用即時移轉技術，並改進記憶體保留維護機制，以縮短的暫停持續時間。  

這些不需要重新開機的維護作業一次會套用一個容錯網域。 如果他們收到任何警告健康情況訊號，停止它們。 

這些類型的更新可能會影響某些應用程式。 時 VM 即時移轉至不同的主機，某些機密的工作負載可能會導致 VM 暫停幾分鐘內顯示輕微的效能降低。 若要準備 VM 維護，並減少在 Azure 的維護影響，請嘗試[使用的 Windows 排定的事件](../articles/virtual-machines/windows/scheduled-events.md)或[Linux](../articles/virtual-machines/linux/scheduled-events.md)之類的應用程式。 Azure 正致力於維護控制的功能，這些機密的應用程式。 

### <a name="live-migration"></a>即時移轉

即時移轉是不需要重新開機，而且會保留 vm 的記憶體的作業。 它會導致暫停或凍結，通常維時不能超過 5 秒。 除了 G、 M、 N，而 H 系列中，所有的基礎結構即服務 (IaaS) Vm，可進行即時移轉。 符合資格的 Vm 會代表超過 90%的 IaaS Vm 部署到 Azure 的伺服器團隊。 

Azure 平台會開始即時移轉，在下列情況：
- 預定的維修
- 硬體失敗
- 配置最佳化

某些計劃性維護的情況下使用即時移轉，以及您可以使用排程的事件，事先知道時即時移轉作業會啟動。

即時移轉也可用來移動 Vm，Azure 機器學習服務演算法預測即將發生硬體失敗或當您想要最佳化 VM 配置時。 如需有關偵測到的已降級的硬體執行個體的預測模型的詳細資訊，請參閱[改善 Azure VM 復原能力，預測性機器學習服務和即時移轉](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 如果您使用這些服務，即時移轉的通知會出現在 Azure 入口網站中監視和服務健全狀況記錄檔中也如同已排定事件。

## <a name="maintenance-that-requires-a-reboot"></a>維護需要重新開機

在少數情況下，Vm 必須重新啟動因維護計劃，將會通知您事先。 計劃性的維護有兩個階段: 「 自助 」 階段和排程的維護階段。

期間*自助式階段*，這通常會持續四週，您在 Vm 上開始維護。 隨著自助的詳細資訊，您可以查詢每個 VM，以查看其狀態和上次維護要求的結果。

當您開始進行自助維護時，您的 VM 會重新部署至已經更新的節點。 VM 重新開機，因為暫存磁碟會遺失，並會更新虛擬網路介面相關聯的動態 IP 位址。

在自助服務的維護期間，作業就會停止，發生錯誤時 VM 不會更新，並取得重試自助式維護選項。 

「 自助 」 階段結束時，*排定的維護階段*開始。 在這個階段，您仍然可以查詢維護階段，但您無法自行開始維護。

如需有關管理維護需要重新開機的詳細資訊，請參閱[處理計劃性維護通知適用於 Linux](../articles/virtual-machines/linux/maintenance-notifications.md)或對應的發行項，如[Windows](../articles/virtual-machines/windows/maintenance-notifications.md)。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>已排程的維護期間的可用性考量 

如果您決定等到排程的維護階段，有幾件事您應該考慮以維護您的 Vm 的最高的可用性。 

#### <a name="paired-regions"></a>配對的區域

每個 Azure 區域都會與相同的地理區域內的另一個區域配對。 共同，形成區域配對。 在排程的維護階段期間，Azure 會更新區域配對的單一區域中的 Vm。 比方說，在更新美國中北部的 VM，Azure 不更新任何在美國中南部的 VM 在相同的時間。 不過，像是北歐等其他區域可以和美國東部一同進行維護。 了解區域配對的運作方式可協助您將 VM 更妥善地分散於各區域。 如需詳細資訊，請參閱 [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性設定組和擴展集

部署 Azure Vm 上的負載時，您可以建立內的 Vm*可用性設定組*以提供您的應用程式的高可用性。 使用可用性設定組，您就可以確保在需要重新開機的中斷或維護事件，至少一個 VM 使用的。

在可用性設定組內，個別 VM 會散佈在多達 20 個更新網域 (UD)。 在排程的維護期間，只有一個 UD 會更新一次。 Ud 不一定會以循序方式更新。 

虛擬機器*擴展集*是 Azure 計算資源，您可以使用來部署和管理一組相同 Vm 做為單一資源。 擴展集自動部署於 Ud，像是可用性設定組中的 Vm。 為可用性設定組，當您使用擴展集，只有一個 UD 會更新在任何指定的時間，在排定的維護期間。

如需有關設定您的 Vm 以獲得高可用性的詳細資訊，請參閱 [管理 Windows 針對您 Vm 的可用性](../articles/virtual-machines/windows/manage-availability.md)或對應的發行項，如[Linux](../articles/virtual-machines/linux/manage-availability.md)。
