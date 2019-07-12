---
title: 診斷和使用 Azure Cosmos DB.NET SDK 時，針對問題進行疑難排解
description: 使用功能，例如用戶端記錄和其他第三方工具來識別、 診斷及使用.NET SDK 時，針對 Azure Cosmos DB 問題進行疑難排解。
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9511562b81f7ac0c1582897d703f4c5ccb89716
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806390"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>診斷和使用 Azure Cosmos DB.NET SDK 時，針對問題進行疑難排解
本文章涵蓋常見的問題、 因應措施、 診斷步驟和工具使用時[.NET SDK](sql-api-sdk-dotnet.md)使用 Azure Cosmos DB SQL API 帳戶。
.NET SDK 提供來存取 Azure Cosmos DB SQL API 的用戶端邏輯表示法。 此文章所說明的工具和方法，可以在您遇到任何問題時提供協助。

## <a name="checklist-for-troubleshooting-issues"></a>移難排解問題的檢查清單：
在您移動您的應用程式至生產環境之前，請考慮下列檢查清單。 使用檢查清單，將會導致數個常見的問題，您可能會看到。 您也可以快速地診斷發生問題時：

*   使用最新版[SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)。 預覽 Sdk 不應該用於生產環境。 這樣可避免達到已修正的已知的問題。
*   檢閱[效能祕訣](performance-tips.md)並遵循建議的做法。 這將有助於避免調整規模、 延遲及其他效能問題。
*   啟用 SDK 記錄可協助您疑難排解問題。 啟用記錄功能可能會影響效能，因此建議您最好針對問題進行疑難排解時才加以啟用。 您可以啟用下列記錄檔：
    *   [記錄度量](monitor-accounts.md)使用 Azure 入口網站。 入口網站計量顯示 Azure Cosmos DB 的遙測，很有幫助判斷是否對應至 Azure Cosmos DB 的問題，或者如果它是從用戶端。
    *   記錄檔[診斷字串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)從點作業的回應。
    *   記錄檔[SQL 查詢計量](sql-api-query-metrics.md)從所有查詢回應 
    *   請依照安裝程式以進行[SDK 記錄]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

