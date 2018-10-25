---
title: Azure 媒體服務中的串流定位器 | Microsoft Docs
description: 本文解釋串流定位器是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 1757ca84e7390f1ecd2d6d1e90a085372d3e4c57
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380675"
---
# <a name="streaming-locators"></a>串流定位器

若要為您的用戶端提供可用來播放已編碼的視訊或音訊檔案的 URL，您必須建立 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 並建置串流 URL。 如需詳細資訊，請參閱[串流處理檔案](stream-files-dotnet-quickstart.md)。

## <a name="streaminglocator-definition"></a>StreamingLocator 定義

下表顯示 StreamingLocator 的屬性並提供其定義。

|名稱|類型|說明|
|---|---|---|
|id |字串|資源的完整資源識別碼。|
|name   |字串|資源名稱。|
|properties.alternativeMediaId  |字串|此串流定位器的替代媒體識別碼。|
|properties.assetName   |字串|資產名稱|
|properties.contentKeys |StreamingLocatorContentKey[]|此串流定位器所使用的 Contentkey。|
|properties.created |字串|串流定位器的建立時間。|
|properties.defaultContentKeyPolicyName |字串|此串流定位器所使用的預設 ContentKeyPolicy 名稱。|
|properties.endTime |字串|串流定位器的結束時間。|
|properties.startTime   |字串|串流定位器的開始時間。|
|properties.streamingLocatorId  |字串|串流定位器的 StreamingLocatorId。|
|properties.streamingPolicyName |字串|此串流定位器所使用的串流原則名稱。 請指定您所建立的串流原則名稱，或使用其中一個預先定義的串流原則。 可用的預先定義串流原則如下：'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' 和 'Predefined_MultiDrmStreaming'|
|type   |字串|資源類型。|

如需完整定義，請參閱[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

媒體服務支援下列適用於串流定位器的 OData 查詢選項： 

* $filter 
* $orderby 
* $top 
* $skiptoken 

運算子描述：

* Eq = 等於
* Ne = 不等於
* Ge = 大於或等於
* Le = 小於或等於
* Gt = 大於
* Lt = 小於

### <a name="filteringordering"></a>篩選/排序

下表顯示這些選項可如何套用至 StreamingLocator 屬性： 

|名稱|Filter|順序|
|---|---|---|
|id |||
|name|Eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |Eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>分頁

在四個已啟用的分類排序中，每一個皆可支援分頁。 目前的頁面大小為 10。

> [!TIP]
> 您應一律使用「下一頁」連結來列舉集合，而不應依存於特定頁面大小。

如果查詢回應包含許多項目，服務會傳回 "\@odata.nextLink" 屬性，以取得下一頁的結果。 這可用來逐頁查看整個結果集。 您無法設定頁面大小。 

如果在逐頁查看集合時建立或刪除了 StreamingLocator，則所做的變更會反映在傳回的結果中 (如果這些變更屬於尚未下載的集合)。 

下列 C# 範例說明如何列舉帳戶中的所有 StreamingLocator。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

如需 REST 範例，請參閱[串流定位器 - 清單](https://docs.microsoft.com/rest/api/media/streaminglocators/streaminglocators_list)

## <a name="next-steps"></a>後續步驟

[串流處理檔案](stream-files-dotnet-quickstart.md)
