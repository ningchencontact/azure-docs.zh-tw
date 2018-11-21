---
title: 調整媒體保留單元 - Azure | Microsoft Docs
description: 本主題為透過 Azure 媒體服務調整媒體處理的概觀。
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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: db1915f23c33b5cc0d504f8fcc21b9533228247f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634391"
---
# <a name="scaling-media-processing"></a>調整媒體處理

Azure 媒體服務可讓您藉由管理媒體保留單元 (MRU)，在帳戶中調整媒體處理方式。 如需詳細概觀，請參閱[調整媒體處理](../previous/media-services-scale-media-processing-overview.md)。 本文說明如何使用[媒體服務 v3 CLI](https://aka.ms/ams-v3-cli-ref) 來調整 MRU。

> [!IMPORTANT]
> 請檢閱[本節](#considerations)中說明的考量。  
> 
>

## <a name="prerequisites"></a>必要條件 

- 在本機安裝和使用 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，並非所有[媒體服務 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中運作。 建議在本機使用 CLI。

- [建立媒體服務帳戶](create-account-cli-how-to.md)。

## <a name="scale-media-reserved-units-with-cli"></a>使用 CLI 調整媒體保留單元

下列 [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) 命令會使用**計數**和**類型**參數在 "amsaccount" 帳戶上設定媒體保留單元。

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="considerations"></a>考量

- 針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 MRU 佈建帳戶。
- 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

## <a name="billing"></a>計費

您須根據在帳戶中佈建 MRU 的數目、類型和時間量支付費用。 無論您是否執行作業，都需付費。 如需詳細說明，請參閱[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)頁面的＜常見問題集＞一節。   

## <a name="next-step"></a>後續步驟

[分析影片](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>另請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
