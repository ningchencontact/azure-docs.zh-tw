---
title: 使用 Azure 映像產生器 （預覽） 建立 Windows VM
description: 使用 Azure 映像產生器中建立 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: fec6d83870e20b7622f37c52847803d4f03cbba5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722669"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>預覽：使用 Azure 映像產生器中建立 Windows VM

這篇文章是要說明如何建立自訂的 Windows 映像，使用 Azure VM 映像產生器。 這篇文章中的範例使用三種不同[客](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize)的自訂映像：
- PowerShell (ScriptUri)-請下載並執行[PowerShell 指令碼](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)。
- Windows 重新啟動-重新啟動 VM。
- PowerShell （內嵌）-執行特定命令。 在此範例中，它會建立一個目錄上 VM 使用`mkdir c:\\buildActions`。
- 檔案-檔案複製到 VM 上的 GitHub。 此範例會複製[index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)到`c:\buildArtifacts\index.html`在 VM 上。

我們將使用範例.json 範本來設定映像。 .Json 檔案，我們會使用已正式推出： [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json)。 


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
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
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
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>建立映像

映像的組態提交至 VM 映像產生器服務

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

啟動映像組建。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

等待建置完成。 這可能需要大約 15 分鐘。

## <a name="create-the-vm"></a>建立 VM

建立使用您所建置的映像的 VM。 取代 *<password>* 與您自己的密碼`aibuser`在 VM 上。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>確認自訂

建立遠端桌面連線使用的使用者名稱和密碼建立 VM 時所設定的 vm。 在 vm 中，開啟命令提示字元並輸入：

```console
dir c:\
```

您應該會看到這些映像自訂期間建立的兩個目錄：
- buildActions
- buildArtifacts

## <a name="clean-up"></a>清除

當您完成時刪除的資源。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>後續步驟

若要深入了解這篇文章中所使用的.json 檔案的元件，請參閱[映像產生器範本參考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

