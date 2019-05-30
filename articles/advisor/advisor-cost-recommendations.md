---
title: 使用 Azure Advisor 降低服務成本 | Microsoft Docs
description: 使用 Azure Advisor 程式將 Azure 部署的成本最佳化。
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: fef7b82e6969de16d1815250d2373c99021b0e86
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254728"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>使用 Azure Advisor 降低服務成本

Advisor 可找出閒置和未充分利用的資源，協助您減少 Azure 的整體費用並加以最佳化。 您可以從 Advisor 儀表板上的 [成本]  索引標籤取得成本建議。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>關閉使用量過低的執行個體，或者重新調整其大小或，從而將虛擬機器的費用最佳化 

雖然特定應用程式的設計情境可能導致低使用率，但您通常可以藉由調整虛擬機器的大小和數量來節省費用。 Advisor 監視 7 天的虛擬機器使用量，然後找出低使用率虛擬機器。 虛擬機器會被視為低使用率的 CPU 使用率為 5%或更少且其網路使用率低於 2%或目前的工作負載是否可以容納由較小的虛擬機器大小。

Advisor 會顯示繼續執行虛擬機器的預估成本，以便您可以選擇將它關閉或調整其大小。

如果您想要更積極地識別使用量過低的虛擬機器，您可以依據個別訂用帳戶調整平均 CPU 使用率規則。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>移除未佈建的 ExpressRoute 線路來降低成本

Advisor 發現 ExpressRoute 線路的提供者狀態為「未佈建」  已超過一個月，並建議如果您不打算透過連線提供者佈建該線路，請將其刪除。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>藉由刪除或重新設定閒置虛擬網路閘道來降低成本

Advisor 會找出已閒置超過 90 天的虛擬網路閘道。 由於這些閘道是以小時計費，因此，當您不再使用這些閘道時，應考慮重新設定或刪除閘道。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>購買保留執行個體以省下較隨用隨付多的成本

Advisor 會檢閱您的虛擬機器在過去 30 天的使用量，並判斷購買 Azure 保留是否可為您節省成本。 Advisor 會顯示可能省下最多成本的區域和大小，並顯示購買保留估計省下的成本。 有了 Azure 保留，您可以預先購買基本數量的虛擬機器。 針對其大小和區域與您保留相同的新建或現有 VM，會自動套用折扣。 [深入了解 Azure 保留的 VM 執行個體。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor 也會通知您的保留執行個體，您必須將在接下來的 30 天後到期。 它會建議您購買新的保留執行個體，以避免支付隨用隨付定價。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>刪除關聯的公用 IP 位址，以節省成本

Advisor 會識別不是目前 Azure 資源，例如負載平衡器或 Vm 相關聯的公用 IP 位址。 這些公用 IP 位址會隨附收取少許費用。 如果您不打算使用它們，則刪除它們會導致成本節約。

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>刪除失敗的 Azure Data Factory 管線

Azure Advisor 會偵測到重複失敗，而且建議您解決問題，或刪除失敗的管線，如果不再需要的 Azure Data Factory 管線。 您將支付這些管線即使不過他們不會處理您時它們會失敗。 

## <a name="use-standard-snapshots-for-managed-disks"></a>使用受控磁碟的標準快照集
若要儲存 60%的成本，我們建議將您的快照集儲存在標準儲存體，不論父系磁碟的儲存體類型。 這是受控磁碟快照集的預設選項。 Azure Advisor 會識別會儲存進階儲存體，建議將您的快照集從高階移轉至標準儲存體的快照集。 [深入了解受控磁碟定價](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何存取 Azure Advisor 中的成本建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [成本]  索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [快速入門](advisor-get-started.md)
* [Advisor 效能建議](advisor-cost-recommendations.md)
* [Advisor 高可用性建議](advisor-cost-recommendations.md)
* [Advisor 安全性建議](advisor-cost-recommendations.md)
