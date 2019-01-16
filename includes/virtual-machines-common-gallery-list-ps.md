---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f7539ed5083a386ef05134bea36426f4a360afad
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192715"
---
## <a name="shared-image-management"></a>共用映像管理 

以下範例是一些常見的管理作業，以及如何使用 PowerShell 完成這些作業。

依名稱列出所有的資源庫。

```azurepowershell-interactive
$galleries = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

依名稱列出所有的映像定義。

```azurepowershell-interactive
$imageDefinitions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

依名稱列出所有的映像版本。

```azurepowershell-interactive
$imageVersions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

刪除映像版本。 此範例會刪除名為 *1.0.0* 的映像版本。

```azurepowershell-interactive
Remove-AzureRmGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





