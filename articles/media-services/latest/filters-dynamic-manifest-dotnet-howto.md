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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 23e83b98288f9ac1fe23e01b9a91d81daa3b0f47
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632376"
---
# <a name="create-filters-with-media-services-net-sdk"></a>使用媒體服務 .NET SDK 建立篩選器

當提供您的內容給客戶 (串流處理即時活動或點播視訊) 時，您的用戶端可能需要比預設資產資訊清單檔中所述還大的彈性。 Azure 媒體服務可讓您為您的內容定義帳戶篩選器與資產篩選器。 如需詳細資訊，請參閱 [篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。

本主題說明如何使用媒體服務 .NET SDK 為點播視訊資產定義篩選器，以及如何建立[帳戶篩選器](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet)與[資產篩選器](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)。 

## <a name="prerequisites"></a>必要條件 

- 檢閱[篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。
- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- 取得所需資訊來[存取 API](access-api-cli-how-to.md)
- 檢閱[使用 Azure 媒體服務執行上傳、編碼和串流](stream-files-tutorial-with-api.md)，以了解如何[開始使用 .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>定義篩選器  

在 .NET 中，您可以使用 [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) 和 [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 類別來設定曲目選取範圍。 

下列程式碼會定義篩選器，其中包括語言為英文且具有 EC-3 的任何音訊曲目，以及位元速率介於 0-1000000 範圍之間的任何視訊曲目。

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Language, "en-us", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
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

## <a name="next-steps"></a>後續步驟

[串流影片](stream-files-tutorial-with-api.md) 


