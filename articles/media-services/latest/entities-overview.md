---
title: 使用 v3 Api-Azure 進行開發 |Microsoft Docs
description: 這篇文章會討論使用媒體服務 v3 進行開發時套用至實體和 Api 的規則。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/30/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6bf0efd6994315d56e7b1b2447ffed9154cf5ee5
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804865"
---
# <a name="developing-with-media-services-v3-apis"></a>使用媒體服務 v3 Api 進行開發

這篇文章會討論使用媒體服務 v3 進行開發時套用至實體和 Api 的規則。

## <a name="naming-conventions"></a>命名慣例

Azure 媒體服務 v3 資源名稱 (例如資產、作業、轉換) 會受到 Azure Resource Manager 命名規則的約束。 根據 Azure Resource Manager，資源名稱永遠是唯一的。 因此，您可以對資源名稱使用任何唯一識別碼字串 (例如，GUID)。 

媒體服務資源名稱不可包含：'<'、'>'、'%'、'&'、':'、'&#92;'、'?'、'/'、'*'、'+'、'.'、單引號字元或任何控制字元。 允許所有其他字元。 資源名稱的長度上限是 260 個字元。 

如需有關 Azure Resource Manager 命名的詳細資訊，請參閱：[命名需求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名慣例](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。

## <a name="v3-api-design-principles"></a>v3 API 設計原則

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 API 不會從**取得**或**列出**作業傳回秘密或認證。 回應中的金鑰一律為 Null、空白或處理過的。 您必須呼叫個別的動作方法，以取得秘密或認證。 在有些 API 會擷取/顯示秘密，而有些 API 不會的情況下，個別的動作可讓您設定不同的 RBAC 安全性權限。 如需如何使用 RBAC 管理存取權的相關資訊，請參閱[使用 RBAC 管理存取權](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)。

範例包括：

* 不在 StreamingLocator Get 會傳回 ContentKey 值。
* 不在 ContentKeyPolicy Get 會傳回限制索引鍵。
* 不會傳回作業的 HTTP 輸入 Url 的 URL （若要移除簽章） 的查詢字串部分。

請參閱[取得內容金鑰原則 - .NET](get-content-key-policy-dotnet-howto.md) 範例。

## <a name="long-running-operations"></a>長時間執行的作業

將作業標示`x-ms-long-running-operation`Azure 媒體服務中[swagger 檔案](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)是長時間執行的作業。 

如需如何追蹤非同步 Azure 作業的詳細資訊，請參閱[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)

媒體服務有下列的長時間執行作業：

* 建立 LiveEvent
* 更新 LiveEvent
* 刪除 LiveEvent
* Start LiveEvent
* 停止 LiveEvent
* 重設 LiveEvent
* 建立 LiveOutput
* Delete LiveOutput
* 建立 StreamingEndpoint
* 更新 StreamingEndpoint
* 刪除 StreamingEndpoint
* 啟動 StreamingEndpoint
* 停止 StreamingEndpoint
* 調整 StreamingEndpoint


## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序、分頁

媒體服務支援下列適用於媒體服務 v3 實體的 OData 查詢選項： 

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

屬於日期時間類型的實體屬性一律為 UTC 格式。

### <a name="page-results"></a>頁面結果

如果查詢回應包含許多項目，服務會傳回 "\@odata.nextLink" 屬性，以取得下一頁的結果。 這可用來逐頁查看整個結果集。 您無法設定頁面大小。 頁面大小隨實體類型而不同，請參閱以下各節以取得詳細資訊。

如果逐頁查看集合時建立或刪除了實體，則所做的變更會反映在傳回的結果中 (如果這些變更屬於尚未下載的集合)。 

> [!TIP]
> 您應一律使用「下一頁」連結來列舉集合，而不應依存於特定頁面大小。

### <a name="assets"></a>Assets

#### <a name="filteringordering"></a>篩選/排序

下表說明篩選和排序選項如何套用至[資產](https://docs.microsoft.com/rest/api/media/assets)屬性： 

|名稱|Filter|順序|
|---|---|---|
|id|||
|name|eq、gt、lt| 遞增和遞減|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq、gt、lt| 遞增和遞減|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

下列 C# 範例會以建立日期篩選：

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

#### <a name="pagination"></a>分頁 

在四個已啟用的分類排序中，每一個皆可支援分頁。 目前的頁面大小為 1000。

##### <a name="c-example"></a>C# 範例

下列 C# 範例會示範如何列舉帳戶中的所有資產。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>REST 範例

請參考下列使用 $skiptoken 的範例。 請務必將 amstestaccount 取代為您的帳戶名稱，並將 api-version 值設為最新版本。

如果您要求的資產清單如下：

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

您將會收到如下所示的回應：

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

然後您會藉由傳送 get 要求來要求下一個頁面：

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

如需其他 REST 範例，請參閱[資產 - 清單](https://docs.microsoft.com/rest/api/media/assets/list)

### <a name="content-key-policies"></a>內容金鑰原則

#### <a name="filteringordering"></a>篩選/排序

下表說明這些選項如何套用至[內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies)屬性： 

|名稱|Filter|順序|
|---|---|---|
|id|||
|name|eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.created |eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.description |eq、ne、ge、le、gt、lt||
|properties.lastModified|eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.options |||
|properties.policyId|eq、ne||
|type|||

#### <a name="pagination"></a>分頁

在四個已啟用的分類排序中，每一個皆可支援分頁。 目前的頁面大小為 10。

下列 C# 範例說明如何列舉帳戶中的所有**內容金鑰原則**。

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

如需 REST 範例，請參閱[內容金鑰原則 - 清單 (英文)](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

### <a name="jobs"></a>工作

#### <a name="filteringordering"></a>篩選/排序

下表說明這些選項如何套用至[作業](https://docs.microsoft.com/rest/api/media/jobs)屬性： 

| 名稱    | Filter                        | 順序 |
|---------|-------------------------------|-------|
| name                    | eq            | 遞增和遞減|
| properties.state        | eq、ne        |                         |
| properties.created      | gt、ge、lt、le| 遞增和遞減|
| properties.lastModified | gt、ge、lt、le | 遞增和遞減| 


#### <a name="pagination"></a>分頁

媒體服務 v3 支援分頁作業。

下列 C# 範例會示範如何列舉帳戶中的作業。

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

如需 REST 範例，請參閱[資產 - 清單](https://docs.microsoft.com/rest/api/media/jobs/list)

### <a name="streaming-locators"></a>串流定位器

#### <a name="filteringordering"></a>篩選/排序

下表顯示這些選項可如何套用至 StreamingLocator 屬性： 

|名稱|Filter|順序|
|---|---|---|
|id |||
|name|eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

#### <a name="pagination"></a>分頁

在四個已啟用的分類排序中，每一個皆可支援分頁。 目前的頁面大小為 10。

下列 C# 範例說明如何列舉帳戶中的所有 StreamingLocator。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

如需 REST 範例，請參閱[串流定位器 - 清單](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

### <a name="streaming-policies"></a>串流原則

#### <a name="filteringordering"></a>篩選/排序

下表說明這些選項可如何套用至 StreamingPolicy 屬性： 

|名稱|Filter|順序|
|---|---|---|
|id|||
|name|eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq、ne、ge、le、gt、lt|遞增和遞減|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

#### <a name="pagination"></a>分頁

在四個已啟用的分類排序中，每一個皆可支援分頁。 目前的頁面大小為 10。

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


### <a name="transform"></a>轉換

#### <a name="filteringordering"></a>篩選/排序

下表說明這些選項如何套用至[轉換](https://docs.microsoft.com/rest/api/media/transforms)屬性： 

| 名稱    | Filter                        | 順序 |
|---------|-------------------------------|-------|
| name                    | eq            | 遞增和遞減|
| properties.created      | gt、ge、lt、le| 遞增和遞減|
| properties.lastModified | gt、ge、lt、le | 遞增和遞減|

## <a name="next-steps"></a>後續步驟

[串流處理檔案](stream-files-dotnet-quickstart.md)
