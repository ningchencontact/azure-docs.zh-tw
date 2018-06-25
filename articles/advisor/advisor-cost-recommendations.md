---
title: Azure Advisor成本建議 | Microsoft Docs
description: 使用 Azure Advisor 程式將 Azure 部署的成本最佳化。
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: ade6ef996c00c0c06d5b8e44815520e6e4ab7e9f
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735862"
---
# <a name="advisor-cost-recommendations"></a>Azure Advisor 成本建議

Advisor 可找出閒置和未充分利用的資源，協助您減少 Azure 的整體費用並加以最佳化。 您可以從 Advisor 儀表板上的 [成本] 索引標籤取得成本建議。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>關閉使用量過低的執行個體，或者重新調整其大小或，從而將虛擬機器的費用最佳化 
雖然特定應用程式的設計情境可能導致低使用率，但您通常可以藉由調整虛擬機器的大小和數量來節省費用。 Advisor 可監視過去 14 天的虛擬機器使用量，並找出低使用率的虛擬機器。 CPU 使用率等於或低於 5% 且網路使用量等於或低於 7 MB 長達 4 天 (含) 以上的虛擬機器，會被視為低使用率虛擬機器。

Advisor 會顯示繼續執行虛擬機器的預估成本，以便您可以選擇將它關閉或調整其大小。

如果您想要更積極地識別使用量過低的虛擬機器，您可以依據個別訂用帳戶調整平均 CPU 使用率規則。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>移除未佈建的 ExpressRoute 線路來降低成本
Advisor 發現 ExpressRoute 線路的提供者狀態為「未佈建」已超過一個月，並建議如果您不打算透過連線提供者佈建該線路，請將其刪除。

## <a name="buy-virtual-machine-reserved-instances-to-save-money-over-pay-as-you-go-costs"></a>購買虛擬機器保留執行個體以省下較隨用隨付多的成本
Advisor 會檢閱您的虛擬機器在過去 30 天的使用量，並判斷購買保留執行個體是否可為您節省成本。 Advisor 會顯示可能省下最多成本的區域和大小，並顯示購買保留執行個體估計省下的成本。 

有了保留執行個體，您可以預先購買基本數量的虛擬機器。 針對其大小和區域與您保留執行個體相同之新的或現有的 VM，會自動套用折扣。 [深入了解 Azure 保留的 VM 執行個體。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

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
