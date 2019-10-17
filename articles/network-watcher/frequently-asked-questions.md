---
title: 關於 Azure 網路監看員的常見問題（FAQ） |Microsoft Docs
description: 本文會回答有關 Azure 網路監看員服務的常見問題。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: ef46c1a631a79dd1c50b2bf7d263538298de233f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333309"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>關於 Azure 網路監看員的常見問題（FAQ）
[Azure 網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)服務提供一套工具，可用來監視、診斷、查看計量，以及啟用或停用 Azure 虛擬網路中的資源記錄。 本文會回答有關此服務的常見問題。

## <a name="general"></a>一般

### <a name="what-is-network-watcher"></a>什麼是網路監看員？
網路監看員的設計是用來監視和修復 IaaS （基礎結構即服務）元件的網路健全狀況，其中包括虛擬機器、虛擬網路、應用程式閘道、負載平衡器，以及 Azure 虛擬網路中的其他資源。 它不是用來監視 PaaS （平臺即服務）基礎結構或取得 web/行動分析的解決方案。

### <a name="what-tools-does-network-watcher-provide"></a>網路監看員提供哪些工具？
網路監看員提供三組主要的功能
* 監視
  * [[拓撲] 視圖](https://docs.microsoft.com/azure/network-watcher/view-network-topology)會顯示虛擬網路中的資源，以及它們之間的關聯性。
  * [連接監視器](https://docs.microsoft.com/azure/network-watcher/connection-monitor)可讓您監視 VM 與另一個網路資源之間的連線和延遲。
  * [網路效能監視器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor)可讓您監視混合式網路架構、Expressroute 線路和服務/應用程式端點之間的連線能力和延遲。  
* 診斷程式
  * [IP 流量驗證](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)可讓您偵測 VM 層級的流量篩選問題。
  * [下一個躍點](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)可協助您驗證流量路由和偵測路由問題。
  * 連線[疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal)可讓 VM 與另一個網路資源之間進行一次性的連線和延遲檢查。
  * 封[包捕獲](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)可讓您在虛擬網路中的 VM 上，捕捉所有流量。
  * [Vpn 疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview)會針對您的 VPN 閘道和連線執行多個診斷檢查，以協助進行偵錯工具的問題。
* 記錄
  * [NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)可讓您記錄[網路安全性群組（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)中的所有流量
  * 使用[分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)會處理您的 NSG 流量記錄資料，讓您可以視覺化、查詢、分析及瞭解您的網路流量。


如需詳細資訊，請參閱[網路監看員總覽頁面](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)。


### <a name="how-does-network-watcher-pricing-work"></a>網路監看員定價如何運作？
請流覽[定價頁面](https://azure.microsoft.com/pricing/details/network-watcher/)以取得網路監看員元件和其定價。

### <a name="which-regions-is-network-watcher-available-in"></a>網路監看員提供哪些區域？
您可以在[Azure 服務可用性頁面](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)上查看最新的區域可用性

## <a name="nsg-flow-logs"></a>NSG 流量記錄

### <a name="what-does-nsg-flow-logs-do"></a>NSG 流量記錄有哪些功能？
您可以透過[網路安全性群組（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)結合和管理 Azure 網路資源。 NSG 流量記錄可讓您透過 Nsg 記錄所有流量的5個元組流量資訊。 未經處理的流量記錄會寫入 Azure 儲存體帳戶，讓您可以視需要進一步進行處理、分析、查詢或匯出。

### <a name="are-there-caveats-for-using-nsg-flow-logs"></a>使用 NSG 流量記錄有任何注意事項嗎？
使用 NSG 流量記錄沒有任何必要條件。 不過，有兩個限制
- **服務端點不得存在於您的 VNET 上**： NSG 流量記錄是從 vm 上的代理程式發出至儲存體帳戶。 不過，目前您只能將記錄直接發出至儲存體帳戶，而且無法使用新增至 VNET 的服務端點。

有兩種方式可以修正此問題：

*選項1：重新設定 NSG 流量記錄，以發出至沒有 VNET 端點的 Azure 儲存體帳戶*

* 尋找具有端點的子網路：

    - 在 Azure 入口網站上，於頂端的全域搜尋中搜尋**資源群組**
    - 瀏覽至所使用 NSG 所在的資源群組
    - 使用第二個下拉式清單依類型篩選並選取 [**虛擬網路**]
    - 按一下服務端點所在的虛擬網路
    - 從左窗格的 [設定] 下選取 [服務端點]
    - 記下已啟用 **Microsoft.Storage** 的子網路

* 停用服務端點：

    - 延續前面的操作，從左窗格的 [設定] 下選取 [子網路]
    * 按一下服務端點所在的子網路
    - 在 [服務端點] 區段的 [服務] 下，取消核取 [Microsoft.Storage]。

您可以在幾分鐘後檢查儲存體記錄，屆時應該就會看到已更新的時間戳記或新建立的 JSON 檔案。

*選項2：停用 NSG 流量記錄*

如果 Microsoft.Storage 服務端點是必須項目，您就必須停用 NSG 流量記錄。


- **儲存體帳戶不得防火牆**：由於內部限制，必須可以透過公用網際網路存取儲存體帳戶，NSG 流量記錄才能使用它們。 流量仍會在內部透過 Azure 路由傳送，而且您不會面臨額外的輸出費用。

藉由啟用「所有網路」來存取儲存體帳戶，即可解決此問題：

* 尋找儲存體帳戶的名稱，方法是在 [[NSG 流量記錄] 概觀頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)上找到 NSG。
* 在入口網站的全域搜尋中輸入儲存體帳戶的名稱，以瀏覽至儲存體帳戶
* 在 [設定] 區段下，選取 [防火牆和虛擬網路]
* 選取 [所有網路] 並加以儲存。 如果早已選取，則不需要再變更。  

這兩項限制都預期會在2020年1月之前解決。

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>流量記錄版本 1 & 2 之間的差異為何？
流量記錄第2版引進了*流程狀態*的概念，& 儲存傳輸位元組和封包的相關資訊。 [閱讀更多](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)。

## <a name="next-steps"></a>後續步驟
 - 請前往我們的[檔集總覽頁面](https://docs.microsoft.com/azure/network-watcher/)，瞭解一些教學課程，讓您開始使用網路監看員。
