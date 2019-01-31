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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ba3de32f4ec3b9f6faf1d5a51da9c1c91e4a2e4
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099301"
---
# <a name="creating-filters-with-cli"></a>使用 CLI 建立篩選器 

當您提供內容給客戶 (串流即時活動或點播視訊) 時，用戶端需要的彈性可能比預設資產資訊清單檔案中所述的還多。 Azure 媒體服務可讓您為您的內容定義帳戶篩選器與資產篩選器。 如需詳細資訊，請參閱 [篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。

此主題說明如何為點播視訊資產設定篩選器，以及如何使用適用於第三版媒體服務的 CLI 來建立[帳戶篩選器](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)與[資產篩選器](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)。 

## <a name="prerequisites"></a>必要條件 

- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- 檢閱[篩選器與動態資訊清單](filters-dynamic-manifest-overview.md)。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>定義篩選器 

下列範例定義新增到最終資訊清單的曲目選取條件。 此篩選包含格式為 EC-3 的所有音軌，以及位元速率介於 0-1000000 的所有影像軌。

篩選器是以 REST 來定義，其包含「屬性」的包裝函式 JSON 物件。  

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

## <a name="next-step"></a>後續步驟

[串流影片](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>另請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
