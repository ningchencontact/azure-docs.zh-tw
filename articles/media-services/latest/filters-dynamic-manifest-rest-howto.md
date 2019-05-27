---
title: 使用 Azure 媒體服務 REST API 建立篩選器 | Microsoft Docs
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
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 69399513291a47f7109003e825052314f447125a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002334"
---
# <a name="creating-filters-with-media-services-rest-api"></a>使用媒體服務 REST API 建立篩選器

當提供您的內容給客戶 (串流即時活動或點播視訊) 時，您的用戶端可能需要比預測資產資訊清單檔案中所述還大的彈性。 Azure 媒體服務可讓您為您的內容定義帳戶篩選器與資產篩選器。 如需詳細資訊，請參閱 <<c0> [ 篩選條件](filters-concept.md)並[動態資訊清單](filters-dynamic-manifest-overview.md)。

此主題說明如何為點播視訊資產建立篩選器，以及如何使用 REST API 來建立[帳戶篩選器](https://docs.microsoft.com/rest/api/media/accountfilters) \(英文\) 與[資產篩選器](https://docs.microsoft.com/rest/api/media/assetfilters) \(英文\)。 

> [!NOTE]
> 請務必檢閱[presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>必要條件 

若要完成此主題中所述的步驟，您必須：

- 檢閱[篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。

    請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 

## <a name="define-a-filter"></a>定義篩選器  

下面提 **要求本文**範例，它定義新增到資訊清單的曲目選取條件。 此篩選器包括語言為具有 EC-3 的任何音訊曲目，以及位元速率介於 0-1000000 範圍之間的任何視訊曲目。

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>建立帳戶篩選器

在您下載的 Postman 集合中，選取 [帳戶篩選器]->[建立或更新帳戶篩選器]。

**PUT** HTTP 要求方法類似：

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

選取 [本文] 索引標籤，並貼上您[稍早定義的 json 程式碼](#define-a-filter)。

選取 [傳送]。 

篩選器已建立。

如需詳細資訊，請參閱 [建立或更新](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate) \(英文\)。 此外，也請參閱[篩選器的 JSON 範例](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter)。

## <a name="create-asset-filters"></a>建立資產篩選器  

在您下載的 "Media Services v3" Postman 集合中，選取 [資產]->[建立或更新資產篩選器]。

**PUT** HTTP 要求方法類似：

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

選取 [本文] 索引標籤，並貼上您[稍早定義的 json 程式碼](#define-a-filter)。

選取 [傳送]。 

資產篩選器已建立。

如需有關如何建立或更新資產篩選器的詳細資訊，請參閱[建立或更新](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate) \(英文\)。 此外，也請參閱[篩選器的 JSON 範例](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter)。 

## <a name="next-steps"></a>後續步驟

[串流影片](stream-files-tutorial-with-rest.md) 
