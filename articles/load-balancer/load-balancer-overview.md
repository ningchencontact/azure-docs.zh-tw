---
title: 什麼是 Azure Load Balancer？
titleSuffix: Azure Load Balancer
description: Azure 負載平衡器功能、架構和實作的概觀。 了解 Load Balancer 的運作方式，以及如何將其用於雲端。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2019
ms.author: allensu
ms.openlocfilehash: 4f6a0a2631c3f7458548a2ff435c3e565e744f41
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660202"
---
# <a name="what-is-azure-load-balancer"></a>什麼是 Azure Load Balancer？

「負載平衡」  是指將負載或傳入的網路流量有效率地分散到一組後端資源或伺服器。 Azure 提供[各種負載平衡選項](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)，以供您根據自己的需要進行選擇。 本文件會說明 Azure Load Balancer。

Azure Load Balancer 會在開放系統互相連線 (OSI) 模型的第四層運作。 這是用戶端的單一連絡點。 Load Balancer 會根據指定的負載平衡規則和健康情況探查，將抵達 Load Balancer 前端的新輸入流程，分送給後端集區執行個體。 後端集區執行個體可以是 Azure 虛擬機器，也可以是虛擬機器擴展集中的執行個體。 

您可以使用 Azure Load Balancer 調整您的應用程式，並建立具備高可用性的服務。 對於所有 TCP 和 UDP 應用程式，Load Balancer 可支援傳入和傳出的場合、提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。

