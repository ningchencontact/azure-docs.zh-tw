---
title: 建立虛擬機器映像，並使用使用者指派的受控身分識別存取 Azure 儲存體 （預覽） 中的檔案
description: 建立虛擬機器映像使用 Azure 映像產生器，可存取使用使用者指派給受控身分識別的 Azure 儲存體中儲存的檔案。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: d10ee8c1af85de5eb79cd4a4af6882c7a8f084f1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159551"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>建立映像，並使用使用者指派的受控身分識別存取 Azure 儲存體中的檔案 

Azure 映像產生器支援使用指令碼，或從多個位置，例如 GitHub 和 Azure 儲存體等複製的檔案。若要使用這些範本，他們必須已從外部存取 Azure 映像產生器，但您無法保護使用 SAS 權杖的 Azure 儲存體 blob。

這篇文章示範如何建立使用 Azure VM 映像產生器，服務將在其中使用自訂映像[指派使用者給受控身分識別](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)來存取 Azure 儲存體中的檔案之映像自訂項目，不含您不必進行公開存取，或設定的 SAS 權杖的檔案。

在下列範例中，您將建立兩個資源群組，其中將會用於自訂的映像，而且另會裝載 Azure 儲存體帳戶，包含指令碼檔。 這會模擬真實生活案例中，您可能會在其中有組建成品或在不同的儲存體帳戶中，映像產生器之外的映像檔案。 您將建立使用者指派的身分識別，則指令碼檔案中，讀取權限的授與，但不是會將任何的公用存取該檔案。 然後，您會使用 Shell 自訂下載並執行該指令碼，從儲存體帳戶。


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


## <a name="create-a-resource-group"></a>建立資源群組

我們將使用資訊的某些部分重複，因此我們將建立一些變數，以儲存該資訊。


```azurecli-interactive
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

建立變數，針對您的訂用帳戶識別碼。 您可以取得此使用`az account show | grep id`。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

建立映像和指令碼儲存體的資源群組。

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


建立儲存體，並將從 GitHub 的範例指令碼複製到其中。

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



提供映像產生器來建立映像的資源群組中的資源的權限。 `--assignee`值是映像產生器服務的應用程式註冊識別碼。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>建立使用者指派給受控身分識別

建立身分識別，並指派指令碼儲存體帳戶的權限。 如需詳細資訊，請參閱 <<c0> [ 使用者所指派的受控身分識別](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)。

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>修改範例

下載範例.json 檔案，並使用您所建立的變數加以設定。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>建立映像

映像的組態提交至 Azure 映像產生器服務。

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

啟動映像組建。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

等待建置完成。 這可能需要大約 15 分鐘。

## <a name="create-a-vm"></a>建立 VM

從映像建立 VM。 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

在建立 VM 之後，啟動 VM 的 SSH 工作階段。

```azurecli-interactive
ssh aibuser@<publicIp>
```

您應該會看到映像已自訂為一天，一旦建立您的 SSH 連線的訊息 ！

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>清除

當您完成時，您就可以刪除資源，如果不再需要。

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>後續步驟

如果您使用 Azure 映像產生器的任何問題，請參閱[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)。