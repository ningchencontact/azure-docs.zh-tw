---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 9647cdd584b53f581f46f728ca2d08f9a113ce92
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198916"
---
## <a name="before-you-begin"></a>開始之前

若要完成本文中的範例，您必須具有一般化虛擬機器的現有受控映像。 如需詳細資訊，請參閱[教學課程：使用 Azure CLI 2.0 建立 Azure VM 的自訂映像](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)。 如果受控映像會包含資料磁碟，資料磁碟大小不得超過 1 TB。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="create-an-image-gallery"></a>建立映像資源庫 

映像資源庫是用於啟用映像共用的主要資源。 資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 組件庫名稱不能包含連字號。   資源庫名稱在您的訂用帳戶內必須是唯一的。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 建立映像資源庫。 下列範例會在 *myGalleryRG* 中建立名為 *myshare* 的資源庫。

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>建立映像定義

映像定義會建立映像的邏輯群組。 它們用來管理在其中建立映像版本的相關資訊。 映像定義名稱可包含大寫或小寫字母、 數字、 點、 連字號和句號。 如需您可以指定映像定義之值的詳細資訊，請參閱[映像定義](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)，在資源庫中建立初始映像定義。

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>建立映像版本 

使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) 建立所需的映像版本。 您必須傳入受控映像的識別碼，以作為建立映像版本的基準。 您可以使用 [az image list](/cli/azure/image?view#az-image-list) 來取得資源群組中映像的相關資訊。 

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式:*MajorVersion*。*MinorVersion*。*修補程式*。

在此範例中，我們的映像的版本是*1.0.0*我們會建立在 2 個複本*美國中西部*區域、 在 1 個複本*美國中南部*區域和 1中的複本*美國東部 2*區域。


```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> 您需要等待映像版本，才能完全完成建置和複寫之前，您可以使用相同的受控映像來建立另一個映像版本。
>
> 您也可以儲存在程式映像版本[區域備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)藉由新增`--storage-account-type standard_zrs`當您建立的映像版本。
>
