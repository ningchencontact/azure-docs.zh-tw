---
title: Azure 媒體服務中的資產 | Microsoft Docs
description: 本文解釋資產是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 76ed74f2df62d478b83e109a492977ec2d580198
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305084"
---
# <a name="assets"></a>Assets

**資產** 包含數位檔案 (包括視訊、音訊、影像、縮圖集合、文字播放軌和隱藏式輔助字幕檔案)，以及這些檔案的相關中繼資料。 將數位檔案上傳到資產之後，可以用於媒體服務編碼和串流工作流程。

資產會對應到 [Azure 儲存體帳戶](storage-account-concept.md)中的 blob 容器，且資產中的檔案會儲存為該容器中的區塊 Blob。 您可以使用儲存體 SDK 用戶端，與容器中的資產檔案互動。

當帳戶使用一般用途 v2 (GPv2) 儲存體時，Azure 媒體服務支援 Blob 層。 您可以利用 GPv2，將檔案移至非經常性存取儲存體或冷儲存體。 來源檔案不再需要時，很適合放到冷儲存體中保管 (例如，在它們編碼之後)。

在媒體服務 v3 中，可以從資產或 HTTP(s) URL 建立工作輸入。 若要建立一個資產而且可以當作工作的輸入，請參閱[從本機檔案建立工作輸入](job-input-from-local-file-how-to.md)。

此外，閱讀有關[媒體服務 中的儲存體帳戶](storage-account-concept.md)和[轉換與工作](transform-concept.md)。

## <a name="asset-definition"></a>資產定義

下表顯示資產屬性並提供其定義。

|Name|類型|說明|
|---|---|---|
|id|字串|資源的完整資源識別碼。|
|name|字串|資源名稱。|
|properties.alternateId |字串|資產的替代 ID。|
|properties.assetId |字串|資產識別碼。|
|properties.container |字串|資產 Blob 容器的名稱。|
|properties.created |字串|資產的建立日期。|
|properties.description |字串|資產描述。|
|properties.lastModified |字串|資產的上次修改日期。|
|properties.storageAccountName |字串|儲存體帳戶的名稱。|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |資產加密格式。 None 或 MediaStorageEncryption 其中一個。|
|type|字串|資源類型。|

如需完整定義，請參閱[資產](https://docs.microsoft.com/rest/api/media/assets)。

## <a name="filtering-ordering-and-paging-support"></a>篩選、排序和分頁支援

媒體服務支援下列資產的 OData 查詢選項： 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>篩選/排序

下表顯示這些選項可如何套用至資產屬性： 

|Name|Filter|順序|
|---|---|---|
|id|支援：<br/>Equals<br/>大於<br/>小於|支援：<br/>遞增<br/>遞減|
|name|||
|properties.alternateId |支援：<br/>Equals||
|properties.assetId |支援：<br/>Equals||
|properties.container |||
|properties.created|支援：<br/>Equals<br/>大於<br/>小於|支援：<br/>遞增<br/>遞減|
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

### <a name="pagination"></a>分頁

在四個已啟用的分類排序中，每一個皆可支援分頁。 

如果查詢回應包含許多 (目前超過 1000 個) 項目，服務會傳回 "@odata.nextLink" 屬性，以取得下一頁的結果。 這可用來逐頁查看整個結果集。 使用者無法設定頁面大小。 

如果逐頁查看集合時，有資產建立或刪除，則所做的變更會反映在傳回的結果中 (如果這些變更屬於尚未下載的集合)。 

下列 C# 範例會示範如何列舉帳戶中的所有資產。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

如需其他範例，請參閱[資產 - 清單](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [串流處理檔案](stream-files-dotnet-quickstart.md)
