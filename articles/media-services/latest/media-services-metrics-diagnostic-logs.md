---
title: 監視 Azure 媒體服務的計量和透過 Azure 監視器的診斷記錄 |Microsoft Docs
description: 這篇文章提供如何監視 Azure 媒體服務的計量和診斷的記錄，透過 Azure 監視器的概觀。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806002"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>監視媒體服務的計量和診斷記錄

[Azure 監視器](../../azure-monitor/overview.md)可讓您監視計量和診斷的記錄檔，可協助您了解如何執行您的應用程式。 Azure 監視器所收集的所有資料都適合其中兩個基本的型別，度量和記錄檔。 您可以監視媒體服務的診斷記錄檔，並建立警示和通知所收集的計量和記錄檔。 您以視覺化方式檢視和分析計量使用資料[計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)。 您可以將記錄傳送至[Azure 儲存體](https://azure.microsoft.com/services/storage/)，串流至[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)，並將它們以匯出[Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用第 3 方服務。

如需詳細概觀，請參閱[Azure 監視器計量](../../azure-monitor/platform/data-platform.md)並[Azure 監視器診斷記錄](../../azure-monitor/platform/diagnostic-logs-overview.md)。

本主題將討論支援[媒體服務度量](#media-services-metrics)並[Media Services 診斷記錄](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒體服務度量

不論值是否變更，系統都會定期收集計量。 它們適合用於警示，因為它們可頻繁地進行取樣，而且可使用相對較簡單的邏輯快速引發警示。 如需如何建立計量警示的詳細資訊，請參閱[建立、 檢視及管理使用 Azure 監視器計量警示](../../azure-monitor/platform/alerts-metric.md)。

媒體服務支援監視計量的下列資源：

* 帳戶
* 串流端點
 
### <a name="account"></a>帳戶

您可以監視下列帳戶計量。 

|度量名稱|Display name|描述|
|---|---|---|
|AssetCount|資產計數|在您的帳戶中的資產。|
|AssetQuota|資產配額|在您的帳戶中的資產配額。|
|AssetQuotaUsedPercentage|資產使用的配額百分比|已在使用中的資產配額的百分比。|
|ContentKeyPolicyCount|內容金鑰原則計數|在您的帳戶中的內容索引鍵原則。|
|ContentKeyPolicyQuota|內容金鑰原則的配額|在您的帳戶中的內容金鑰原則配額。|
|ContentKeyPolicyQuotaUsedPercentage|內容金鑰原則的配額使用百分比|已在使用中的內容金鑰原則配額的百分比。|
|StreamingPolicyCount|串流原則計數|在您的帳戶中的資料流處理原則。|
|StreamingPolicyQuota|串流處理原則的配額|在您的帳戶中的資料流原則配額。|
|StreamingPolicyQuotaUsedPercentage|串流處理原則的配額使用百分比|已在使用中的資料流的原則配額的百分比。|
 
您也應該檢閱[帳戶的配額和限制](limits-quotas-constraints.md)。

### <a name="streaming-endpoint"></a>串流端點

下列的 Media Services[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)支援計量：

|度量名稱|Display name|描述|
|---|---|---|
|Requests|Requests|提供串流端點所提供的 HTTP 要求的總數。|
|輸出|輸出|輸出位元組總數。 比方說，位元組資料流處理的串流端點。|
|SuccessE2ELatency|成功的端對端延遲|從串流端點收到要求時傳送回應的最後一個位元組時持續時間。|

### <a name="why-would-i-want-to-use-metrics"></a>為什麼要使用計量？ 

以下是如何監視媒體服務度量可協助您了解如何執行您的應用程式的範例。 使用媒體服務度量可以解決一些問題如下：

* 如何監視知道我已超過限制時我標準串流端點？
* 如何知道是否有足夠的進階串流端點縮放單位？ 
* 如何設定警示知道何時要相應增加我的資料流端點？
* 如何設定警示，以了解已到達最大帳戶上設定的輸出？
* 如何查看的明細而失敗的要求，並造成失敗的原因？
* 如何查看多少 HLS 或虛線的要求會取自封裝程式？
* 如何設定警示以得知已點擊的失敗要求的數目的臨界值？ 

### <a name="example"></a>範例

請參閱[如何監視媒體服務度量](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>媒體服務的診斷記錄

診斷記錄會提供有關 Azure 資源之作業的豐富、 經常性資料。 如需詳細資訊，請參閱 <<c0> [ 如何收集並取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/diagnostic-logs-overview.md)。

媒體服務支援下列的診斷記錄檔：

* 金鑰傳遞

### <a name="key-delivery"></a>金鑰傳遞

|名稱|描述|
|---|---|
|金鑰傳遞服務要求|顯示金鑰傳遞服務要求資訊的記錄檔。 如需詳細資訊，請參閱 <<c0> [ 結構描述](media-services-diagnostic-logs-schema.md)。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>為什麼要使用診斷記錄？ 

金鑰傳遞診斷記錄，您可以檢查一些事項如下：

* 請參閱 DRM 類型所提供的授權數目
* 請參閱原則所提供的授權數目 
* 請參閱 DRM] 或 [原則類型的錯誤
* 查看來自用戶端的未經授權的授權要求的數目

### <a name="example"></a>範例

請參閱[如何監視媒體服務的診斷記錄](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>後續步驟 

* [如何收集並取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [建立、 檢視及管理使用 Azure 監視器計量警示](../../azure-monitor/platform/alerts-metric.md)
* [如何監視媒體服務度量](media-services-metrics-howto.md)
* [如何監視媒體服務的診斷記錄](media-services-diagnostic-logs-howto.md)