**[公用 Load Balancer](#publicloadbalancer)** 可以將虛擬網路內虛擬機器 (VM) 的私人 IP 位址轉譯成公用 IP 位址，為虛擬機器提供輸出連線。 公用 Load Balancer 可用來對進入 VM 的網際網路流量進行負載平衡。

**[內部 (或私用) Load Balancer](#internalloadbalancer)** 則可用於前端只需要私人 IP 位址的案例。 內部 Load Balancer 可用來對虛擬網路內的流量進行負載平衡。 在混合式的案例中，您也可以從內部部署網路連線到 Load Balancer 前端。

## <a name="load-balancer-components"></a>Load Balancer 元件
* **前端 IP 設定**：Load Balancer 的 IP 位址。 這是用戶端的連絡點。 這些設定可以是公用或私人 IP 位址，進而會分別建立公用或內部 Load Balancer。
* **後端集區**：將會為傳入要求提供服務的一組虛擬機器或虛擬機器擴展集中的執行個體。 若要以符合成本效益的方式進行擴充，以滿足大量的傳入流量運算，最佳做法通常會建議您在後端集區中新增更多執行個體。 當您相應增加或減少執行個體時，Load Balancer 會立即透過自動重新設定功能來自行重新設定。 從後端集區新增或移除 VM 會重新設定 Load Balancer，而不需要在 Load Balancer 資源上進行其他作業。
* **健康情況探查**：健康情況探查可用來判斷後端集區中執行個體的健康情況。 您可以為健康情況探查定義狀況不良臨界值。 當探查無法回應時，Load Balancer 會停止傳送新的連線至狀況不良的執行個體。 探查失敗不會影響現有的連線。 連線會繼續維持，直到應用程式終止流程、發生閒置逾時或 VM 關閉。 Load Balancer 會為 TCP、HTTP 和 HTTPS 端點提供不同的健康情況探查類型。 如需詳細資訊，請參閱[探查類型](load-balancer-custom-probe-overview.md#types)。
* **負載平衡規則**：負載平衡規則會告訴 Load Balancer 必須在何時完成什麼工作。 
* **輸入 NAT 規則**：輸入 NAT 規則會將來自特定前端 IP 位址的特定連接埠的流量，轉送至虛擬網路內特定後端執行個體的特定連接埠。 連接埠轉送作業會使用與負載平衡相同的雜湊式分送來完成。 此功能的常見案例是透過遠端桌面通訊協定 (RDP) 或安全殼層 (SSH) 工作階段連線至 Azure 虛擬網路內的個別 VM 執行個體。 您可以將多個內部端點對應至相同前端 IP 位址的連接埠。 您可以使用前端 IP 位址從遠端管理 VM，而不需要額外的跳躍箱。
* **輸出規則**：輸出規則會設定輸出網路位址轉譯 (NAT)，以便讓後端集區所識別的所有虛擬機器或執行個體轉譯至前端。

## <a name="load-balancer-concepts"></a>Load Balancer 概念

Load Balancer 為 TCP 和 UDP 應用程式提供下列基本功能：

* **負載平衡演算法**：有了 Azure Load Balancer，您就能建立負載平衡規則，將抵達前端的流量分送給各個後端集區執行個體。 Load Balancer 會使用雜湊演算法來分送輸入流程，並重寫流向後端集區執行個體的流程標頭。 當健康情況探查表示後端端點狀況良好時，伺服器便可以接收新的流程。
根據預設，Load Balancer 會使用 5 個元組的雜湊。 此雜湊包含來源 IP 位址、來源連接埠、目的地 IP 位址、目的地連接埠、IP 通訊協定數目，用以將流程對應至可用的伺服器。 您可以將 2 或 3 個元組的雜湊用於指定的規則，以建立來源 IP 位址的親和性。 相同封包流程的所有封包會抵達相同執行個體，執行個體位於已負載平衡的前端之後。 當用戶端從相同的來源 IP 起始新的流程時，來源連接埠將會變更。 因此，5 個元組的雜湊可能會使流量送往不同的後端端點。
如需詳細資訊，請參閱[設定 Azure Load Balancer 的分送模式](load-balancer-distribution-mode.md)。 下列影像顯示雜湊型分送：

  ![雜湊型分散](./media/load-balancer-overview/load-balancer-distribution.png)

  *圖：雜湊型分散*

* **應用程式獨立性和透明度**：Load Balancer 不會直接與 TCP 或 UDP 或應用程式層互動。 任何 TCP 或 UDP 應用程式案例均受支援。 Load Balancer 不會終止或產生流程、與流程的承載互動，或提供任何應用層閘道功能。 通訊協定交握一律會在用戶端與後端集區執行個體之間直接執行。 輸入流程的回應一律是來自虛擬機器的回應。 當流程到達虛擬機器時，也會保留原始的來源 IP 位址。
  * 每個端點只會由 VM 回應。 例如，TCP 交握一律是在用戶端和選取的後端 VM 之間發生。 前端要求的回應是由後端 VM 所產生的回應。 當您已順利驗證前端連線時，您至少要驗證一個後端虛擬機器的端對端連線。
  * 對 Load Balancer 而言，應用程式承載是透明可見的。 任何 UDP 或 TCP 應用程式均受支援。
  * 由於 Load Balancer 不會與 TCP 裝載互動及提供 TLS 卸載，您可以建置端對端加密的案例。 使用 Load Balancer，可藉由終止 VM 本身的 TLS 連線，讓 TLS 應用程式進行大規模的向外延展。 舉例而言，您的 TLS 工作階段金鑰處理容量只會受限於您新增至後端集區的 VM 類型和數量。

* **輸出連線 (SNAT)** ：所有從虛擬網路內私人 IP 位址送往網際網路上公用 IP 位址的輸出流程，皆可以轉譯為 Load Balancer 的前端 IP 位址。 當公用前端透過負載平衡規則繫結至後端 VM 時，Azure 會將輸出連線轉譯為公用前端 IP 位址。 此組態有下列優點：
  * 能夠輕鬆進行服務的升級及災害復原，因為前端可以動態對應到服務的另一個執行個體。
  * 讓存取控制清單 (ACL) 管理變得更容易。 當服務相應增加、相應減少或重新部署時，以前端 IP 表示的 ACL 不會變更。 將輸出連線轉譯成數量比機器更少的 IP 位址，可以減輕實作安全收件者清單的負擔。
如需詳細資訊，請參閱 [Azure 中的輸出連線](load-balancer-outbound-connections.md)。
除了這些基本功能，Standard Load Balancer 還有其他的 SKU 專屬功能，如下所述。

## <a name="skus"></a>Load Balancer SKU 的比較

Load Balancer 支援基本和標準 SKU。 這些 SKU 的案例規模、功能、價格各不相同。 任何可在基本 Load Balancer 建立的案例，都可以使用 Standard Load Balancer 來建立。 這兩個 SKU 的 API 類似，均透過指定 SKU 來叫用。 從 `2017-08-01` API 開始，已提供可支援 Load Balancer 和公用 IP 之 SKU 的 API。 這兩個 SKU 共用相同的一般 API 和結構。

但完整的案例組態可能稍有不同，視 SKU 而定。 當文章僅適用於特定 SKU 時，Load Balancer 文件才會加以標注。 若要比較並了解其中的差異，請參閱下表。 如需詳細資訊，請參閱 [Azure Standard Load Balancer 概觀](load-balancer-standard-overview.md)。

>[!NOTE]
> Microsoft 建議使用 Standard Load Balancer。
獨立 VM、可用性設定組和虛擬機器擴展集都只能和一個 SKU 連線，永遠不能和兩者同時連線。 與公用 IP 位址搭配使用時，Load Balancer 和公用 IP 位址的 SKU 必須相符。 Load Balancer 和公用 IP 的 SKU 不可變動。

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

如需詳細資訊，請參閱[負載平衡器限制](https://aka.ms/lblimits)。 如需標準 Load Balancer 詳細資料，請參閱[概觀](load-balancer-standard-overview.md)、[定價](https://aka.ms/lbpricing)及 [SLA](https://aka.ms/lbsla)。

## <a name="load-balancer-types"></a>Load Balancer 類型

### <a name = "publicloadbalancer"></a>公用 Load Balancer

公用 Load Balancer 會將傳入流量的公用 IP 位址和連接埠對應至 VM 的私人 IP 位址和連接埠。 對於來自 VM 的回應流量，Load Balancer 會以相反的方式對應流量。 您可以套用負載平衡規則，以將特定類型的流量分散至多個 VM 或服務。 例如，您可以將 Web 要求的流量負載分散在多個 Web 伺服器。

>[!NOTE]
>對於每個可用性設定組，您只能執行一個公用 Load Balancer 和一個內部 Load Balancer。

下圖顯示在三個 VM 間共用，且公用和 TCP 連接埠均為 80 的 Web 流量負載平衡端點。 這三部 VM 均位在負載平衡集中。

![公用 Load Balancer 範例](./media/load-balancer-overview/IC727496.png)

*圖：使用公用 Load Balancer 來平衡 Web 流量負載*

網際網路用戶端會在 TCP 連接埠 80 上，將網頁要求傳送至 Web 應用程式的公用 IP 位址。 Azure Load Balancer 會將要求分散到負載平衡集內的三個 VM。 如需 Load Balancer 演算法的詳細資訊，請參閱 [Load Balancer 概念](load-balancer-overview.md##load-balancer-concepts)。

根據預設，Azure Load Balancer 會在多個 VM 執行個體之間均分網路流量。 您也可以設定工作階段親和性。 如需詳細資訊，請參閱[設定 Azure Load Balancer 的分送模式](load-balancer-distribution-mode.md)。

### <a name = "internalloadbalancer"></a>內部 Load Balancer

相對於公用 Load Balancer，內部 Load Balancer 只會將流量導向至位於虛擬網路內的資源，或導向至使用 VPN 存取 Azure 基礎結構的資源。 Azure 基礎結構會限制存取虛擬網路的負載平衡前端 IP 位址。 前端 IP 位址與虛擬網路絕不會直接公開至網際網路端點。 內部企業營運應用程式會在 Azure 中執行，並且可從 Azure 內或內部部署資源內存取。

內部 Load Balancer 可提供下列幾種類型的負載平衡：

* **虛擬網路內**：從虛擬網路中的 VM 負載平衡至一組位於相同虛擬網路中的 VM。
* **在跨單位虛擬網路中**：從內部部署電腦負載平衡至一組位於相同虛擬網路中的 VM。
* **多層式應用程式**：對後端層並非網際網路對向的網際網路對向多層式應用程式進行負載平衡。 後端層需要來自網際網路對向層的流量負載平衡。 請參閱下圖。
* **針對企業營運應用程式**：在 Azure 中代管的企業營運應用程式的負載平衡，而不需要額外的負載平衡器硬體或軟體。 此案例包括流量已進行負載平衡的電腦集所包含的內部部署伺服器。

![內部 Load Balancer 範例](./media/load-balancer-overview/IC744147.png)

*圖：使用公用和內部 Load Balancer 進行多層式應用程式的平衡*

## <a name="pricing"></a>價格

使用 Standard Load Balancer 需要付費。

* 已設定的負載平衡和輸出規則的數目。 輸入 NAT 規則不會計入規則總數中。
* 已處理的輸入和輸出資料量與規則無關。

如需 Standard Load Balancer 的定價資訊，請參閱 [Load Balancer 定價](https://azure.microsoft.com/pricing/details/load-balancer/)。

基本 Load Balancer 則是免費提供。

## <a name="sla"></a>SLA

如需 Standard Load Balancer SLA 的相關資訊，請參閱 [Load Balancer 的 SLA](https://aka.ms/lbsla)。

## <a name="limitations"></a>限制

* Load Balancer 提供針對特定 TCP 或 UDP 通訊協定進行負載平衡和連接埠轉送的功能。 負載平衡規則和輸入 NAT 規則支援 TCP 和 UDP，但不支援其他 IP 通訊協定，包括 ICMP。

  Load Balancer 並不會終止、回應或與 UDP 或 TCP 流程的承載互動。 它並非 Proxy。 對前端的連線必須在負載平衡或是 NAT 傳入規則所使用的相同通訊協定中，成功進行頻內驗證。 至少必須有一個虛擬機器為用戶端產生回應，才能看到來自前端的回應。

  若未從 Load Balancer 前端接收到頻內回應，表示沒有任何虛擬機器能夠回應。 在虛擬機器皆無法回應的情況下，無法與 Load Balancer 前端進行任何互動。 這項準則也適用於連接埠偽裝 SNAT 僅支援 TCP 和 UDP 的輸出連線。 包括 ICMP 在內的任何其他 IP 通訊協定都會失敗。 指派執行個體層級的公用 IP 可緩解此問題。 如需詳細資訊，請參閱[了解 SNAT 和 PAT](load-balancer-outbound-connections.md#snat)。

* 內部 Load Balancer 不會將輸出起始連線轉譯為內部 Load Balancer 的前端，因為這兩者都位於私人 IP 位址空間中。 公用 Load Balancer 提供從虛擬網路內的私人 IP 位址到公用 IP 位址的[輸出連線](load-balancer-outbound-connections.md)。 對於內部 Load Balancer，此方法可避免在無需轉譯的專屬內部 IP 位址空間中將 SNAT 連接埠耗盡。

  其副作用是，如果後端集區 VM 的輸出流程嘗試將流量流程至其集區中的內部 Load Balancer 前端，並且  對應回本身，則這兩個流程互不相符。 由於兩者不相符，流程將會失敗。 如果流程未對應回後端集區中建立前端流程的相同 VM，流程就會成功。

  當流程對應回本身時，傳出流程似乎是來自 VM 而傳至前端，而對應的傳入流程似乎來自 VM 至而傳至本身。 以客體作業系統來看，相同流程的傳入和傳出部分在虛擬機器內部不相符。 TCP 堆疊無法將其中半數的流程視為相同流程的一部分。 來源與目的地不相符。 當流程對應至後端集區中的任何其他 VM 時，半數流程將會相符，而 VM 就能回應流程。

  此案例的徵兆是當流量回到其源自的相同後端時，發生間歇性連線逾時。 常見的因應措施包括在內部 Load Balancer 後方插入 Proxy 層，以及使用伺服器直接回傳 (DSR) 樣式規則。 如需詳細資訊，請參閱[多個 Azure Load Balancer 前端](load-balancer-multivip-overview.md)。

  您可以結合內部 Load Balancer 與任何第三方 Proxy，或將內部[應用程式閘道](../application-gateway/application-gateway-introduction.md)用於使用 HTTP/HTTPS 的 Proxy 案例。 儘管可以使用公用 Load Balancer 來緩解此問題，但產生的案例容易造成 [SNAT 耗盡](load-balancer-outbound-connections.md#snat)。 除非謹慎管理，否則請避免使用此替代方法。

* 一般而言，負載平衡規則不支援轉送 IP 片段。 負載平衡規則不支援 UDP 和 TCP 封包的 IP 分散。 高可用性連接埠負載平衡規則可用來轉送現有的 IP 片段。 如需詳細資訊，請參閱[高可用性連接埠概觀](load-balancer-ha-ports-overview.md)。

## <a name="next-steps"></a>後續步驟

請參閱[建立公用 Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md)，以開始使用 Load Balancer：先建立 Load Balancer，再建立安裝好自訂 IIS 擴充功能的 VM，並對 VM 之間的 Web 應用程式進行負載平衡。
