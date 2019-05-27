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
ms.openlocfilehash: 82187b05a398c066f9da94c57cbe8a59a6ba3275
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145765"
---
## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。


## <a name="get-the-managed-image"></a>取得受控映像

您可以使用 [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage) 來查看資源群組中的可用映像清單。 當您知道映像名稱和其所位於的資源群組之後，您可以再次使用 `Get-AzImage` 來取得該映像物件，並將它儲存在變數中以供日後使用。 此範例會從 "myResourceGroup" 資源群組取得名為 *myImage* 的映象，然後將它指派至 *$managedImage* 變數。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 組件庫名稱不能包含連字號。 資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) 建立映像資源庫。 下列範例會在 *myGalleryRG* 資源群組中建立名為 *myGallery* 的資源庫。

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>建立映像定義 

映像定義會建立映像的邏輯群組。 它們用來管理在其中建立映像版本的相關資訊。 映像定義名稱可包含大寫或小寫字母、 數字、 點、 連字號和句號。 如需您可以指定映像定義之值的詳細資訊，請參閱[映像定義](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)。

建立映像定義 using[新增 AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)。 在此範例中，資源庫映像名為 *myGalleryImage*。

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>建立映像版本

從受控映像，使用建立映像版本[新增 AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式:*MajorVersion*。*MinorVersion*。*修補程式*。

在此範例中，映像版本為 *1.0.0*，且它會被複寫到「美國中西部」和「美國中南部」資料中心。 當選擇針對複寫的目標區域，請記住，您也必須包含*來源*做為複寫的目標區域。


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

將映像複寫到所有目標區域可能需要花上一點時間，因此我們已建立可追蹤進度的作業。 若要查看作業的進度，請輸入 `$job.State`。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 您需要等待映像版本，才能完全完成建置和複寫之前，您可以使用相同的受控映像來建立另一個映像版本。 
>
> 您也可以儲存在程式映像版本[區域備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)藉由新增`-StorageAccountType Standard_ZRS`當您建立的映像版本。
>
