---
title: 了解 Azure 監視器警示的自願性移轉工具的運作方式
description: 了解警示移轉工具的運作方式，並針對問題進行疑難排解。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295535"
---
# <a name="understand-how-the-migration-tool-works"></a>了解移轉工具的運作方式

做為[先前所宣布](monitoring-classic-retirement.md)，在 Azure 監視器傳統警示正在 2019 年 8 月 31 日停用 (已原本年 6 月 30，2019年)。 客戶使用傳統的警示規則的人員，而且想要自行觸發移轉至 Azure 入口網站中使用移轉工具。

這篇文章說明自願性移轉工具的運作方式。 此外，本文也將說明一些常見問題的補救方式。

> [!NOTE]
> 因為導入的移轉工具中的延遲，所以傳統警示移轉的停用日期已[延伸到 2019 年 8 月 31 日](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)從最初宣布於 2019 年 6 月 30 日的日期。

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>將不會移轉的傳統警示規則

> [!IMPORTANT]
> 活動記錄警示 （包括服務健康情況警示） 和記錄警示不會受到移轉影響。 移轉作業僅適用於傳統的警示規則所述[此處](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)。

雖然此工具可以將幾乎所有移轉[傳統警示規則](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)，有一些例外狀況。 使用工具 （或在自動移轉開始 2019 年 9 月），不會移轉下列的警示規則：