查看此文章的[常見問題和因應措施](#common-issues-workarounds)一節。

請檢查[GitHub 問題區段](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)，主動監視。 查看您是否有任何含有已提出因應措施的類似問題。 如果您找不到所需的方案，然後提出 GitHub 問題。 您可以開啟支援刻度緊急問題。


## <a name="common-issues-workarounds"></a>常見問題和因應措施

### <a name="general-suggestions"></a>一般建議
* 您的 Azure Cosmos DB 帳戶，請盡可能相同 Azure 區域中執行您的應用程式。 
* 您可能會遇到連線/可用性問題，因為缺少用戶端電腦上的資源。 我們建議您監視您執行 Azure Cosmos DB 用戶端的節點上的 CPU 使用量並相應增加/相應放大如果它們在高負載執行。

### <a name="check-the-portal-metrics"></a>檢查入口網站計量
檢查[入口網站計量](monitor-accounts.md)將協助您判斷它是否用戶端發生問題，或是否有與服務的問題。 比方說是否計量資訊包含高比率的速率限制的要求 （HTTP 狀態碼 429） 的表示正在進行節流的要求然後檢查[要求速率太大]一節。 

### <a name="request-timeouts"></a>要求逾時
RequestTimeout 通常是因為使用 Direct/TCP，但在閘道模式中，可能會發生。 這些是常見的已知的原因，以及如何修正此問題的建議。

* 這會造成延遲和/或要求逾時，CPU 使用率很高。 客戶可以相應增加的主機電腦，以提供更多資源，或在負載可以分散到多部機器。
* 通訊端連接埠可用性可能會低 /。 當使用.NET Sdk 2.0 版之前發行，在 Azure 中執行的用戶端，可能會發生[Azure SNAT (PAT) 連接埠耗盡]。 此範例的原因建議您一律執行最新的 SDK 版本。
* 建立多個 DocumentClient 執行個體可能會導致連接爭用和逾時問題。 請遵循[效能祕訣](performance-tips.md)，並跨整個處理序使用單一 DocumentClient 執行個體。
* 因為其集合已佈建不足、 後端節流處理要求，而用戶端可以不含此呈現給呼叫者在內部重試使用者有時會看到較高的延遲或要求逾時。 請檢查[入口網站計量](monitor-accounts.md)。
* Azure Cosmos DB 會將整體佈建的輸送量平均分散到實體分割區。 檢查入口網站的計量，以查看工作負載是否發生 hot[分割區索引鍵](partition-data.md)。 這會彙總已使用的輸送量 （RU/秒） 佈建的 Ru，底下會出現，但使用的單一分割區的輸送量 （RU/秒） 將會超過佈建的輸送量。 
* 此外，2.0 SDK 會將通道語意將 direct/TCP 連線。 一個 TCP 連線用於多個要求中，在相同的時間。 這可能會導致在特定情況下的兩個問題：
    * 高程度並行存取可能會導致競爭的情況，在通道上。
    * 大型的要求或回應可以封鎖在通道上的標頭的線條會導致，而惡化爭用，即使是使用相對較低的並行程度。
    * 如果案例落在任何兩個分類 （或如果懷疑 CPU 使用率過高），這些是可能的緩和措施：
        * 請嘗試相應增加/相應放大應用程式。
        * 此外，SDK 記錄檔，您可以透過擷取[追蹤接聽項](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)以取得詳細資料。

### <a name="connection-throttling"></a>連線節流
連線節流可能是因為連線限制在主機電腦上。 上一步 2.0 中，在 Azure 中執行的用戶端，可能會發生[Azure SNAT (PAT) 連接埠耗盡]。

### <a name="snat"></a>Azure SNAT (PAT) 連接埠耗盡

如果您的應用程式部署於不具公用 IP 位址的 Azure 虛擬機器上，則 [Azure SNAT 連接埠](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)預設會建立與您 VM 外部任何端點的連線。 從 VM 到 Azure Cosmos DB 端點所允許的連線數目會受到 [Azure SNAT 設定](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)所限制。

 只有在您的 VM 具有私人 IP 位址，而且來自 VM 的程序嘗試連線到公用 IP 位址時，才會使用 Azure SNAT 連接埠。 有兩種因應措施可避免 Azure SNAT 限制：

* 將 Azure Cosmos DB 服務端點新增至您的 Azure 虛擬機器虛擬網路。 如需詳細資訊，請參閱 [Azure 虛擬網路服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)。 

    啟用服務端點時，要求不再會從公用 IP 傳送到 Azure Cosmos DB。 改為傳送虛擬網路和子網路身分識別。 如果只允許公用 IP，此變更可能會導致防火牆卸除。 如果您使用防火牆，當您啟用服務端點時，請使用[虛擬網路 ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) 將子網路新增至防火牆。
* 將公用 IP 指派給您的 Azure VM。

### <a name="http-proxy"></a>HTTP Proxy
如果您使用 HTTP Proxy，請確定它可以支援在 SDK `ConnectionPolicy` 中設定的連線數目。
否則就會遇到連線問題。

### 要求速率太大<a name="request-rate-too-large"></a>
「 要求速率太大 」 或錯誤碼 429 表示 ，您的要求會受到節流處理，，因為已使用的輸送量 （RU/秒） 超過佈建的輸送量。 SDK 會自動重試根據指定的要求[重試原則](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)。 如果經常發生此失敗，請考慮增加集合的輸送量。 請檢查[入口網站的計量](use-metrics.md)以查看 是否您收到 429 錯誤。 檢閱您[分割區索引鍵](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)以確保它會導致儲存體和要求的磁碟區的平均分配。 

### <a name="slow-query-performance"></a>查詢效能緩慢
[查詢計量](sql-api-query-metrics.md)將協助您判斷查詢花費大部分的情況。 從查詢度量，您可以看到它多少正在花在後端與用戶端。
* 如果後端查詢傳回快速，且花費大量時間在用戶端上檢查電腦上的負載。 很可能沒有足夠的資源，以及 SDK 正在等候資源，可處理回應。
* 如果後端的查詢速度很慢嘗試[的查詢進行最佳化](optimize-cost-queries.md)並查看目前[編製索引原則](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[要求速率太大]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) 連接埠耗盡]: #snat
[Production check list]: #production-check-list


