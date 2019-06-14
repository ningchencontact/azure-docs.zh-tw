---
title: 如何設定 Azure Cache for Redis 的異地複寫 | Microsoft Docs
description: 了解如何跨地理區域複寫您的 Azure Cache for Redis 執行個體。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60552286"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>如何設定 Azure Cache for Redis 的異地複寫

異地複寫提供一個機制，可連結兩個進階層 Azure Cache for Redis 執行個體。 一個快取會被選為主要連結快取，而另一個則為次要連結快取。 次要連結快取會變成唯讀，而寫入主要快取的資料會複寫至次要連結快取。 這項功能可用來跨 Azure 區域複寫快取。 本文提供的指南說明如何設定進階層 Azure Cache for Redis 執行個體的異地複寫。

## <a name="geo-replication-prerequisites"></a>異地複寫的必要條件

若要設定兩個快取之間的異地複寫，必須符合下列必要條件：

- 這兩個快取[進階層](cache-premium-tier-intro.md)會快取。
- 這兩個快取位於相同的 Azure 訂用帳戶。
- 次要連結快取是快取大小相同或較大的快取大小超過主要連結快取。
- 這兩個快取會建立並處於執行中狀態。

使用異地複寫不支援某些功能：

- 異地複寫不支援持續性。
- 如果這兩個快取啟用叢集，而且相同的分區數目，則支援叢集。
- 支援相同的 VNET 中的快取。
- 支援不同 Vnet 中的快取的注意事項。 請參閱[可以使用異地複寫搭配 VNET 中的快取？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)如需詳細資訊。

在設定異地複寫之後，下列限制適用於連結的快取組：