- 在 虛擬機器客體計量 （Windows 和 Linux） 上的傳統警示規則。 請參閱[指導方針來重新建立這類警示的規則，在新的計量警示](#guest-metrics-on-virtual-machines)本文稍後。
- 傳統對傳統儲存體度量的警示規則。 請參閱[監視您的傳統儲存體帳戶的指引](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)。
- 傳統上某些儲存體帳戶計量的警示規則。 請參閱[詳細資料](#storage-account-metrics)本文稍後。
- 傳統上一些 Cosmos DB 計量的警示規則。 會在未來的更新中新增詳細資料。

如果您的訂用帳戶有任何這類傳統規則，您必須手動移轉。 我們無法提供的自動移轉，因為這些類型的任何現有、 傳統計量警示會繼續運作，直到 2020 年 6 月為止。 此延伸模組可讓您有時間將移至新的警示。 不過，可以在 2019 年 8 月之後不建立任何新的傳統警示。

> [!NOTE]
> 除了上述所列的例外狀況，如果您的傳統警示規則無效也就是它們位於[已被取代的計量](#classic-alert-rules-on-deprecated-metrics)或已刪除的資源不會移轉期間自願性移轉。 自動移轉發生時，將會刪除任何這類無效傳統警示規則。

### <a name="guest-metrics-on-virtual-machines"></a>在虛擬機器上的客體計量

您可以建立新的計量警示上的客體計量之前，客體計量必須傳送至 Azure 監視器的自訂計量存放區。 請遵循這些指示來啟用診斷設定的 Azure 監視器接收：

- [啟用適用於 Windows Vm 的客體計量](collect-custom-metrics-guestos-resource-manager-vm.md)
- [啟用適用於 Linux Vm 的客體計量](collect-custom-metrics-linux-telegraf.md)

完成這些步驟之後，您可以建立新的計量警示上的客體計量。 並建立新的計量警示之後，您可以刪除傳統警示。

### <a name="storage-account-metrics"></a>儲存體帳戶計量

除了這些度量的警示，可以移轉儲存體帳戶上的所有傳統警示：

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

百分比的計量規則必須移轉的傳統警示依據[舊的和新的儲存體度量之間的對應](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)。 需要因為可用的新計量是絕對適當地修改臨界值。

傳統的警示規則上 AnonymousThrottlingError、 SASThrottlingError 和 ThrottlingError 必須分割成兩個新的警示，因為沒有任何合併的度量會提供相同的功能。 將會需要適當地調整閾值。

### <a name="classic-alert-rules-on-deprecated-metrics"></a>傳統的警示規則已被取代的計量

這些是傳統的警示規則上先前支援，但最終已被取代的計量。 這類度量一小部分的客戶可能會有無效的傳統警示規則。 由於這些警示規則都是無效的它們不會移轉。

| 資源類型| 已被取代的計量 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective、 storage_limit、 storage_used、 節流、 dtu_consumption_percent、 storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>如何建立對等的新警示規則和動作群組

移轉工具會將傳統的警示規則轉換為相等的新警示規則和動作群組。 大部分傳統的警示規則，對等的新警示規則會使用相同的屬性相同的計量這類`windowSize`和`aggregationType`。 不過，有一些傳統的警示規則會在新的系統中有不同的對等計量的度量。 除非在下一節中指定的傳統警示移轉至適用於下列原則：

- **頻率**:定義傳統或新的警示規則條件檢查的頻率。 `frequency`傳統的警示規則中由使用者設定並不一定是 5 分鐘，除非它是 1 分鐘的 Application Insights 元件的所有資源類型。因此對等規則的頻率也是分別設定為 5 分鐘與 1 分鐘。
- **彙總類型**:定義如何感興趣的間隔彙總度量。 `aggregationType`也是傳統的警示和大部分的計量的新警示相同。 在某些情況下，因為度量是傳統和新的警示，對等之間不同`aggregationType`或`primary Aggregation Type`中定義的度量。
- **單位**:建立警示的計量的屬性。 某些對等的計量會有不同的單位。 適當地視需要調整臨界值。 比方說，如果原有的度量單位為秒，但對等的新計量有毫秒為單位，原始的臨界值將會乘以 1000，以確保相同的行為。
- **視窗大小**:定義要比較根據臨界值的資料彙總的度量的視窗。 適用於標準`windowSize`5mins、 15mins、 30 分鐘，再，值 1 小時、 3 個小時，6 小時、 12 小時、 1 天，沒有任何對等的新警示規則所做的變更。 如需其他值，最接近`windowSize`選擇要用。 對大多數客戶來說不會影響這項變更。 針對一小部分的客戶，可能需要調整臨界值，以取得完全相同的行為。

在下列章節中，我們會詳細說明新的系統中有不同的對等計量的度量。 未列出任何維持不變的傳統和新的警示規則的度量。 您可以找到一份新的系統中支援的計量[此處](metrics-supported.md)。

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

例如 blob、 資料表、 檔案和佇列儲存體帳戶服務，下列計量會對應至對等的計量，如下所示：

| 在傳統警示的計量 | 在新警示的對等計量 | 註解|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 交易計量具有維度 「 ResponseType"="AuthorizationError 」 和 「 驗證 」 ="Anonymous"| |
| AnonymousClientOtherError | 交易計量具有維度 「 ResponseType"="ClientOtherError 」 和 「 驗證 」 ="Anonymous" | |
| AnonymousClientTimeOutError| 交易計量具有維度 「 ResponseType"="ClientTimeOutError 」 和 「 驗證 」 ="Anonymous" | |
| AnonymousNetworkError | 交易計量具有維度 「 ResponseType"="NetworkError 」 和 「 驗證 」 ="Anonymous" | |
| AnonymousServerOtherError | 交易計量具有維度 「 ResponseType"="ServerOtherError 」 和 「 驗證 」 ="Anonymous" | |
| AnonymousServerTimeOutError | 交易計量具有維度 「 ResponseType"="ServerTimeOutError 」 和 「 驗證 」 ="Anonymous" | |
| AnonymousSuccess | 交易計量具有維度 「 ResponseType"="Success"和"Authentication"="Anonymous" | |
| AuthorizationError | 交易計量具有維度 「 ResponseType"="AuthorizationError 」 | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 容量 | BlobCapacity | 使用`aggregationType`「 平均 」 而不是 'text-align-last'。 計量僅適用於 Blob 服務 |
| ClientOtherError | 交易計量具有維度 「 ResponseType"="ClientOtherError 」  | |
| ClientTimeoutError | 交易計量具有維度 「 ResponseType"="ClientTimeOutError 」 | |
| ContainerCount | ContainerCount | 使用`aggregationType`「 平均 」 而不是 'text-align-last'。 計量僅適用於 Blob 服務 |
| NetworkError | 交易計量具有維度 「 ResponseType"="NetworkError 」 | |
| ObjectCount | BlobCount| 使用`aggregationType`「 平均 」 而不是 'text-align-last'。 計量僅適用於 Blob 服務 |
| SASAuthorizationError | 交易計量具有維度 「 ResponseType"="AuthorizationError 」 和 「 驗證 」 ="SAS" | |
| SASClientOtherError | 交易計量具有維度 「 ResponseType"="ClientOtherError 」 和 「 驗證 」 ="SAS" | |
| SASClientTimeOutError | 交易計量具有維度 「 ResponseType"="ClientTimeOutError 」 和 「 驗證 」 ="SAS" | |
| SASNetworkError | 交易計量具有維度 「 ResponseType"="NetworkError 」 和 「 驗證 」 ="SAS" | |
| SASServerOtherError | 交易計量具有維度 「 ResponseType"="ServerOtherError 」 和 「 驗證 」 ="SAS" | |
| SASServerTimeOutError | 交易計量具有維度 「 ResponseType"="ServerTimeOutError 」 和 「 驗證 」 ="SAS" | |
| SASSuccess | 交易計量具有維度 「 ResponseType"="Success"和"Authentication"="SAS" | |
| ServerOtherError | 交易計量具有維度 「 ResponseType"="ServerOtherError 」 | |
| ServerTimeOutError | 交易計量具有維度 「 ResponseType"="ServerTimeOutError 」  | |
| 成功 | 交易計量具有維度 「 ResponseType"= 「 成功 」 | |
| TotalBillableRequests| 交易 | |
| TotalEgress | 輸出 | |
| TotalIngress | 輸入 | |
| TotalRequests | 交易 | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Application Insights 的對等的度量資訊會是如下所示：

| 在傳統警示的計量 | 在新警示的對等計量 | 註解|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| 傳統的計量的單位是以秒為單位，而新其中會以毫秒為單位，則請乘以 1000年原始的臨界值。  |
| basicExceptionBrowser.count | exceptions/browser|  使用`aggregationType`'count'，而不是 'sum'。 |
| basicExceptionServer.count | exceptions/server| 使用`aggregationType`'count'，而不是 'sum'。  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| 傳統的計量的單位是以秒為單位，而新其中會以毫秒為單位，則請乘以 1000年原始的臨界值。  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  傳統的計量的單位是以秒為單位，而新其中會以毫秒為單位，則請乘以 1000年原始的臨界值。 |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| 傳統的計量的單位是以秒為單位，而新其中會以毫秒為單位，則請乘以 1000年原始的臨界值。  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| 傳統的計量的單位是以秒為單位，而新其中會以毫秒為單位，則請乘以 1000年原始的臨界值。  |
| clientPerformance.total.value | browserTimings/totalDuration| 傳統的計量的單位是以秒為單位，而新其中會以毫秒為單位，則請乘以 1000年原始的臨界值。  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| 閾值必須適當地修改原始的計量是跨所有核心以及新的計量會正規化為一個核心。 移轉工具不會變更臨界值。  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| 傳統的計量的單位是以秒為單位，而新其中會以毫秒為單位，則請乘以 1000年原始的臨界值。  |
| request.rate | requests/rate|   |
| requestFailed.count | requests/failed| 使用`aggregationType`'count'，而不是 'sum'。   |
| view.count | pageViews/count| 使用`aggregationType`'count'，而不是 'sum'。   |

### <a name="how-equivalent-action-groups-are-created"></a>如何建立對等的動作群組

傳統的警示規則有電子郵件、 webhook，邏輯應用程式和 runbook 動作繫結至警示規則本身。 新的警示規則會使用動作群組可以跨多個警示規則重複使用。 移轉工具會建立相同的動作，無論多少警示規則會使用動作的單一動作群組。 移轉工具所建立的動作群組使用的命名格式 ' Migrated_AG *'。

## <a name="rollout-phases"></a>首度發行階段

移轉工具推出階段中使用傳統的警示規則的客戶。 訂用帳戶已準備好使用此工具進行移轉時，訂用帳戶擁有者會收到一封電子郵件。

> [!NOTE]
> 因為此工具推出階段中，您可能會看到，部分訂用帳戶未尚未準備好進行移轉的先期階段。

大部分的訂用帳戶目前標示為準備好進行移轉。 對下列資源類型的傳統警示的訂閱是仍然未準備好進行移轉。

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>誰可以觸發移轉？

任何擁有訂用帳戶層級的內建的監視參與者 」 角色的使用者可以觸發移轉。 具有下列權限的自訂角色的使用者也可以觸發移轉：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> 除了擁有以上權限，此外應該 Microsoft.AlertsManagement 資源提供者註冊訂用帳戶。 如此才能將 Application Insights 失敗異常警示成功移轉。 

## <a name="common-problems-and-remedies"></a>常見的問題和解決方式

之後您[觸發程序移轉](alerts-using-migration-tool.md)，您會收到電子郵件到您提供給通知您移轉已完成，或您是否需要任何動作的位址。 本章節描述一些常見的問題，以及如何處理它們。

### <a name="validation-failed"></a>驗證失敗

由於傳統警示的規則，您的訂用帳戶中一些最新變更，因此無法移轉訂用帳戶。 這個問題是暫時性的。 移轉狀態回復之後，您可以重新啟動移轉**準備好進行移轉**幾天。

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>原則或範圍的鎖定，導致我們無法移轉您的規則

移轉的一部分，將會建立新的計量警示和新的動作群組，與傳統的警示規則將被刪除。 不過，沒有原則或範圍的鎖定，導致我們無法建立資源。 根據原則或範圍鎖定，無法移轉部分或所有的規則。 您可以解決這個問題，暫時移除範圍鎖定或原則，並觸發一次移轉。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [為移轉做準備](alerts-prepare-migration.md)
