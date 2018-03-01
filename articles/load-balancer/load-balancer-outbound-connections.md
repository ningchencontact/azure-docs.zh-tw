---
title: "了解 Azure 中的輸出連線 | Microsoft Docs"
description: "本文說明如何讓 Azure VM 與公用網際網路服務進行通訊。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: e1a2b4c281194ec4c70e4d9fe1bc97c5aa61436e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>了解 Azure 中的輸出連線

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]


Azure 透過數個不同的機制，提供客戶部署的輸出連線。  本文說明案例為何、套用時機、運作方式，以及如何管理它們。

Azure 中的部署可在公用 IP 位址空間中與 Azure 外部的端點進行通訊。 當執行個體啟動輸出流程至公用 IP 位址空間中的目的地時，Azure 會動態地將私人 IP 位址對應到公用 IP 位址。  一旦建立此對應，此輸出起源流程的傳回流量也可以觸達流程來源的私人 IP 位址。

Azure 會使用來源網路位址轉譯 (SNAT) 執行這項功能。  當多個私人 IP 位址在單一公用 IP 位址後面偽裝時，Azure 會使用[連接埠位址轉譯 (PAT)](#pat) 來偽裝私人 IP 位址。  暫時連接埠會用於 PAT，並且根據集區大小[預先配置](#preallocatedports)。

有多個[輸出案例](#scenarios)。 這些案例可以視需要合併。 仔細檢閱它們以了解當它們套用到您的部署模型和應用程式案例時的功能、限制和模式。  檢閱[管理這些案例](#snatexhaust)的指引。

## <a name="scenarios"></a>案例概觀

Azure 有兩個主要部署模型：Azure Resource Manager 和傳統。 使用 [Azure Resource Manager 資源](#arm)時會明確地定義負載平衡器和相關資源。  傳統部署會讓負載平衡器的概念變得抽象，並且透過[雲端服務](#classic)的端點定義表達類似功能。 您的部署適用的[案例](#scenarios)相依於使用的部署模型。

### <a name="arm"></a>Azure Resource Manager

Azure 目前提供三個不同的方法，來達成 Azure Resource Manager 資源的輸出連線。  [傳統](#classic)部署有這些案例的子集。

| 案例 | 方法 | 說明 |
| --- | --- | --- |
| [1.具有執行個體層級公用 IP 位址的 VM (無論是否有負載平衡器)](#ilpip) | SNAT，未使用連接埠偽裝 |Azure 會使用指派給執行個體 NIC 之 IP 設定的公用 IP。  執行個體有所有可用的暫時連接埠。 |
| [2.與 VM 相關聯的公用負載平衡器 (執行個體上沒有執行個體層級公用 IP 位址)](#lb) | SNAT 與使用負載平衡器前端的連接埠偽裝 (PAT) |Azure 會與多個私人 IP 位址共用公用負載平衡器前端的公用 IP 位址，並且使用前端到 PAT 的暫時連接埠。 |
| [3.獨立 VM (無負載平衡器，無執行個體層級公用 IP 位址)](#defaultsnat) | SNAT 與連接埠偽裝 (PAT) | Azure 會自動指定 SNAT 的公用 IP 位址，與可用性設定組的多個私人 IP 位址共用此公用 IP 位址，以及使用此公用 IP 位址的暫時連接埠。  此案例是先前案例 1 和 2 的後援，如果您需要可見度和控制，並不建議此案例。 |

如果您不想要 VM 與公用 IP 位址空間中的 Azure 外部端點進行通訊，您可以使用網路安全性群組 (NSG) 來視需要封鎖存取。  [防止輸出連線](#preventoutbound)中會更詳細地討論使用 NSG。  如何設計和管理沒有任何輸出存取之虛擬網路的設計指南和實作詳細資料已超出本文的範圍。

### <a name="classic"></a>傳統 (雲端服務)

適用於傳統部署的案例是適用於 [Azure Resource Manager](#arm) 部署與負載平衡器基本的案例子集。

傳統虛擬機器具有如同針對 Azure Resource Manager 資源所述的相同三種基本案例 ([1](#ilpip)、[2](#lb)、[3](#defaultsnat))。 傳統 Web 背景工作角色只能有兩個案例 ([2](#lb)、[3](#defaultsnat))。  [風險降低策略](#snatexhaust)也有相同的差異。

用於傳統部署之 PAT 的[預先配置暫時連接埠](#ephemeralprots)的演算法，與用於 Azure Resource Manager 資源部署的演算法相同。  

### <a name="ilpip"></a>案例 1：VM 與執行個體層級公用 IP 位址

在此案例中，VM 具有指派給它的執行個體層級公用 IP (ILPIP)。 就輸出連線而言，VM 是否負載平衡並不重要。  此案例的優先順序高於其他案例。 使用 ILPIP 時，VM 會針對所有輸出流程使用 ILPIP。  

未使用連接埠偽裝 (PAT)，且 VM 具有所有可用的暫時連接埠。

如果您的應用程式起始許多輸出流程而發生 SNAT 連接埠耗盡的情況，您可以考慮指派 [ILPIP 來緩和 SNAT 限制的問題](#assignilpip)。 檢閱[管理 SNAT 耗盡](#snatexhaust)。

### <a name="lb"></a>案例 2：無執行個體層級公用 IP 位址的負載平衡 VM

在此案例中，VM 是公用 Load Balancer 後端集區的一部分。  VM 沒有指派給它的公用 IP 位址。 必須使用負載平衡器規則來建立公用 IP 前端與後端集區之間的連結，從而設定 Load Balancer 資源。 如果您未完成此規則設定，行為就如[不含執行個體層級公用 IP 的獨立 VM](#defaultsnat) 案例中所述。  規則不需要後端集區中的運作中接聽程式或健康情況探查成功。

當負載平衡的 VM 建立輸出流程時，Azure 會將輸出流量的私用來源 IP 位址轉譯為公用負載平衡器前端的公用 IP 位址。 Azure 會使用來源網路位址轉譯 (SNAT) 執行這項功能，以及使用[連接埠位址轉譯 (PAT)](#pat)在公用 IP 位址背後偽裝多個私人 IP 位址。 Load Balancer 公用 IP 位址前端的暫時連接埠用來區分源自 VM 的個別流程。 建立輸出流程時，SNAT 會動態使用[預先配置暫時連接埠](#preallocatedports)。 在此情況下，用於 SNAT 的暫時連接埠稱為 SNAT 連接埠。

SNAT 連接埠會預先配置，如[了解 SNAT 和 PAT](#snat) 一節中所述，並且是可以耗盡的有限資源。 請務必了解[取用](#pat)的方式。 檢閱[管理 SNAT 耗盡](#snatexhaust)來了解如何設計及視需要降低風險。

當[多個 (公用) IP 位址與 Load Balancer Basic 相關聯](load-balancer-multivip-overview.md)，這些公用 IP 位址中的任一位址都可以是[輸出流程的候選項並且選取其中一個](#multivipsnat)。  

您可以使用 [Log Analytics for Load Balancer](load-balancer-monitor-log.md) 和[要監視之 SNAT 連接埠耗盡訊息的警示事件記錄檔](load-balancer-monitor-log.md#alert-event-log)，來使用 Load Balancer Basic 監視輸出連線的健康情況。

### <a name="defaultsnat"></a>案例 3：無執行個體層級公用 IP 位址的獨立 VM

VM 不是 Azure Load Balancer 集區的一部分，而且沒有指派給它的執行個體層級公用 IP (ILPIP) 位址。 當 VM 建立輸出流程時，Azure 會將輸出流量的公用來源 IP 位址轉譯為私用來源 IP 位址。 此輸出流量所用的公用 IP 位址無法進行設定，而且不利於訂用帳戶的公用 IP 資源限制。 Azure 會使用來源網路位址轉譯 (SNAT) 與位址偽裝 ([PAT](#pat)) 執行這項功能。 這個案例類似於[案例 2](#lb)，差別在於沒有所使用 IP 位址的控制權。  這是當案例 1 和案例 2 不存在時的後援案例。  如果想要輸出位址的控制權，則不建議此案例。

SNAT 連接埠會預先配置，如[了解 SNAT 和 PAT](#snat) 一節中所述，並且是可以耗盡的有限資源。 請務必了解[取用](#pat)的方式。 檢閱[管理 SNAT 耗盡](#snatexhaust)來了解如何設計及視需要降低風險。

### <a name="combinations"></a>多個合併案例

先前章節所述的案例可以合併以達到特定結果。  有多個案例時，會套用以下優先順序：[案例 1](#ilpip) 優先順序高於[案例 2](#lb) 和 [3](#defaultsnat) (僅限 Azure Resource Manager)，而[案例 2](#lb) 會覆寫[案例 3](#defaultsnat) (Azure Resource Manager 和傳統)。

範例是 Azure Resource Manager 部署，其中應用程式高度依賴與有限數目目的地的輸出連線，也會透過 Load Balancer 前端接收輸入流程。 在此情況下，您可以合併案例 1 和 2 來緩解。  檢閱[管理 SNAT 耗盡](#snatexhaust)以取得其他模式。

### <a name="multivipsnat"></a>輸出流程的多個前端

Load Balancer Basic 會選擇單一前端，在[多個 (公用) IP 前端](load-balancer-multivip-overview.md)是輸出流程的候選項目時使用輸出流程。  無法設定此選項，選取演算法應該視為隨機。  您可以為輸出指定特定 IP 位址，如[合併案例](#combinations)中所述。

## <a name="snat"></a>了解 SNAT 和 PAT

### <a name="pat"></a>連接埠偽裝 SNAT (PAT)

當公用 Load Balancer 資源與 VM 執行個體建立關聯時，系統會改寫每個連出連線來源。 來源會從虛擬網路私人 IP 位址空間改寫成負載平衡器的前端「公用 IP」位址。 在公用 IP 位址空間中，5 tuple 流程 (來源 IP 位址、來源連接埠、IP 傳輸通訊協定、目的地 IP 位址、目的地連接埠) 必須是唯一的。  暫時連接埠是用來在重寫私人來源 IP 位址之後達到這個目的，因為多個流程來自單一公用 IP 位址。  這些暫時連接埠稱為 SNAT 連接埠。 

每個流程都會取用一個 SNAT 連接埠至單一目的地 IP 位址、連接埠和通訊協定。 針對相同目的地 IP 位址、連接埠和通訊協定的多個流程，每個流程都會取用單一 SNAT 連接埠。 這可確保源自相同公用 IP 位址至相同目的地 IP 位址、連接埠和通訊協定時，流程是唯一的。 

多個各自前往不同目的地 IP 位址、連接埠和通訊協定的流程會共用單一 SNAT 連接埠。 目的地 IP 位址、連接埠和通訊協定讓流程成為唯一的，而不需要使用其他來源連接埠來區別公用 IP 位址空間中的流程。

當 SNAT 連接埠資源耗盡時，輸出流程失敗，直到現有流程釋放 SNAT 連接埠為止。 當流程關閉並使用 [4 分鐘閒置逾時](#idletimeout)來從閒置流程回收 SNAT 連接埠時，Load Balancer 會回收 SNAT 連接埠。

檢閱[管理 SNAT](#snatexhaust) 一節以取得模式，針對通常會導致 SNAT 連接埠耗盡的情況降低風險。

### <a name="preallocatedports"></a>連接埠偽裝 SNAT (PAT) 的暫時連接埠預先配置

Azure 會使用演算法在使用連接埠偽裝 SNAT ([PAT](#pat)) 時，根據後端集區的大小決定可用的預先配置 SNAT 連接埠數目。  SNAT 連接埠是暫時連接埠，可用於指定公用 IP 來源位址。

Azure 會將 SNAT 連接埠預先配置到每個 VM 之 NIC 的 IP 設定。 當 IP 設定新增至集區時，會根據後端集區大小針對此 IP 設定預先配置 SNAT 連接埠。 針對傳統 Web 背景工作角色，會對每個角色執行個體進行配置。  建立輸出流程時，[PAT](#pat) 會動態取用 (最多達預先配置的限制)，並且在流程關閉或[閒置逾時](#ideltimeout)時釋放這些連接埠。

下表說明後端集區大小層級的 SNAT 連接埠預先配置：

| 集區大小 (VM 執行個體) | 對每個 IP 設定預先配置 SNAT 連接埠|
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

請務必記住，可用的 SNAT 連接埠號碼不會直接轉譯為連線數目。 單一 SNAT 連接埠可以重複使用於多個唯一目的地。 如果需要讓流程成為唯一的，才會取用連接埠。 請參閱[如何管理這個可耗盡資源](#snatexhaust)以及描述 [PAT](#pat) 的章節，以取得設計和降低風險指引。

變更您的後端集區大小可能會影響您建立的部分流程。 如果後端集區大小增加並轉換到下一層，在轉換至下一個較大的後端集區層期間，會回收您預先配置的一半 SNAT 連接埠。 與已回收 SNAT 連接埠關聯的流程會逾時，必須重新建立。 只要預先配置的連接埠可用，新的連線嘗試就會立即成功。

如果後端集區大小縮減而轉換成較低的層級，可用的 SNAT 連接埠數目就會增加。 在此情況下，現有已配置 SNAT 連接埠及其各自流程都不會受到影響。

## <a name="snatexhaust"></a>管理 SNAT (PAT) 連接埠耗盡

如[無執行個體層級公用 IP 位址的獨立 VM](#defaultsnat) 和[無執行個體層級公用 IP 位址的負載平衡 VM](#lb) 中所述，用於 [PAT](#pat) 的[暫時連接埠](#preallocatedports)是可耗盡的資源。

如果您知道將會對相同的目的地 IP 位址和連接埠起始許多連出 TCP 或 UDP 連線，並且觀察到失敗的連出連線，或是支援人員告知您 SNAT 連接埠 ([PAT](#pat) 使用的預先配置[暫時連接埠](#preallocatedports)) 將耗盡，您有數個可緩和這些問題的一般選項。  請檢閱這些選項並判斷哪一個可用且最適合您的案例。  可能會有一或多個選項有助於管理此案例。

如果您對於了解輸出連線行為有問題，您可以使用 IP 堆疊統計資料 (netstat)，或者使用封包擷取觀察連線行為很有幫助。  您可以在您執行個體的客體 OS 中執行這些封包擷取，或使用[網路監看員來進行封包擷取](../network-watcher/network-watcher-packet-capture-manage-portal.md)。

### <a name="connectionreuse"></a>將應用程式修改成重複使用連線 
您可以在應用程式中重複使用連線，以降低對用於 SNAT 之暫時連接埠的需求。  對於 HTTP/1.1 之類的通訊協定尤其如此，因為連線重複使用是預設值。  而使用 HTTP 做為其傳輸方式的其他通訊協定也會因而受益。  重複使用一律比每個要求的獨立且不可部分完成的 TCP 連線來得好，而且可帶來效能更好又非常有效率的 TCP 傳輸。

### <a name="connection pooling"></a>將應用程式修改成使用連線共用
您可以在應用程式中採用連線集區配置，這樣要求會在內部分布到一固定的連線集合 (每個都盡可能地重複使用)。  這麼做會限制使用中的暫時連接埠的數目，並建立較可預測的環境。  當作業之回應的單一連線被封鎖時，這麼做可支援多個同時進行的作業，進而增加要求的輸送量。  連線集區可能已存在於您用來開發應用程式的架構中，或是您應用程式的組態設定中。  您可以結合此作法與連線重複使用，這樣多個要求就會取用固定且可預測的數個對應相同目的地 IP 位址和連接埠的連接埠，同時也受益於非常有效率地使用 TCP 交易，而減少延遲和資源使用率。  UDP 交易也有幫助，因為管理 UDP 流程數目可以輪流避免耗盡狀況並管理 SNAT 連接埠使用量。

### <a name="retry logic"></a>將應用程式修改成使用較不積極的重試邏輯
當用於 [PAT](#pat) 的[預先配置暫時連接埠](#preallocatedports)耗盡或發生應用程式失敗時，不含衰減和降速邏輯的積極或暴力重試會造成耗盡的情況發生或持續存在。 您可以使用較不積極的重試邏輯，以降低對暫時連接埠的需求。   暫時連接埠有 4 分鐘的閒置逾時 (無法調整)，如果重試太過積極，耗盡情況就沒有機會進行自我清理。  因此，考慮應用程式重試交易的方法和頻率是設計的重要考量。

### <a name="assignilpip"></a>指派執行個體層級公用 IP 給每個 VM
這會將您的案例變更為 [VM 的執行個體層級公用 IP](#ilpip)。  用於每個 VM 的所有公用 IP 暫時連接埠都可供 VM 使用 (與公用 IP 暫時連接埠會與個別後端集區之所有相關 VM 共用的案例相反)。  要考慮的取捨點如公用 IP 位址的額外成本，以及將大量的個別 IP 位址加入白名單的潛在影響。

>[!NOTE] 
>此選項不適用於 Web 背景工作角色。

### <a name="idletimeout"></a>使用 keepalives 重設輸出閒置逾時

連出連線有 4 分鐘的閒置逾時。 此值無法調整。 不過，您可以使用傳輸 (也就是 TCP keep-alive) 或應用程式層 keepalives，視需要重新整理閒置流程，然後重設此閒置逾時。

## <a name="discoveroutbound"></a>探索指定 VM 使用的公用 IP
有許多方式可判斷輸出連線的公用來源 IP 位址。 OpenDNS 提供可顯示您 VM 公用 IP 位址的服務。 使用 nslookup 命令，您可以將名稱 myip.opendns.com 的 DNS 查詢傳送至 OpenDNS 解析程式。 服務會傳回用來傳送查詢的來源 IP 位址。 當您從 VM 執行下列查詢時，回應會是用於該 VM 的公用 IP。

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>防止輸出連線
有時您並不想允許 VM 建立輸出流程，或可能需要管理可使用輸出流程來連線到哪些目的地，或哪些目的地可以發起輸入流程。 在此情況下，您需使用[網路安全性群組 (NSG)](../virtual-network/virtual-networks-nsg.md) 來管理 VM 可連線的目的地，以及哪些公用目的地可以起始輸入流程。 當您將 NSG 套用到負載平衡的 VM 時，需要注意[預設標籤](../virtual-network/virtual-networks-nsg.md#default-tags)和[預設規則](../virtual-network/virtual-networks-nsg.md#default-rules)。

您必須確定 VM 可以從 Azure Load Balancer 接收健康情況探查要求。 如果 NSG 封鎖來自 AZURE_LOADBALANCER 預設標籤的健全狀況探查要求，您的 VM 健全狀況探查會失敗，且會將 VM 標示為離線。 負載平衡器會停止將新的流程傳送到該 VM。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Load Balancer Basic](load-balancer-overview.md)。
- 深入了解[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。
- 了解 Azure 中的一些其他重要[網路功能](../networking/networking-overview.md)。
