---
title: 使用 Azure 映像產生器 （預覽） 建立 Linux VM
description: 使用 Azure 映像產生器中建立 Linux VM。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159506"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>預覽：使用 Azure 映像產生器中建立 Linux VM

本文說明如何建立自訂的 Linux 映像使用 Azure 映像產生器和 Azure CLI。 這篇文章中的範例使用三種不同[客](image-builder-json.md#properties-customize)的自訂映像：

- Shell (ScriptUri)-下載並執行[殼層指令碼](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)。
- Shell （內嵌）-執行特定命令。 在此範例中，內嵌命令會包含建立目錄和更新的作業系統。
- 檔案-複本[檔案，從 GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)到 VM 上的目錄。

我們將使用範例.json 範本來設定映像。 .Json 檔案，我們會使用已正式推出： [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json)。 

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
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

建立變數，針對您的訂用帳戶識別碼。 您可以取得此使用`az account show | grep id`。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

建立資源群組。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```


提供映像產生器來建立該資源群組中的資源的權限。 `--assignee`值是映像產生器服務的應用程式註冊識別碼。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-json-example"></a>下載 json 範例

下載範例.json 檔案，並使用您所建立的變數加以設定。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>建立映像
映像的組態提交至 VM 映像產生器服務

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

啟動映像組建。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

等待建置完成。 這可能需要大約 15 分鐘。


## <a name="create-the-vm"></a>建立 VM

建立使用您所建置的映像的 VM。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

建立 VM 的輸出中取得的 IP 位址，並使用它來透過 ssh 連線到 VM。

```azurecli-interactive
ssh azureuser@<pubIp>
```

您應該會看到映像已自訂為一天，一旦建立您的 SSH 連線的訊息 ！

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

型別`exit`完成時關閉 SSH 連線。

## <a name="check-the-source"></a>請檢查來源

在 [屬性] 中的映像產生器範本中，您會看到的來源映像、 自訂指令碼執行，並發佈它的位置。

```azurecli-interactive
cat helloImageTemplateLinux.json
```

如需詳細此.json 檔案的相關資訊，請參閱[映像產生器範本參考](image-builder-json.md)

## <a name="clean-up"></a>清除

當您完成時刪除的資源。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>後續步驟

若要深入了解這篇文章中所使用的.json 檔案的元件，請參閱[映像產生器範本參照](image-builder-json.md)。