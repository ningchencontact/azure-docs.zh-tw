---
title: Azure 標準 Load Balancer 概觀 | Microsoft Docs
description: Azure 標準 Load Balancer 功能概觀
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: d7ee74a19f806faed0bcfcfa5f1c5de3937d9f31
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="azure-load-balancer-standard-overview"></a>Azure Load Balancer Standard 概觀

Azure Load Balancer 可讓您調整您的應用程式，並為您的服務建立高可用性。 對於所有的 TCP 和 UDP 應用程式，Load Balancer 皆可用於輸入和輸出案例，並可提供低延遲和高輸送量，且最多可相應增加為數百萬個流程。 

本文將著重於標準 Load Balancer。  如需 Azure Load Balancer 的一般概觀，請另行檢閱[負載平衡器概觀](load-balancer-overview.md)。

## <a name="what-is-standard-load-balancer"></a>什麼是標準 Load Balancer？

標準 Load Balancer 是適用於所有 TCP 和 UDP 應用程式的新款 Load Balancer 產品，具有比基本 Load Balancer 更豐富、更細密的功能。  雖然兩者有許多相似之處，但請務必熟悉本文所說明的差異。

您可以將標準 Load Balancer 作為公用或內部 Load Balancer。 此外，虛擬機器可以連接至一個公用和一個內部 Load Balancer 資源。

Load Balancer 資源的運作方式，一律以前端、規則、健康情況探查和後端集區定義來表示。  一個資源可包含多項規則。 您可以從虛擬機器的 NIC 資源指定後端集區，以將虛擬機器放入後端集區中。  在使用虛擬機器擴展集時，此參數會傳遞至網路設定檔並擴充。

資源的虛擬網路範圍，是重要層面之一。  基本 Load Balancer 存在於可用性設定組的範圍內，而標準 Load Balancer 則與虛擬網路範圍完全整合，並且適用所有的虛擬網路概念。

Load Balancer 資源是一種物件，而您可以在其中表示 Azure 應如何對其多租用戶基礎結構進行設計程式，以達到您想要建立的案例。  Load Balancer 資源與實際的基礎結構之間沒有直接關聯性；建立 Load Balancer 並不會建立執行個體，容量一直都在，且無須考量啟動或調整延遲的問題。 

>[!NOTE]
> Azure 可為您的案例提供一套受到完整管理的負載平衡解決方案。  如果您需要 TLS 終止 (「SSL 卸載」) 或每一 HTTP/HTTPS 要求的應用程式層處理，請參閱[應用程式閘道](../application-gateway/application-gateway-introduction.md)。  如果您需要全域 DNS 負載平衡，請參閱[流量管理員](../traffic-manager/traffic-manager-overview.md)。  視需要結合這些解決方案，您的端對端案例可能因而受益。

## <a name="why-use-standard-load-balancer"></a>為何要使用標準 Load Balancer？

您可以將標準 Load Balancer 用於各種規模的虛擬資料中心，從小規模的部署到大型且複雜的多重區域架構均適用。

請檢閱下表，大致了解標準 Load Balancer 與基本 Load Balancer 之間的差異：

>[!NOTE]
> 新的設計應考慮使用標準 Load Balancer。 

