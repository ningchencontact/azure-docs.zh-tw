---
title: 诊断和排查使用 Azure Cosmos DB .NET SDK 时遇到的问题
description: 使用客户端日志记录等功能和其他第三方工具来确定、诊断和排查使用 .NET SDK 时遇到的 Azure Cosmos DB 问题。
author: j82w
ms.service: cosmos-db
ms.date: 01/19/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7f969ab6059140ec32c9c5bf5045c546602a3c15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404696"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>诊断和排查使用 Azure Cosmos DB .NET SDK 时遇到的问题
本文介绍了将 [.NET SDK](sql-api-sdk-dotnet.md) 与 Azure Cosmos DB SQL API 帐户配合使用时的常见问题、解决方法、诊断步骤和工具。
.NET SDK 提供客户端逻辑表示用于访问 Azure Cosmos DB SQL API。 此文章所說明的工具和方法，可以在您遇到任何問題時提供協助。

## <a name="checklist-for-troubleshooting-issues"></a>问题排查清单：
在将应用程序投放生产之前，请查看以下清单。 使用该清单有助于防止出现多个常见问题。 出现问题时可以快速诊断：

*   使用最新的 [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)。 不应该将预览版 SDK 用于生产。 这可以防止出现已予以修复的已知问题。
*   檢閱[效能祕訣](performance-tips.md)並遵循建議的做法。 这有助于防止缩放、延迟和其他性能问题。
*   启用 SDK 日志记录以帮助排查问题。 啟用記錄功能可能會影響效能，因此建議您最好針對問題進行疑難排解時才加以啟用。 可以启用以下日志：
    *   使用 Azure 门户[记录指标](monitor-accounts.md)。 入口網站計量顯示 Azure Cosmos DB 的遙測，很有幫助判斷是否對應至 Azure Cosmos DB 的問題，或者如果它是從用戶端。
    *   记录点操作响应中的[诊断字符串](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)。
    *   记录所有查询响应中的 [SQL 查询指标](sql-api-query-metrics.md) 
    *   遵循有关 [SDK 日志记录]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的设置