- 次要連結快取是唯讀的；您可以從中讀取，但無法寫入任何資料。 
- 任何將連結新增之前就已在次要連結快取中的資料都會加以移除。 如果地理複寫晚不過，移除複寫的資料會保留在次要連結快取中。
- 您不能[擴展](cache-how-to-scale.md)時快取連結在任一個快取。
- 您不能[變更分區數目](cache-how-to-premium-clustering.md)如果快取已啟用叢集。
- 您無法啟用任一個快取的持續性。
- 您可以[匯出](cache-how-to-import-export-data.md#export)從任一個快取。
- 您不能[匯入](cache-how-to-import-export-data.md#import)到次要連結快取。
- 您無法刪除連結快取或包含它們，直到您取消連結快取的資源群組。 如需詳細資訊，請參閱[當我嘗試刪除連結快取時，作業失敗的原因？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- 如果快取位於不同區域中，網路輸出費用適用於在區域之間移動資料。 如需詳細資訊，請參閱[跨 Azure 區域複寫我的資料需要多少費用？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- 主要和次要連結快取之間，不進行自動容錯移轉。 如需詳細資訊和如何容錯移轉的用戶端應用程式的詳細資訊，請參閱[容錯移轉至次要連結快取如何運作？](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>新增異地複寫連結

1. 若要將兩個快取連結在一起進行異地複寫，通過拳擊第按一下**異地複寫**從快取的資源功能表要設為主要連結快取。 接下來，按一下**新增快取複寫連結**從**異地複寫**刀鋒視窗。

    ![新增連結](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 按一下您想要的次要快取中的名稱**相容的快取**清單。 如果您的次要快取未顯示在清單中，確認[異地複寫的必要條件](#geo-replication-prerequisites)符合次要快取。 若要篩選依區域的快取，按一下地圖來顯示這些中的快取中的區域**相容的快取**清單。

    ![異地複寫相容的快取](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    您也可以啟動連結的程序，或使用操作功能表，檢視有關次要快取的詳細資料。

    ![異地複寫操作功能表](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. 按一下 [連結]  可將兩個快取連結在一起，並開始複寫流程。

    ![連結快取](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. 您可以在 [異地複寫]  刀鋒視窗上檢視複寫流程的進度。

    ![連結狀態](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    您也可以在 [概觀]  刀鋒視窗上檢視主要和次要快取的連結狀態。

    ![快取狀態](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    一旦複寫程序完成之後，[連結狀態]  會變為 [成功]  。

    ![快取狀態](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    在 連結的程序期間，主要連結快取仍然可供使用。 連結的程序完成之前，次要連結快取無法使用。

## <a name="remove-a-geo-replication-link"></a>移除異地複寫連結

1. 若要將兩個快取之間的連結移除並停止異地複寫，請從 [異地複寫]  刀鋒視窗按一下 [取消連結快取]  。
    
    ![取消連結快取](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    取消連結流程完成時，次要快取就可供讀取和寫入。

>[!NOTE]
>將異地複寫連結移除時，從主要連結快取複寫的資料就會保留在次要快取中。
>
>

## <a name="geo-replication-faq"></a>異地複寫常見問題集

- [可以使用異地複寫搭配標準或基本層快取嗎？](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [在連結或取消連結流程期間，快取是否可供使用？](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [可以同時連結兩個以上的快取嗎？](#can-i-link-more-than-two-caches-together)
- [可以將不同 Azure 訂用帳戶中的兩個快取加以連結嗎？](#can-i-link-two-caches-from-different-azure-subscriptions)
- [可以將兩個不同大小的快取加以連結嗎？](#can-i-link-two-caches-with-different-sizes)
- [啟用叢集時可以使用異地複寫嗎？](#can-i-use-geo-replication-with-clustering-enabled)
- [可以使用異地複寫搭配 VNET 中的快取嗎？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [哪個複寫排程適用於 Redis 異地複寫？](#what-is-the-replication-schedule-for-redis-geo-replication)
- [異地複寫需要花費多久的時間？](#how-long-does-geo-replication-replication-take)
- [是否保證複寫復原點？](#is-the-replication-recovery-point-guaranteed)
- [可以使用 PowerShell 或 Azure CLI 管理異地複寫嗎？](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [跨 Azure 區域複寫我的資料需要多少費用？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [當我嘗試刪除連結快取時，作業失敗的原因？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [要將次要連結快取用於哪個區域？](#what-region-should-i-use-for-my-secondary-linked-cache)
- [容錯移轉至次要連結快取如何運作？](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>可以使用異地複寫搭配標準或基本層快取嗎？

否，異地複寫僅適用於高階層快取。

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>在連結或取消連結流程期間，快取是否可供使用？

- 連結時，主要連結快取仍可使用連結的程序完成時。
- 連結時，次要連結快取連結流程完成之前無法使用。
- 取消連結，則取消連結流程完成時，仍會提供兩個快取。

### <a name="can-i-link-more-than-two-caches-together"></a>可以同時連結兩個以上的快取嗎？

否，您只可以同時連結兩個快取。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>可以將不同 Azure 訂用帳戶中的兩個快取加以連結嗎？

否，這兩個快取必須位於相同的 Azure 訂用帳戶。

### <a name="can-i-link-two-caches-with-different-sizes"></a>可以將兩個不同大小的快取加以連結嗎？

是，前提是次要連結快取超過主要連結快取。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>啟用叢集時可以使用異地複寫嗎？

是，前提是這兩個快取具有相同的分區數目。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>可以使用異地複寫搭配 VNET 中的快取嗎？

是，Vnet 中的快取的異地複寫可支援注意事項：

- 支援在相同 VNET 中多個快取之間的異地複寫。
- 也支援不同 Vnet 中的快取之間的異地複寫。
  - 如果 Vnet 位於相同的區域，您可以將它們連接使用[VNET 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或是[VPN 閘道 VNET 對 VNET 連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)。
  - 如果 Vnet 位於不同區域中異地複寫使用 VNET 對等互連不受支援，因為基本的內部負載平衡器的條件約束。 如需 VNET 對等互連的條件約束的詳細資訊，請參閱[虛擬網路對等互連-需求和限制](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)。 建議的解決方案是使用 VPN 閘道 VNET 對 VNET 連線。

使用[這個 Azure 範本](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)，您可以快速地部署兩個的異地複寫快取，到連接的 VPN 閘道 VNET 對 VNET 連線的 VNET。

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>哪個複寫排程適用於 Redis 異地複寫？

複寫是連續且非同步的而且不會發生在特定排程。 完成到主要複本的所有寫入會立即並以非同步方式都複寫的次要複本上。

### <a name="how-long-does-geo-replication-replication-take"></a>異地複寫需要花費多久的時間？

複寫是累加式、 非同步和持續和跨區域所花費的時間不太大的延遲。 在某些情況下，次要快取可能必須從主要進行完整同步處理的資料。 在此情況下複寫時間會取決於許多因素，例如： 主要快取、 可用的網路頻寬和區域間延遲上的負載。 我們發現完整 53 GB 異地複寫配對的複寫時間會介於 5 到 10 分鐘。

### <a name="is-the-replication-recovery-point-guaranteed"></a>是否保證複寫復原點？

在異地複寫模式中的快取，會停用持續性。 如果未連結，例如由客戶起始的容錯移轉的異地複寫 」 組，次要連結快取會保留其同步處理的資料，當時的時間為止。 在此情況下不保證任何復原點。

若要取得復原點，[匯出](cache-how-to-import-export-data.md#export)從任一個快取。 您可以稍後[匯入](cache-how-to-import-export-data.md#import)到主要連結快取。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>可以使用 PowerShell 或 Azure CLI 管理異地複寫嗎？

是，您可以使用 Azure 入口網站、 PowerShell 或 Azure CLI 來管理異地複寫。 如需詳細資訊，請參閱 < [PowerShell 文件](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache)或是[Azure CLI 文件](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>跨 Azure 區域複寫我的資料需要多少費用？

使用異地複寫時，主要連結快取的資料會複寫到次要連結快取。 如果兩個連結快取位於相同區域中，沒有資料傳輸為免費。 如果兩個連結快取位於不同區域中，資料傳輸費用會是在其中一個區域之間移動資料的網路輸出成本。 如需詳細資訊，請參閱[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>當我嘗試刪除連結快取時，作業失敗的原因？

無法刪除異地複寫的快取和其資源群組，而連結到您移除異地複寫連結為止。 如果您嘗試將包含一個或兩個連結快取的資源群組刪除，就會將資源群組中的其他資源刪除，但資源群組會保留在 `deleting` 狀態，而資源群組中的任何連結快取會維持 `running` 狀態。 若要完全刪除資源群組和連結的快取中，取消連結快取中所述[移除異地複寫連結](#remove-a-geo-replication-link)。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>要將次要連結快取用於哪個區域？

一般情況下，建議您快取中存取它的應用程式位於相同 Azure 區域。 對於具有不同的主要和後援區域的應用程式，建議您的主要和次要快取位於這些相同的區域。 如需配對區域的詳細資訊，請參閱[最佳做法 – Azure 配對的區域](../best-practices-availability-paired-regions.md)。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>容錯移轉至次要連結快取如何運作？

跨 Azure 區域的自動容錯移轉不支援異地複寫的快取。 在災害復原案例中，客戶應該會顯示整個應用程式堆疊中協調的方式在其備份的區域中。 讓個別的應用程式元件決定何時要切換至其自己的備份可以效能產生負面影響。 Redis 的主要優點之一是它是非常低延遲存放區。 如果客戶的主要應用程式在其快取以外的不同區域中，新增的來回時間會對效能的一個明顯的影響。 基於這個理由，我們避免自動容錯移轉因為暫時性的可用性問題。

若要啟動由客戶起始的容錯移轉，您必須先取消連結快取。 然後，變更您的 Redis 用戶端使用 （先前稱為連結） 次要快取的 「 連線 」 端點。 取消連結兩個快取時，次要快取就會再次變成一般的讀寫快取，並接受直接來自 Redis 用戶端要求。

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Cache for Redis 進階層](cache-premium-tier-intro.md)。
