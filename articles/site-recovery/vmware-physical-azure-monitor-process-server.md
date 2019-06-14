---
title: 監視 Azure Site Recovery 處理序伺服器
description: 本文說明如何監視 Azure Site Recovery 處理序伺服器。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4ff52e737438210296b8f2201d5e66e1d38b7bc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418277"
---
# <a name="monitor-the-process-server"></a>監視處理序伺服器

本文說明如何監視[Site Recovery](site-recovery-overview.md)處理序伺服器。

- 您設定內部部署 VMware Vm 和實體伺服器至 Azure 的災害復原時，會使用處理序伺服器。
- 根據預設處理序伺服器會在組態伺服器上執行。 當您部署組態伺服器時，它是預設情況下進行安裝。
- （選擇性） 若要擴充和處理大量的複寫機器及更高的磁碟區的複寫流量，您可以部署額外、 向外延展處理序伺服器。

[了解更多](vmware-physical-azure-config-process-server-overview.md)有關角色和處理序伺服器的部署。

## <a name="monitoring-overview"></a>監視概觀

因為處理序伺服器有這麼多的角色，特別是在複寫的資料快取、 壓縮和傳輸到 Azure，務必監視持續的處理序伺服器健全狀況。

有許多情況下，通常會影響處理序伺服器的效能。 影響效能的問題會影響階層式 VM 健康情況，最終將處理序伺服器和其複寫的機器推送至 「 重大 」 狀態。 下列情況：

- 大量的 Vm 會使用處理序伺服器，接近或超過建議的限制。
- 使用處理序伺服器的 Vm 具有高變換率。
- Vm 與處理序伺服器之間的網路輸送量並無法複寫資料上傳至處理序伺服器。
- 處理序伺服器與 Azure 之間的網路輸送量不足，無法上傳至 Azure 的複寫資料從處理序伺服器。

所有這些問題可能會影響 Vm 的復原點目標 (RPO)。 

**為什麼？** 因為 vm 產生復原點需要有一個共同的點在 VM 上的所有磁碟。 如果一個磁碟有高的變換率、 複寫變慢，或處理序伺服器並不太，它會影響如何有效率地建立復原點。

## <a name="monitor-proactively"></a>主動監視

若要避免問題處理序伺服器，請務必：

- 了解使用的處理序伺服器的特定需求[容量和調整指引](site-recovery-plan-capacity-vmware.md#capacity-considerations)，請確定處理序伺服器會部署並根據建議執行。
- 監視警示，並針對問題進行疑難排解，產生時，若要將有效地執行的處理序伺服器。


## <a name="process-server-alerts"></a>處理序伺服器警示

處理序伺服器會產生健康情況警示，在下表中彙總的數字。

**警示類型** | **詳細資料**
--- | ---
![Healthy][green] | 處理序伺服器已連線且狀況良好。
![警告][yellow] | 過去 15 分鐘內的 CPU 使用率 > 80%
![警告][yellow] | 過去 15 分鐘內的記憶體使用方式 > 80%
![警告][yellow] | 快取資料夾的可用空間 < 30%的過去 15 分鐘
![警告][yellow] | 過去 15 分鐘內未執行的處理序伺服器服務
![重要][red] | CPU 使用率 > 95%長達過去 15 分鐘
![重要][red] | 記憶體使用量 > 95%長達過去 15 分鐘
![重要][red] | 快取資料夾的可用空間 < 25%過去 15 分鐘
![重要][red] | 從處理序伺服器，15 分鐘沒有活動訊號。

![資料表索引鍵](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 處理序伺服器的整體健全狀況狀態根據產生的最差警示。



## <a name="monitor-process-server-health"></a>監視處理序伺服器健全狀況

您可以監視您的處理序伺服器的健全狀況狀態，如下所示： 

1. 若要監視的複寫健康情況和複寫的機器，以及其處理序伺服器，在保存庫中的狀態 >**複寫的項目**，按一下您想要監視的機器。
2. 在 **複寫健康情況**，您可以監視 VM 健康情況狀態。 按一下向下切入，取得錯誤詳細資料的狀態。

    ![VM 儀表板中的處理序伺服器健全狀況](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. 在 **處理序伺服器健全狀況**，您可以監視處理序伺服器的狀態。 如需詳細資料向下切入。

    ![VM 儀表板中的處理序伺服器詳細資料](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 健康狀態也可以監視的 VM 上使用的圖形表示。
    - 向外延展處理序伺服器會反白顯示為橙色，如果有與其建立關聯的警告和紅色，如果有任何重大的問題。 
    - 如果處理序伺服器在預設部署組態伺服器上執行，然後在組態伺服器就會反白據此。
    - 若要向下切入，按一下組態伺服器或處理序伺服器。 請注意任何問題，而且任何補救建議。

您也可以監視處理序下的保存庫中的伺服器**Site Recovery 基礎結構**。 在 **管理 Site Recovery 基礎結構**，按一下**組態伺服器**。 選取組態伺服器相關聯的處理序伺服器和向下切入往下到處理序伺服器詳細資料。


## <a name="next-steps"></a>後續步驟

- 如果您有任何處理伺服器的問題，請依照我們[疑難排解指引](vmware-physical-azure-troubleshoot-process-server.md)
- 如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