查看此文章的[常見問題和因應措施](#common-issues-workarounds)一節。

查看我们积极关注的 [GitHub 问题部分](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)。 查看您是否有任何含有已提出因應措施的類似問題。 如果找不到解决方法，请提出 GitHub 问题。 对于紧急问题，可以开具支持票证。


## <a name="common-issues-workarounds"></a>常見問題和因應措施

### <a name="general-suggestions"></a>一般建議
* 尽可能在 Azure Cosmos DB 帐户所在的同一个 Azure 区域中运行应用。 
* 由于客户端计算机上的资源不足，你可能会遇到连接/可用性问题。 我们建议监视运行 Azure Cosmos DB 客户端的节点上的 CPU 利用率，如果这些节点的负载较大，请纵向/横向扩展节点。

### <a name="check-the-portal-metrics"></a>检查门户指标
检查[门户指标](monitor-accounts.md)有助于确定问题是否与客户端相关，或者服务是否有问题。 例如，如果指标中包含较高比率的速率受限请求（HTTP 状态代码 429，表示请求受到限制），请查看[请求速率过大]部分。 

### <a name="request-timeouts"></a>要求逾時
使用直接/TCP 连接时往往会发生 RequestTimeout，但在网关模式下也可能会发生。 下面是常见的已知原因，以及有关如何解决问题的建议。

* CPU 利用率较高，导致延迟和/或请求超时。 客户可以纵向扩展主机以便为其提供更多的资源，或者可将负载分散到更多的计算机。
* 套接字/端口可用性可能较低。 使用低于 2.0 版的 .NET SDK 时，Azure 中运行的客户端可能会遇到 [Azure SNAT (PAT) 連接埠耗盡]。 正因如此，我们建议始终运行最新的 SDK 版本。
* 创建多个 DocumentClient 实例可能会导致连接争用和超时问题。 遵循[性能提示](performance-tips.md)，并在整个进程中使用单个 DocumentClient 实例。
* 用户有时会看到延迟或请求超时增大，原因是集合的预配不足、后端限制了请求，或者客户端在不提示调用方的情况下在内部重试。 检查[门户指标](monitor-accounts.md)。
* Azure Cosmos DB 在物理分区之间均匀分配预配的总吞吐量。 请检查门户指标，以确定工作负荷是否遇到了热[分区键](partition-data.md)。 热分区键会导致消耗的聚合吞吐量 (RU/s) 看上去低于预配的 RU，但单个分区消耗的吞吐量 (RU/s) 会超过预配的吞吐量。 
* 此外，2.0 SDK 将通道语义添加到了直接/TCP 连接。 同时为多个请求使用一个 TCP 连接。 在特定的情况下，这可能会导致两个问题：
    * 高并发度可能导致通道中出现争用。
    * 大的请求或响应可能导致通道上出现行首阻塞，加剧资源争用，甚至出现相对较低的并发度。
    * 如果遇到上述两种情况之一（或者怀疑 CPU 利用率较高），可能的缓解措施如下：
        * 尝试纵向/横向扩展应用程序。
        * 此外，可以通过[跟踪侦听器](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)捕获 SDK 日志，以获取更多详细信息。

### <a name="connection-throttling"></a>連線節流
主机上的连接受限可能导致连接限制。 在版本低于 2.0 的 SDK 中，Azure 中运行的客户端可能会遇到 [Azure SNAT (PAT) 連接埠耗盡]。

### <a name="snat"></a>Azure SNAT (PAT) 連接埠耗盡

如果您的應用程式部署於不具公用 IP 位址的 Azure 虛擬機器上，則 [Azure SNAT 連接埠](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)預設會建立與您 VM 外部任何端點的連線。 從 VM 到 Azure Cosmos DB 端點所允許的連線數目會受到 [Azure SNAT 設定](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)所限制。

 只有在您的 VM 具有私人 IP 位址，而且來自 VM 的程序嘗試連線到公用 IP 位址時，才會使用 Azure SNAT 連接埠。 有兩種因應措施可避免 Azure SNAT 限制：

* 將 Azure Cosmos DB 服務端點新增至您的 Azure 虛擬機器虛擬網路。 如需詳細資訊，請參閱 [Azure 虛擬網路服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)。 

    啟用服務端點時，要求不再會從公用 IP 傳送到 Azure Cosmos DB。 改為傳送虛擬網路和子網路身分識別。 如果只允許公用 IP，此變更可能會導致防火牆卸除。 如果您使用防火牆，當您啟用服務端點時，請使用[虛擬網路 ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) 將子網路新增至防火牆。
* 將公用 IP 指派給您的 Azure VM。

### <a name="http-proxy"></a>HTTP Proxy
如果您使用 HTTP Proxy，請確定它可以支援在 SDK `ConnectionPolicy` 中設定的連線數目。
否則就會遇到連線問題。

### 请求速率过大<a name="request-rate-too-large"></a>
“请求速率过大”或错误代码 429 表示请求正受到限制，因为消耗的吞吐量 (RU/s) 已超过预配的吞吐量。 SDK 会根据指定的[重试策略](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)自动重试请求。 如果经常遇到这种失败，请考虑增大集合的吞吐量。 請檢查[入口網站的計量](use-metrics.md)以查看 是否您收到 429 錯誤。 查看[分区键](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)，以确保均匀分配存储和请求量。 

### <a name="slow-query-performance"></a>查询性能较低
[查询指标](sql-api-query-metrics.md)有助于确定查询在哪个位置花费的时间最多。 在查询指标中，可以查看查询在客户端与后端上花费的时间。
* 如果后端查询的返回速度较快，并将大量的时间花费在客户端上，请检查计算机上的负载。 可能的原因是资源不足，SDK 正在等待提供可用的资源来处理响应。
* 如果后端查询速度较慢，请尝试[优化查询](optimize-cost-queries.md)，并查看当前的[索引策略](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[请求速率过大]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) 連接埠耗盡]: #snat
[Production check list]: #production-check-list