| | 標準 SKU | 基本 SKU |
| --- | --- | --- |
| 後端集區大小 | 最多 1000 個執行個體 | 最多 100 個執行個體 |
| 後端集區端點 | 單一虛擬網路中的任何虛擬機器，包括虛擬機器混合、可用性設定組、虛擬機器擴展集。 | 單一可用性設定組或虛擬機器擴展集中的虛擬機器 |
| 可用性區域 | 輸入和輸出的區域備援和區域性前端、輸出流程對應存活區域失敗、跨區域負載平衡 | / |
| 診斷 | Azure 監視器、多維度計量 (包括位元組和封包計數器)、健康情況探查狀態、連線嘗試 (TCP SYN)、輸出連線的健康情況 (SNAT 成功和失敗的流程)、作用中資料平面測量 | 僅適用於公用 Load Balancer 的 Azure Log Analytics、SNAT 耗盡警示、後端集區健康情況計數 |
| HA 連接埠 | 內部 Load Balancer | / |
| 預設保護 | 針對公用 IP 和 Load Balancer 端點的保護預設為關閉，需使用網路安全性群組明確將流程的流量加入白名單 | 預設為開啟，網路安全性群組為選用項目 |
| 輸出連線 | 多個具有選擇退出規則的前端。「必須」明確建立輸出案例，虛擬機器才能使用輸出連線能力。  [VNet 服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)不需要輸出連線即可聯繫，且不會計入處理的資料。  任何公用 IP 位址都必須透過輸出連線來聯繫 (包括無法作為 VNet 服務端點的 Azure PaaS 服務)，且會計入處理的資料。 僅以一個內部 Load Balancer 為虛擬機器提供服務時，就無法透過預設 SNAT 進行輸出連線。 輸出 SNAT 的程式設計依傳輸通訊協定而異，以輸入負載平衡規則的通訊協定為準。 | 單一前端，有多個前端存在時會隨機選取。  僅以一個內部 Load Balancer 為虛擬機器提供服務時，會使用預設 SNAT。 |
| 多個前端 | 輸入和輸出 | 僅輸入 |
| 管理作業 | 大部分的作業 < 30 秒 | 通常是 60-90+ 秒 |
| SLA | 99.99% (當資料路徑兩端的兩個虛擬機器狀況良好時) | VM SLA 中不言明 | 
| 價格 | 根據規則數目、與資源相關聯的輸入或輸出處理資料來計費  | 不收費 |

