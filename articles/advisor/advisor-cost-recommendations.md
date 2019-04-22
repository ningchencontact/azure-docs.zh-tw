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
ms.openlocfilehash: 188a79df99a174436808acd3d964abf9357cf4c0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699316"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>使用 Azure Advisor 降低服務成本

Advisor 可找出閒置和未充分利用的資源，協助您減少 Azure 的整體費用並加以最佳化。 您可以從 Advisor 儀表板上的 [成本] 索引標籤取得成本建議。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>關閉使用量過低的執行個體，或者重新調整其大小或，從而將虛擬機器的費用最佳化 

雖然特定應用程式的設計情境可能導致低使用率，但您通常可以藉由調整虛擬機器的大小和數量來節省費用。 顾问可监视虚拟机 7 天的使用情况，并识别出利用率较低的虚拟机。 以下情况可以将虚拟机视为使用率低：其 CPU 使用率为 5% 或更低且其网络使用率低于 2%，或者更小的虚拟机大小可以容纳当前工作负荷。

Advisor 會顯示繼續執行虛擬機器的預估成本，以便您可以選擇將它關閉或調整其大小。

如果您想要更積極地識別使用量過低的虛擬機器，您可以依據個別訂用帳戶調整平均 CPU 使用率規則。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>移除未佈建的 ExpressRoute 線路來降低成本

Advisor 發現 ExpressRoute 線路的提供者狀態為「未佈建」已超過一個月，並建議如果您不打算透過連線提供者佈建該線路，請將其刪除。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>藉由刪除或重新設定閒置虛擬網路閘道來降低成本

Advisor 會找出已閒置超過 90 天的虛擬網路閘道。 由於這些閘道是以小時計費，因此，當您不再使用這些閘道時，應考慮重新設定或刪除閘道。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>購買保留執行個體以省下較隨用隨付多的成本

Advisor 會檢閱您的虛擬機器在過去 30 天的使用量，並判斷購買 Azure 保留是否可為您節省成本。 Advisor 會顯示可能省下最多成本的區域和大小，並顯示購買保留估計省下的成本。 有了 Azure 保留，您可以預先購買基本數量的虛擬機器。 針對其大小和區域與您保留相同的新建或現有 VM，會自動套用折扣。 [深入了解 Azure 保留的 VM 執行個體。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor 也會通知您的保留執行個體，您必須將在接下來的 30 天後到期。 它會建議您購買新的保留執行個體，以避免支付隨用隨付定價。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>删除未关联的公共 IP 地址可节省资金

顾问可以标识目前未关联到 Azure 资源（例如负载均衡器或 VM）的公共 IP 地址。 这些公共 IP 地址会产生少许费用。 如果不打算使用它们，删除它们可以节省成本。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何存取 Azure Advisor 中的成本建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [成本] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [快速入門](advisor-get-started.md)
* [Advisor 效能建議](advisor-cost-recommendations.md)
* [Advisor 高可用性建議](advisor-cost-recommendations.md)
* [Advisor 安全性建議](advisor-cost-recommendations.md)
