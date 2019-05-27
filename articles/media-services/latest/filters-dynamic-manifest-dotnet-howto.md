---
title: 使用 Azure 媒體服務 .NET SDK 建立篩選器
description: 本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。 媒體服務會建立動態資訊清單來完成此選擇性資料流。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 0d85bad5328c9d28c8690b389fca18731ea17644
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002366"
---
# <a name="create-filters-with-media-services-net-sdk"></a>使用媒體服務 .NET SDK 建立篩選器

當提供您的內容給客戶 (串流處理即時活動或點播視訊) 時，您的用戶端可能需要比預設資產資訊清單檔中所述還大的彈性。 Azure 媒體服務可讓您為您的內容定義帳戶篩選器與資產篩選器。 如需詳細資訊，請參閱 <<c0> [ 篩選條件](filters-concept.md)並[動態資訊清單](filters-dynamic-manifest-overview.md)。

本主題說明如何使用媒體服務 .NET SDK 為點播視訊資產定義篩選器，以及如何建立[帳戶篩選器](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet)與[資產篩選器](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)。 

> [!NOTE]
> 請務必檢閱[presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>必要條件 

- 檢閱[篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。
- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- 取得所需資訊來[存取 API](access-api-cli-how-to.md)
- 檢閱[使用 Azure 媒體服務執行上傳、編碼和串流](stream-files-tutorial-with-api.md)，以了解如何[開始使用 .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>定義篩選器  

在 .NET 中，您可以使用 [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) 和 [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 類別來設定曲目選取範圍。 

下列程式碼會定義篩選，其中包括具有 EC-3 的任何音訊曲目，以及位元速率介於 0-1000000 範圍之間的任何視訊曲目。

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>建立帳戶篩選器

下列程式碼示範如何使用 .NET 來建立帳戶篩選器，其中包括[以上定義](#define-a-filter)的所有曲目選取範圍。 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>建立資產篩選器

下列程式碼示範如何使用 .NET 來建立資產篩選器，其中包括[以上定義](#define-a-filter)的所有曲目選取範圍。 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>串流定位器相關聯的篩選器

您可以指定資產或帳戶會套用到您的串流定位器的篩選器清單。 [動態封裝程式 （串流端點）](dynamic-packaging-overview.md)適用於這份清單，以及那些用戶端在 URL 中所指定的篩選條件。 這個組合會產生[動態資訊清單](filters-dynamic-manifest-overview.md)，根據在 URL 中的篩選器 + 串流定位器指定的篩選條件。 我們建議您使用這項功能，如果您想要套用篩選，但不是想要公開 （expose） 在 URL 中的篩選條件名稱。

下列C#程式碼示範如何建立串流定位器，並指定`StreamingLocator.Filters`。 這是選擇性屬性接受`IList<string>`的篩選器的名稱。

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>使用篩選器的 Stream

定義篩選條件後，您的用戶端就可以在串流 URL 中使用它們。 篩選器可以套用至自適性串流通訊協定：Apple HTTP 即時串流 (HLS)、MPEG-DASH 和 Smooth Streaming。

下表顯示包含篩選器之 URL 的一些範例：

|Protocol|範例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>後續步驟

[串流影片](stream-files-tutorial-with-api.md) 


