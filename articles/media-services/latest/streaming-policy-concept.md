---
title: Azure 媒體服務中的串流原則 | Microsoft Docs
description: 本文解釋串流原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/22/2018
ms.author: juliako
ms.openlocfilehash: d74ce913a2189dd1062b30f9def919cbbabe7b64
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742519"
---
# <a name="streaming-policies"></a>串流原則

Azure 媒體服務 v3 中，串流原則可讓您設定 StreamingLocators 的串流通訊協定和加密選項。 您可以指定您所建立的串流原則名稱，或使用其中一個預先定義的串流原則。 可用的預先定義串流原則如下：'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' 和 'Predefined_MultiDrmStreaming'。

> [!IMPORTANT]
> 使用自訂的 [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) 時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的加密選項和通訊協定時，對 StreamingLocators 重新使用這些原則。 媒體服務帳戶有串流原則項目的數量配額。 不建議您對每個串流定位器建立新的串流原則。

## <a name="streamingpolicy-definition"></a>StreamingPolicy 定義

下表說明 StreamingPolicy 的屬性並提供其定義。

|Name|說明|
|---|---|
|id|資源的完整資源識別碼。|
|name|資源名稱。|
|properties.commonEncryptionCbcs|CommonEncryptionCbcs 的設定|
|properties.commonEncryptionCenc|CommonEncryptionCenc 的設定|
|properties.created |串流原則的建立時間|
|properties.defaultContentKeyPolicyName |目前串流原則使用的預設 ContentKey|
|properties.envelopeEncryption  |EnvelopeEncryption 的設定|
|properties.noEncryption|Nocompression 的設定|
|type|資源類型。|

如需完整定義，請參閱[串流原則 (英文)](https://docs.microsoft.com/rest/api/media/streamingpolicies)。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

媒體服務支援下列適用於串流原則的 OData 查詢選項： 

* $filter 
* $orderby 
* $top 
* $skiptoken 

運算子說明：

* Eq = 等於
* Ne = 不等於
* Ge = 大於或等於
* Le = 小於或等於
* Gt = 大於
* Lt = 小於

### <a name="filteringordering"></a>篩選/排序

下表說明這些選項可如何套用至 StreamingPolicy 屬性： 

|Name|Filter|順序|
|---|---|---|
|id|||
|name|Eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |Eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>分頁

在四個已啟用的分類排序中，每一個皆可支援分頁。 目前的頁面大小為 10。

> [!TIP]
> 您應一律使用下一個連結來列舉集合，而不應依存於特定頁面大小。

如果查詢回應包含許多項目，服務會傳回 "\@odata.nextLink" 屬性，以取得下一頁的結果。 這可用來逐頁查看整個結果集。 您無法設定頁面大小。 

如果在逐頁查看集合時建立或刪除了 StreamingPolicy，則所做的變更會反映在傳回的結果中 (如果這些變更屬於尚未下載的集合)。 

下列 C# 範例說明如何列舉帳戶中的所有 StreamingPolicies。

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

如需 REST 範例，請參閱[串流原則 - 清單 (英文)](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="next-steps"></a>後續步驟

[串流處理檔案](stream-files-dotnet-quickstart.md)
