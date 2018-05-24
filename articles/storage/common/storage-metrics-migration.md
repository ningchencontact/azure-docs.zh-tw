---
title: Azure 儲存體計量移轉 | Microsoft Docs
description: 了解如何將舊版計量移轉至由 Azure 監視器管理的新計量。
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: da8eb0b9e2e5aba60b61a36d83f525c7ce4a7958
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776256"
---
# <a name="azure-storage-metrics-migration"></a>Azure 儲存體計量移轉

為了讓 Azure 有一致的監視器體驗，Azure 儲存體在 Azure 監視器平台上引入計量功能。 未來，舊版計量的服務將會根據 Azure 原則結束，但會提前通知。 如果您依賴舊版的儲存體計量，則必須在服務結束日期之前移轉，以保留您的計量資訊。

本文將引導您從舊版計量移轉至新計量。

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>了解由 Azure 儲存體管理的舊版計量

Azure 儲存體會收集舊版計量值、加以彙總並儲存在相同儲存體帳戶內的 $Metric 表格中。 您可以使用 Azure 入口網站設定監視圖表，也可以使用 Azure 儲存體 SDK 根據結構描述來讀取 $Metric 表格的資料。 如需詳細資訊，您可以參考此[儲存體分析](./storage-analytics.md)。

舊版計量只提供 Blob 的容量計量，以及 Blob、表格、檔案和佇列的交易計量。

舊版計量是以一般的結構描述來設計。 如果您沒有觸發計量的流量模式，此設計會產生零計量值。 例如，即使未從傳輸到儲存體帳戶的即時流量接收到任何伺服器逾時錯誤，**ServerTimeoutError** 在 $Metric 表格中也會設定為 0。

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>了解由 Azure 監視器管理的新計量

對於新的儲存體計量，Azure 儲存體會將計量資料傳送到 Azure 監視器後端。 Azure 監視器會提供統一的監視體驗，包括從入口網站的資料到資料擷取。 如需詳細資訊，您可以參考這篇[文章](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

新計量提供 Blob、表格、檔案、佇列和進階儲存體的容量計量和交易計量。

多維度是 Azure 監視器提供的其中一個功能。 Azure 儲存體採用此種設計來定義新的計量結構描述。 如需支援的計量維度，您可以在 [Azure 監視器中的 Azure 儲存體計量](./storage-metrics-in-azure-monitor.md)中找到詳細資訊。 多維度設計能針對擷取的頻寬和儲存計量的容量兩者提供成本效益。 因此，如果您的流量未觸發相關的計量，就不會產生相關的計量資料。 例如，如果您的流量未觸發任何伺服器逾時錯誤，當您查詢維度 **ResponseType** 等於 **ServerTimeoutError** 的 **Transactions** 計量值時，Azure 監視器不會傳回任何資料。

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>舊版計量和新計量之間的計量對應

如果您以程式設計方式讀取計量資料，就必須在程式中採用新的計量結構描述。 若要進一步了解變更，您可以參考下表列出的對應：

**容量計量**

| 舊版計量 | 新計量 |
| ------------------- | ----------------- |
| Capacity            | 維度 BlobType 等於 BlockBlob 或 PageBlob 的 BlobCapacity |
| ObjectCount         | 維度 BlobType 等於 BlockBlob 或 PageBlob 的 BlobCount |
| ContainerCount      | ContainerCount |

下列計量是舊版計量不支援的新項目：
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**交易計量**

| 舊版計量 | 新計量 |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | 維度 ResponseType 等於 AuthorizationError 的 Transactions |
| AnonymousClientOtherError | 維度 ResponseType 等於 ClientOtherError 的 Transactions |
| AnonymousClientTimeoutError | 維度 ResponseType 等於 ClientTimeoutError 的 Transactions |
| AnonymousNetworkError | 維度 ResponseType 等於 NetworkError 的 Transactions |
| AnonymousServerOtherError | 維度 ResponseType 等於 ServerOtherError 的 Transactions |
| AnonymousServerTimeoutError | 維度 ResponseType 等於 ServerTimeoutError 的 Transactions |
| AnonymousSuccess | 維度 ResponseType 等於 Success 的 Transactions |
| AnonymousThrottlingError | 維度 ResponseType 等於 ClientThrottlingError 或 ServerBusyError 的 Transactions |
| AuthorizationError | 維度 ResponseType 等於 AuthorizationError 的 Transactions |
| 可用性 | 可用性 |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | 維度 ResponseType 等於 ClientOtherError 的 Transactions |
| ClientTimeoutError | 維度 ResponseType 等於 ClientTimeoutError 的 Transactions |
| NetworkError | 維度 ResponseType 等於 NetworkError 的 Transactions |
| PercentAuthorizationError | 維度 ResponseType 等於 AuthorizationError 的 Transactions |
| PercentClientOtherError | 維度 ResponseType 等於 ClientOtherError 的 Transactions |
| PercentNetworkError | 維度 ResponseType 等於 NetworkError 的 Transactions |
| PercentServerOtherError | 維度 ResponseType 等於 ServerOtherError 的 Transactions |
| PercentSuccess | 維度 ResponseType 等於 Success 的 Transactions |
| PercentThrottlingError | 維度 ResponseType 等於 ClientThrottlingError 或 ServerBusyError 的 Transactions |
| PercentTimeoutError | 維度 ResponseType 等於 ServerTimeoutError 或 ResponseType 等於 ClientTimeoutError 的 Transactions|
| SASAuthorizationError | 維度 ResponseType 等於 AuthorizationError 的 Transactions |
| SASClientOtherError | 維度 ResponseType 等於 ClientOtherError 的 Transactions |
| SASClientTimeoutError | 維度 ResponseType 等於 ClientTimeoutError 的 Transactions |
| SASNetworkError | 維度 ResponseType 等於 NetworkError 的 Transactions |
| SASServerOtherError | 維度 ResponseType 等於 ServerOtherError 的 Transactions |
| SASServerTimeoutError | 維度 ResponseType 等於 ServerTimeoutError 的 Transactions |
| SASSuccess | 維度 ResponseType 等於 Success 的 Transactions |
| SASThrottlingError | 維度 ResponseType 等於 ClientThrottlingError 或 ServerBusyError 的 Transactions |
| ServerOtherError | 維度 ResponseType 等於 ServerOtherError 的 Transactions |
| ServerTimeoutError | 維度 ResponseType 等於 ServerTimeoutError 的 Transactions |
| 成功 | 維度 ResponseType 等於 Success 的 Transactions |
| ThrottlingError | 維度 ResponseType 等於 ClientThrottlingError 或 ServerBusyError 的 Transactions |
| TotalBillableRequests | 交易 |
| TotalEgress | 輸出 |
| TotalIngress | 輸入 |
| TotalRequests | 交易 |

## <a name="faq"></a>常見問題集

* 如何移轉現有的警示規則？

答：如果您已經根據舊版儲存體計量建立傳統的警示規則，就必須根據新的計量結構描述來建立新的警示規則。

* 新的計量資料是否預設儲存在相同的儲存體帳戶中？

答：否。 如果您要將計量資料封存至儲存體帳戶，可以使用 [Azure 監視器診斷設定 API](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate) \(英文\)

## <a name="next-steps"></a>後續步驟

* [Azure 監視器](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure 監視器中的儲存體計量](./storage-metrics-in-azure-monitor.md)
