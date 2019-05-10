---
title: 使用映像庫中的 Azure 映像產生器，適用於 Linux 虛擬機器 （預覽）
description: 使用 Azure 映像產生器及共用映像庫中建立 Linux 映像。
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: d29fa8700cb1f530cfe85f0bdf6852d75ec1613e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508160"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>預覽：建立 Linux 映像，並將它散發給 共用映像庫 

本文說明如何使用 Azure 映像產生器來建立映像版本[共用映像庫](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)，然後將全域映像。


我們將使用範例.json 範本來設定映像。 .Json 檔案，我們會使用已正式推出： [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json)。 

若要發佈至共用映像庫映像，此範本會使用[sharedImage](image-builder-json.md#distribute-sharedimage)的值為`distribute`範本區段。

> [!IMPORTANT]
> Azure 映像產生器目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>註冊功能
若要使用 Azure 映像產生器，在預覽期間，您需要註冊新的功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

檢查功能註冊狀態。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

請檢查您的註冊。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

如果他們未執行說已註冊，執行下列命令：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>設定變數和權限 

我們將使用資訊的某些部分重複，因此我們將建立一些變數，以儲存該資訊。

預覽版本中，映像產生器只支援作為來源的受控映像相同的資源群組中建立自訂映像。 更新資源群組名稱，在此範例中是相同的資源群組，為您的來源受控映像。

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

建立變數，針對您的訂用帳戶識別碼。 您可以取得此使用`az account show | grep id`。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

建立資源群組。

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


提供 Azure 映像產生器來建立該資源群組中的資源的權限。 `--assignee`值是映像產生器服務的應用程式註冊識別碼。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>建立映像定義和資源庫

建立映像庫。 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

建立映像定義。

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>下載及設定.json

下載.json 範本並設定您的變數。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>建立映像版本

接下來的部分就會建立資源庫映像版本。 

映像的組態提交至 Azure 映像產生器服務。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

啟動映像組建。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

建立映像，並將它複寫至這兩個區域可能需要一段時間。 等候完成再繼續進行建立 VM，此組件。


## <a name="create-the-vm"></a>建立 VM

從 Azure 映像產生器所建立的映像版本建立的 VM。

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

透過 ssh 連線到 VM。

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

您應該會看到已自訂為映像*在一天的訊息*只要建立您的 SSH 連線 ！

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>清除資源

如果您想要現在，請嘗試重新自訂映像版本來建立相同的映像的新版本，略過接下來的步驟，請繼續進行[使用 Azure 映像產生器，以建立另一個映像版本](image-builder-gallery-update-image-version.md)。


這會刪除已建立，以及所有其他資源檔案的映像。 請確定您已完成這項部署之前刪除的資源。

當刪除映像資源庫資源，您需要刪除所有映像版本，才能刪除映像定義用來建立它們。 若要刪除資源庫，必須先刪除所有資源庫中的映像定義。

刪除映像產生器範本。

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

取得映像產生器所建立的映像版本，這永遠會啟動`0.`，然後再刪除映像版本

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


刪除映像的定義。

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

刪除資源庫。

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

刪除資源群組。

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>後續步驟

深入了解[Azure 共用映像資源庫](shared-image-galleries.md)。