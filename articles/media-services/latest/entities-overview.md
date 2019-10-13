---
title: 媒體服務實體的篩選、排序、分頁-Azure |Microsoft Docs
description: 本文討論 Azure 媒體服務實體的篩選、排序、分頁。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed509ac8fea43a9c011bbbf76c1dc433cd78d43c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298961"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序、分頁

本主題討論在列出 Azure 媒體服務 v3 實體時可用的 OData 查詢選項和分頁支援。

## <a name="considerations"></a>考量

* 屬於日期時間類型的實體屬性一律為 UTC 格式。
* 在傳送要求之前，查詢字串中的空白字元應以 URL 編碼。

## <a name="comparison-operators"></a>比較運算子

您可以使用下列運算子來比較欄位與常數值：

等號比較運算子：

- `eq`:測試欄位是否**等於**常數值
- `ne`:測試欄位是否**不等於**常數值

範圍運算子：

- `gt`:測試欄位是否**大於**常數值
- `lt`:測試欄位是否**小於**常數值
- `ge`:測試欄位是否**大於或等於**常數值
- `le`:測試欄位是否**小於或等於**常數值

## <a name="filter"></a>Filter

**$filter** -使用 Filter 提供 OData 篩選參數，只尋找您感興趣的物件。

下列 REST 範例會篩選資產的替代識別碼：

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

下列C#範例會篩選資產的建立日期：

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>排序依據

**$orderby** -用來依指定的參數排序傳回的物件。 例如:    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

若要以遞增或遞減順序排序結果，請將 `asc` 或 `desc` 附加至功能變數名稱，並以空格分隔。 例如，`$orderby properties/created desc`。

## <a name="skip-token"></a>略過權杖

**$skiptoken** -如果查詢回應包含許多專案，服務會傳回您用來取得下一頁結果的略過標記（`@odata.nextLink`）值。 這可用來逐頁查看整個結果集。

在媒體服務 v3 中，您無法設定頁面大小。 頁面大小隨實體類型而不同，請參閱以下各節以取得詳細資訊。

如果逐頁查看集合時建立或刪除了實體，則所做的變更會反映在傳回的結果中 (如果這些變更屬於尚未下載的集合)。 

> [!TIP]
> 您應該一律使用 `nextLink` 來列舉集合，而不是相依于特定頁面大小。
>
> 只有在有一個以上的實體頁面時，才會顯示 `nextLink`。

請參考下列使用 $skiptoken 的範例。 請務必將 amstestaccount 取代為您的帳戶名稱，並將 api-version 值設為最新版本。

如果您要求的資產清單如下：

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

您將會收到如下所示的回應：

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

然後您會藉由傳送 get 要求來要求下一個頁面：

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

下列C#範例顯示如何列舉帳戶中的所有串流定位器。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>使用邏輯運算子結合查詢選項

媒體服務 v3 支援 ' or ' 和 ' and ' 邏輯運算子。 

下列 REST 範例會檢查作業的狀態：

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

您會在中C#建立相同的查詢，如下所示： 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>實體的篩選和排序選項

下表顯示篩選和排序選項可如何套用至不同的實體：

|實體名稱|屬性名稱|Filter|順序|
|---|---|---|---|
|[資產](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` 和 `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` 和 `desc`|
|[內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.description    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`||
||properties.lastModified|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.policyId|`eq`、 `ne`||
|[作業](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` 和 `desc`|
||properties.state        | `eq`、 `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` 和 `desc`| 
|[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`，`ne`，`ge`，`le`，`gt`，`lt`|`asc` 和 `desc`|
||properties.endTime    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
|[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
|[可轉換](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` 和 `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|

## <a name="next-steps"></a>後續步驟

* [列出資產](https://docs.microsoft.com/rest/api/media/assets/list)
* [列出內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [列出作業](https://docs.microsoft.com/rest/api/media/jobs/list)
* [列出串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [列出串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [串流處理檔案](stream-files-dotnet-quickstart.md)
* [配額和限制](limits-quotas-constraints.md)
