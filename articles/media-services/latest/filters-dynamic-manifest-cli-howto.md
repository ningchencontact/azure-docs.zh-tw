---
title: 使用 CLI 搭配 Azure 媒體服務建立篩選器 | Microsoft Docs
description: 本主題顯示如何使用 CLI 搭配媒體服務建立篩選器。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: fd581d1dfea850e925909df59f2d4fdd421053fb
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494378"
---
# <a name="creating-filters-with-cli"></a>使用 CLI 建立篩選器 

當您提供內容給客戶 (串流即時活動或點播視訊) 時，用戶端需要的彈性可能比預設資產資訊清單檔案中所述的還多。 Azure 媒體服務可讓您為您的內容定義帳戶篩選器與資產篩選器。 

如需詳細的這個功能和使用的案例的詳細說明，請參閱[動態資訊清單](filters-dynamic-manifest-overview.md)並[篩選](filters-concept.md)。

此主題說明如何為點播視訊資產設定篩選器，以及如何使用適用於第三版媒體服務的 CLI 來建立[帳戶篩選器](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)與[資產篩選器](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)。 

> [!NOTE]
> 請務必檢閱[presentationTimeRange](filters-concept.md#presentationtimerange)。

## <a name="prerequisites"></a>必要條件 

- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>定義篩選器 

下列範例定義新增到最終資訊清單的曲目選取條件。 此篩選包含格式為 EC-3 的所有音軌，以及位元速率介於 0-1000000 的所有影像軌。

> [!TIP]
> 如果您計劃來定義**篩選器**在 REST 中，請注意，您需要包含 「 屬性 」 的包裝函式的 JSON 物件。  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>建立帳戶篩選器

下列 [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) 命令可建立含[稍早定義](#define-a-filter)之曲目選取條件的帳戶篩選器。 

此命令可讓您傳遞包含代表軌道選取之 JSON 的選用 `--tracks` 參數。  使用 @{file} 從檔案載入 JSON。 若您在本機使用 Azure CLI，請指定完整檔案路徑：

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

此外，也請參閱[篩選器的 JSON 範例](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter)。

## <a name="create-asset-filters"></a>建立資產篩選器

下列 [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) 命令可建立含[稍早定義](#define-a-filter)之曲目選取條件的資產篩選器。 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

此外，也請參閱[篩選器的 JSON 範例](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter)。


## <a name="associate-filters-with-streaming-locator"></a>串流定位器相關聯的篩選器

您可以指定資產或帳戶會套用到您的串流定位器的篩選器清單。 [動態封裝程式 （串流端點）](dynamic-packaging-overview.md)適用於這份清單，以及那些用戶端在 URL 中所指定的篩選條件。 這個組合會產生[動態資訊清單](filters-dynamic-manifest-overview.md)，根據在 URL 中的篩選器 + 串流定位器指定的篩選條件。 我們建議您使用這項功能，如果您想要套用篩選，但不是想要公開 （expose） 在 URL 中的篩選條件名稱。

下列的 CLI 程式碼示範如何建立串流定位器，並指定`filters`。 這是選擇性屬性，可為以空格分隔的清單資產篩選器的名稱及/或帳戶篩選器名稱。

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>使用篩選器的 Stream

定義篩選條件後，您的用戶端就可以在串流 URL 中使用它們。 篩選器可以套用至自適性串流通訊協定：Apple HTTP 即時串流 (HLS)、MPEG-DASH 和 Smooth Streaming。

下表顯示包含篩選器之 URL 的一些範例：

|Protocol|範例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>後續步驟

[串流影片](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
