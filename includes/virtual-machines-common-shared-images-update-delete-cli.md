---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b34d37fa79ccb8344fdacd99877403d61ba5f5c2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138932"
---
## <a name="update-resources"></a>更新資源

有一些限制可以更新項目。 可以更新下列項目： 

共用映像庫：
- 描述

映像定義：
- 建議的 vCPU
- 建議的記憶體
- 描述
- 生命週期結束日期

映像版本：
- 區域複本計數
- 目標區域
- 從最新版排除
- 生命週期結束日期

更新資源庫使用的描述 ([az sig 更新](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update)。 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


更新的映像定義使用描述[az sig 映像定義更新](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)。

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

更新映像版本，才能新增要複寫到使用的區域[az sig 映像版本更新](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)。 這項變更將需要一段時間，因為映像會複寫至新的區域。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>刪除資源

您必須先刪除映像版本，以相反順序中，刪除資源。 刪除所有的映像版本之後，您可以刪除映像定義。 刪除所有的映像定義之後，您可以刪除資源庫。 

刪除使用您建立映像版本[az sig 映像版本刪除](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete)。

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

刪除映像定義使用[az sig 映像定義刪除](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete)。

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


刪除映像資源庫使用[az sig 刪除](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete)。

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```