請檢閱 [Load Balancer 的服務限制](https://aka.ms/lblimits)、[定價](https://aka.ms/lbpricing)和 [SLA](https://aka.ms/lbsla)。


### <a name="backend"></a>後端集區

標準 Load Balancer 後端集區可擴充至虛擬網路中的任何虛擬機器資源。  它最多可包含 1000 個後端執行個體。  後端執行個體是 IP 組態，也就是 NIC 資源的屬性。

後端集區可包含獨立虛擬機器、可用性設定組或虛擬機器擴展集。  您可以混合搭配後端集區中的資源，且集區中最多可包含 150 項這些資源的任意組合。

在考量如何設計您的後端集區時，您可以將最少量的個別後端集區資源作為設計原則，以進一步最佳化執行管理作業所需的時間。  在資料平面的效能或規模方面沒有任何差異。

## <a name="az"></a>可用性區域

>[!NOTE]
> 若要搭配使用[可用性區域預覽版](https://aka.ms/availabilityzones)與標準 Load Balancer，必須要[註冊可用性區域](https://aka.ms/availabilityzones)。

標準 Load Balancer 在可使用「可用性區域」的區域中可支援較多功能。  這些功能會累加於所有標準 Standard Load Balancer 所提供的功能之上。  公用和內部標準 Load Balancer 皆可進行可用性區域設定。

區域性前端在部署於具有可用性區域的區域時，會依預設成為區域備援前端。   區域備援前端不受區域失敗的影響，且所有區域中的專用基礎結構會同時為其提供服務。 

此外，您可以確保前端可用於特定區域。 區域性前端與個別的區域會有共同的結果，且只有單一區域中的專用基礎結構會為其提供服務。

跨區域負載平衡適用於後端集區，Vnet 中的任何虛擬機器資源都可以是後端集區的一部分。

請檢閱[可用性區域相關功能的詳細討論](load-balancer-standard-availability-zones.md)。

### <a name="diagnostics"></a> 診斷

標準 Load Balancer 可透過 Azure 監視器提供多維度計量。  這些計量可進行篩選和分組，並且可讓您深入檢視您的服務目前和過去的效能和健康情況。  此外也支援資源健康情況。  以下將簡單說明支援的診斷：

| 計量 | 說明 |
| --- | --- |
| VIP 可用性 | Load Balancer Standard 會持續運用從區域內到 Load Balancer 前端再一直到支援您 VM 之 SDN 堆疊的資料路徑。 只要狀況良好的執行個體持續存在，測量就會依循與您應用程式的負載平衡流量相同的路徑。 此外，也會驗證您客戶所使用的資料路徑。 此測量對您的應用程式來說是看不見的，也不會干擾到其他作業。|
| DIP 可用性 | Load Balancer Standard 使用分散式健康情況探查服務，可根據您的組態設定監視應用程式端點的健康情況。 這個計量會提供 Load Balancer 集區中每個個別執行個體端點的彙總檢視，或各端點篩選過的檢視。  您可以看到 Load Balancer 藉由健康情況探查設定如何檢視應用程式的健康情況。
| SYN 封包 | Load Balancer Standard 不會終止 TCP 連線，或是與 TCP 或 UDP 封包流程互動。 流程及其交握一律是在來源與 VM 執行個體之間進行。 若要針對您的 TCP 通訊協定案例進行進一步疑難排解，您可使用 SYN 封包計數器來了解已進行多少次 TCP 連線嘗試。 此計量會回報已收到的 TCP SYN 封包數。|
| SNAT 連線 | Load Balancer Standard 會回報偽裝為「公用 IP」位址前端的連出流程數目。 SNAT 連接埠是可耗盡的資源。 此計量可以指出應用程式有多依賴 SNAT 來處理連出的起始流程。  系統會回報成功和失敗之連出 SNAT 流程的計數器，而且可用來對連出流程的健康情況進行疑難排解及了解。|
| 位元組計數器 | Load Balancer Standard 會回報每個前端處理的資料。|
| 封包計數器 | Load Balancer Standard 會回報每個前端處理的封包。|

請檢閱[標準 Load Balancer 診斷的詳細討論](load-balancer-standard-diagnostics.md)。

### <a name="haports"></a>HA 連接埠

標準 Load Balancer 支援新類型的規則。  

您可以設定負載平衡規則，讓應用程式延展並變得高度可靠。 當您使用 HA 連接埠負載平衡規則時，標準 Load Balancer 將會在內部標準 Load Balancer 之前端 IP 位址的每個暫時連接埠上提供以流量為準的負載平衡。  對於那些指定個別連接埠並不實際或不符合需求的其他案例來說，此功能相當實用。

HA 連接埠負載平衡規則可讓您為虛擬網路設備和任何需要大範圍輸入連接埠的應用程式，建立主動-被動或主動-主動 n + 1 的案例。  健康情況探查可用來判斷哪個後端應接收新的流量。  您可以使用網路安全性群組來模擬連接埠範圍案例。

>[!IMPORTANT]
> 如果您打算使用網路虛擬設備，請要求廠商提供相關指引以了解是否其產品是否經過 HA 連接埠的測試，並遵循其指引進行實作。 

請檢閱 [HA 連接埠的詳細討論](load-balancer-ha-ports-overview.md)。

### <a name="securebydefault"></a>預設保護

標準 Load Balancer 完全架構於虛擬網路上。  虛擬網路是封閉的私人網路。  由於標準 Load Balancer 和標準公用 IP 位址設計為允許從虛擬網路外部存取此虛擬網路，因此這些資源目前預設為關閉，除非您加以開啟。 這表示目前會使用網路安全性群組 (NSG) 來明確許可和放行允許的流量。  您可以在建立整個虛擬資料中心之後，透過 NSG 決定該中心可供使用的項目和時間。  如果您沒有子網路的 NSG 或虛擬機器資源的 NIC，我們將不會允許存取此資源的流量。

若要深入了解 NSG 及如何將其套用至您的案例，請參閱[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

### <a name="outbound"></a> 輸出連線

Load Balancer 支援輸入和輸出案例。  標準 Load Balancer 在輸出連線方面與基本 Load Balancer 有很大的不同。

系統會使用來源網路位址轉譯 (SNAT)，將您虛擬網路上的內部、私人 IP 位址對應至 Load Balancer 前端的公用 IP 位址。

標準 Load Balancer 基於[更為穩健、可擴充且可預期的 SNAT 演算法](load-balancer-outbound-connections.md#snat)的目標導入了新的演算法，並且啟用了新功能、去除模糊性，以及強制執行不產生負面影響的明確設定。 必須要有這些變更，才會有新的功能。 

使用標準 Load Balancer 時須注意的重要原則如下：

- Load Balancer 資源的運用端賴於完整的規則。  Azure 的所有程式設計皆衍生自該規則的設定。
- 有多個可用的前端時，將會使用所有前端，而每個前端分別會使可用的 SNAT 連接埠數目隨之增加
- 您可以選擇及控制是否不要讓特定前端用於輸出連線。
- 輸出案例是明確的，且必須經由指定才會有輸出連線存在。
- 負載平衡規則會推斷 SNAT 的程式設計方式。 負載平衡規則會隨通訊協定而不同。 SNAT 會隨通訊協定而不同，且設定應反映這一點，而非產生負面影響。

#### <a name="multiple-frontends"></a>多個前端
如果您因為預期會有或已面臨大量輸出連線需求而需要更多 SNAT 連接埠，您也可以藉由對相同的虛擬機器資源設定額外的前端、規則和後端集區，來加入增量的 SNAT 連接埠數量。

#### <a name="control-which-frontend-is-used-for-outbound"></a>控制用於輸出的前端
如果您想要將輸出連線限定為僅來自特定的前端 IP 位址，您可以選擇性地在表示輸出對應的規則中停用輸出 SNAT。

#### <a name="control-outbound-connectivity"></a>控制輸出連線
標準 Load Balancer 存在於虛擬網路的內容中。  虛擬網路是隔離的私人網路。  若未與公用 IP 位址有所關聯，就不會允許公用連線。  您可以存取 [VNet 服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)，因為它們位於您虛擬網路內部的本機位置。  如果您想要對您虛擬網路外部的目的地建立輸出連線，您有兩個選項：
- 將標準 SKU 公用 IP 位址作為執行個體層級的公用 IP 位址，指派給虛擬機器資源，或
- 將虛擬機器資源放在公用標準 Load Balancer 的後端集區中。

這兩種方式都允許從虛擬網路到虛擬網路外部的輸出連線。 

如果您_只有_內部標準 Load Balancer 與您虛擬機器資源所在的後端集區相關聯，則您的虛擬機器將只能存取虛擬網路資源和 [VNet 服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。  您可以依照上一段說明的步驟來建立輸出連線。

與標準 SKU 無關聯之虛擬機器資源的輸出連線會維持原狀。

請檢閱[輸出連線的詳細討論](load-balancer-outbound-connections.md)。

### <a name="multife"></a>多個前端
Load Balancer 支援為多個前端使用多項規則。  標準 Load Balancer 可將此支援擴及至輸出案例。  輸出案例基本上就是與輸入負載平衡規則相反的情形。  輸入負載平衡規則也會建立輸出連線的關聯性。 標準 Load Balancer 會透過負載平衡規則使用所有與虛擬機器資源相關聯的前端。  此外，您可以透過負載平衡規則的參數來抑制輸出連線用途的負載平衡規則，而使特定前端的可用選項變成空的。

相對地，基本 Load Balancer 會隨機選取單一前端，且無法控制能夠選取哪一個。

請檢閱[輸出連線的詳細討論](load-balancer-outbound-connections.md)。

### <a name="operations"></a>管理作業

標準 Load Balancer 資源存在於全新的基礎結構平台上。  這可大幅提升標準 SKU 的管理作業執行速度，且每項標準 SKU 資源的管理通常都可在 30 秒內完成。  請注意，當後端集區的大小增加，變更後端集區的所需時間也會隨之增加。

您修改標準 Load Balancer 資源，以及在不同虛擬機器之間移動標準公用 IP 位址的速度，都可快上許多。

## <a name="migration-between-skus"></a>在 SKU 之間移轉

SKU 是不可變動的。 請依照本節中的步驟從一個資源 SKU 移到另一個 SKU。

### <a name="migrate-from-basic-to-standard-sku"></a>從 Basic 移轉到 Standard SKU

1. 視需要針對 Load Balancer 和「公用 IP」建立一個新的 Standard 資源。 重新建立您的規則和探查定義。

2. 在 NIC 或子網路上建立新的或更新現有的 NSG，以將已負載平衡的流量、探查及任何其他您想要允許的流量列入白名單。

3. 從所有 VM 執行個體中移除 Basic SKU 資源 (看 Load Balancer 和「公用 IP」哪一個適用)。 請務必也移除可用性設定組的所有 VM 執行個體。

4. 將所有 VM 執行個體附加至新的 Standard SKU 資源。

### <a name="migrate-from-standard-to-basic-sku"></a>從 Standard 移轉到 Basic SKU

1. 視需要針對 Load Balancer 和「公用 IP」建立一個新的 Basic 資源。 重新建立您的規則和探查定義。 

2. 從所有 VM 執行個體中移除 Standard SKU 資源 (看 Load Balancer 和「公用 IP」哪一個適用)。 請務必也移除可用性設定組的所有 VM 執行個體。

3. 將所有 VM 執行個體附加至新的 Basic SKU 資源。

>[!IMPORTANT]
>
>在 Basic 和 Standard SKU 的使用上有一些限制。
>
>只有在 Standard SKU 中才有提供「HA 連接埠」和 Standard SKU 的「診斷」。 您無法既從 Standard SKU 移轉到 Basic SKU 又同時保留這些功能。
>
>如本文所述，基本和標準 SKU 之間有許多差異。  請確實加以了解，並做好相關準備。
>
>Load Balancer 和 Public IP 資源必須使用相符的 SKU。 您無法將 Basic SKU 資源與 Standard SKU 資源混用。 您無法將獨立虛擬機器、可用性設定組資源中的虛擬機器或虛擬機器擴展集資源同時連結到這兩個 SKU。

## <a name="region-availability"></a>區域可用性

Load Balancer Standard 目前已在所有公用雲端地區推出。

## <a name="sla"></a>SLA

標準 Load Balancer 提供 99.99% SLA。  如需詳細資訊，請檢閱[標準 Load Balancer SLA](https://aka.ms/lbsla)。

## <a name="pricing"></a>價格

標準 Load Balancer 為計費產品，根據所設定的負載平衡規則數目以及處理的所有輸入和輸出資料計算費用。 如需標準 Load Balancer 的定價資訊，請瀏覽 [Load Balancer 定價](https://aka.ms/lbpricing)頁面。

## <a name="limitations"></a>限制

- Load Balancer 後端執行個體目前不能位於對等互連的虛擬網路中。 所有後端執行個體都必須在相同的區域中。
- SKU 是不可變動的。 您無法變更現有資源的 SKU。
- 獨立虛擬機器資源、可用性設定組資源或虛擬機器擴展集資源都只能參考一個 SKU，絕不會同時參考兩者。
- 目前不支援 [Azure 監視器警示](../monitoring-and-diagnostics/monitoring-overview-alerts.md)。
- 標準 SKU LB 與 PIP 資源不支援[移動訂用帳戶作業](../azure-resource-manager/resource-group-move-resources.md)。

## <a name="next-steps"></a>後續步驟

- 了解[標準 Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)
- 深入了解[可用性區域](../availability-zones/az-overview.md)。
- 了解[標準 Load Balancer 診斷](load-balancer-standard-diagnostics.md)。
- 參閱[支援的多維度計量](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers)，以了解 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview.md)中的診斷。
- 了解如何使用[輸出連線的 Load Balancer](load-balancer-outbound-connections.md)
- 了解[具有 HA 連接埠負載平衡規則的標準 Load Balancer](load-balancer-ha-ports-overview.md)
- 了解如何使用[多個前端的 Load Balancer](load-balancer-multivip-overview.md)
- 了解[虛擬網路](../virtual-network/virtual-networks-overview.md)。
- 深入了解[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。
- 了解 [VNet 服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)
- 了解 Azure 中的一些其他重要[網路功能](../networking/networking-overview.md)。
- 深入了解 [Load Balancer](load-balancer-overview.md)。
