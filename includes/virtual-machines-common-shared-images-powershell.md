---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 91889971e1ab8a9ea8341f6bc57735d973ea0e89
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116113"
---
## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。


## <a name="preview-register-the-feature"></a>預覽：註冊功能

共用映像資源庫處於預覽狀態，但您必須先註冊該功能，才能使用它。 若要註冊共用映像資源庫功能：

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>取得受控映像

您可以使用 [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage) 來查看資源群組中的可用映像清單。 當您知道映像名稱和其所位於的資源群組之後，您可以再次使用 `Get-AzImage` 來取得該映像物件，並將它儲存在變數中以供日後使用。 此範例會從 "myResourceGroup" 資源群組取得名為 *myImage* 的映象，然後將它指派至 *$managedImage* 變數。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱在您的訂用帳戶內必須是唯一的。 使用 [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) 建立映像資源庫。 下列範例會在 *myGalleryRG* 資源群組中建立名為 *myGallery* 的資源庫。

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

使用 [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 建立資源庫映像定義。 在此範例中，資源庫映像名為 *myGalleryImage*。

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
### <a name="using-publisher-offer-and-sku"></a>使用發行者、 供應項目和 SKU 
客戶實作共用的映像，計劃**近期版本中**，您將能夠使用您個人的已定義 **-發行者**， **-提供**和 **-Sku**值，以尋找並指定映像定義，然後使用從相符的最新映像版本建立 VM 映像的定義。 例如，以下是三個映像定義和其值：

|映像定義|發行者|供應項目|SKU|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|測試|standardOffer|testSku|

這三個映像定義都擁有唯一的值組。 不同的映像版本之間可以擁有一或兩個相同的值，但不能三個值都相同。 **近期版本中**，您可以結合這些值，以要求特定的映像的最新版本。 **這不適用於目前版本**，但會在未來使用。 發行時，使用下列語法應該用來設定來源映像*myImage1*從上述資料表。

```powershell
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

這是類似於您如何目前指定使用發行者、 供應項目和 SKU [Azure Marketplace 映像](../articles/virtual-machines/windows/cli-ps-findimage.md)以取得最新版的 Marketplace 映像。 因此，每個映像定義都需要有由這些值組成的唯一值組。  

## <a name="create-an-image-version"></a>建立映像版本

使用 [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 從受控映像建立映像版本。 在此範例中，映像版本為 *1.0.0*，且它會被複寫到「美國中西部」和「美國中南部」資料中心。


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

