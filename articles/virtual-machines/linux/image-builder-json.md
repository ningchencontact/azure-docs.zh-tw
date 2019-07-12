---
title: 建立 Azure 映像產生器範本 （預覽）
description: 了解如何建立範本以使用 Azure 映像產生器。
author: cynthn
ms.author: cynthn
ms.date: 05/10/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 16ad2a93c9ff035166a738edba40c99075a6e7ba
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671468"
---
# <a name="preview-create-an-azure-image-builder-template"></a>預覽：建立 Azure 映像產生器範本 

Azure 映像產生器會將資訊傳遞到映像產生器服務使用的.json 檔案。 這篇文章中我們將介紹的區段的 json 檔案中，因此您也可以建置自己。 若要查看完整的.json 檔案的範例，請參閱[Azure 映像產生器 GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)。

這是基本的範本格式：

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>類型和 API 版本

`type`是資源類型，它必須是`"Microsoft.VirtualMachineImages/imageTemplates"`。 `apiVersion`會變更一段時間的 API 變更，但應該是`"2019-05-01-preview"`preview。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

這個位置是即將建立的自訂映像的區域。 對於映像產生器 預覽中，支援下列區域：

- East US
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>（選擇性） 而定

此選擇性區段可用來確保相依性都會完成再繼續進行。 

```json
    "dependsOn": [],
```

如需詳細資訊，請參閱 <<c0> [ 定義的資源相依性](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)。

## <a name="identity"></a>身分識別
根據預設，映像產生器支援使用指令碼，或從多個位置，例如 GitHub 和 Azure 儲存體複製的檔案。 若要使用這些範本，它們必須可公開存取。

您也可以使用 Azure User-Assigned 受控身分識別，由您定義允許映像產生器存取 Azure 儲存體，只要身分識別授與最低的版本 '儲存體 Blob 資料讀者' 的 Azure 儲存體帳戶。 這表示您不需要儲存體 blob 從外部存取，或安裝程式的 SAS 權杖。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

如需完整範例，請參閱 <<c0> [ 使用 Azure User-Assigned 受控身分識別存取 Azure 儲存體中的檔案](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

映像產生器支援使用者指派身分識別: • 支援單一身分識別，只有 • 不支援自訂網域名稱

若要進一步了解，請參閱[什麼是適用於 Azure 資源管理的身分識別？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
如需有關如何部署這項功能的詳細資訊，請參閱 <<c0> [ 設定受管理身分識別使用 Azure CLI 在 Azure VM 上的 Azure 資源的](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)。

## <a name="properties-source"></a>屬性： 來源

`source`區段包含將映像產生器所使用的來源映像的相關資訊。

API 要求 'SourceType' 定義映像組建的來源，目前有三種類型：
- ISO-RHEL ISO 來源時，請使用此。
- PlatformImage-指出來源映像的 Marketplace 映像。
- ManagedImage-從一般的受控映像啟動時，請使用此。
- SharedImageVersion-這您使用的映像版本共用映像庫中做為來源時使用。

### <a name="iso-source"></a>ISO 來源

Azure 映像產生器只支援使用已發行之的 Red Hat Enterprise Linux 7.x 二進位 DVD Iso、 預覽。 映像產生器支援：
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

若要取得`sourceURI`並`sha256Checksum`值，請移至`https://access.redhat.com/downloads`然後選取 產品**Red Hat Enterprise Linux**，和支援的版本。 

在這份**安裝程式與適用於 Red Hat Enterprise Linux Server 映像**，您需要複製 Red Hat Enterprise Linux 7.x 二進位 DVD 及總和檢查碼的連結。

> [!NOTE]
> 連結的存取權杖重新整理頻率，因此每次您想要提交範本，您必須檢查如果 RH 連結位址已變更。
 
### <a name="platformimage-source"></a>PlatformImage 來源 
Azure 映像產生器支援下列 Azure Marketplace 映像：
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


此處的屬性都相同，用來建立 VM，使用 AZ CLI，執行以下以取得的屬性： 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> 版本不可為 'latest'，您必須使用上述命令來取得版本號碼。 

### <a name="managedimage-source"></a>ManagedImage 來源

將來源影像設為現有的受控映像的一般化的 VHD 或 VM。 來源受控映像必須是受支援的作業系統，而且是與您的 Azure 映像產生器範本相同的區域。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`應該是受管理的映像的 ResourceId。 使用`az image list`列出可用的映像。


### <a name="sharedimageversion-source"></a>SharedImageVersion 來源
設定現有的映像版本的來源映像共用映像庫中。 映像版本必須是支援的 OS，並將映像必須複寫到與您的 Azure 映像產生器範本相同的區域。 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`應該是映像版本的 ResourceId。 使用[az sig 映像版本清單](/cli/azure/sig/image-version#az-sig-image-version-list)清單映像版本。

## <a name="properties-customize"></a>屬性： 自訂


映像產生器支援多個 '客'。 自訂者是函式，可用來自訂您的映像，例如執行指令碼，或重新啟動伺服器。 

當使用`customize`: 
- 您可以使用多個自訂者，但它們必須具有唯一`name`。
- 在範本中指定的順序中，執行自訂者。
- 如果一個自訂失敗，則整個自訂元件會失敗，並回報錯誤。
- 請考慮您的映像組建將需要此項目，和調整 'buildTimeoutInMinutes' 屬性，以允許映像產生器有足夠的時間來完成多少時間。
- 強烈建議您指令碼前要徹底測試在範本中使用它。 偵錯您自己的 VM 上的指令碼會比較容易。
- 請勿將機密資料放在指令碼中。 
- 指令碼位置必須可公開存取，除非您使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
[自訂] 區段是陣列。 Azure 映像產生器會透過客循序執行。 任何失敗的任何自訂將會失敗的組建程序。 
 
 
### <a name="shell-customizer"></a>Shell 自訂

執行殼層指令碼 shell 自訂支援，它們必須是可公開存取的存取 IB。

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS 支援：Linux 
 
自訂屬性：

- **型別**– 殼層 
- **名稱**-追蹤自訂名稱 
- **scriptUri** -檔案的位置的 URI 
- **內嵌**-殼層命令，並以逗號分隔的陣列。
 
> [!NOTE]
> 當執行 shell 自訂 RHEL ISO 來源時，您需要確保向 Red Hat 權利新增伺服器註冊，才能進行任何自訂，就會發生您第一個自訂殼層處理。 完成自訂後，指令碼應該從權利伺服器取消登錄。

### <a name="windows-restart-customizer"></a>Windows 重新啟動自訂 
重新啟動自訂可讓您重新啟動 Windows VM 並等它重新上線，這可讓您安裝需要重新開機的軟體。  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

OS 支援：視窗
 
自訂屬性：
- **類型**：WindowsRestart
- **restartCommand** -命令來執行 （選擇性） 重新啟動。 預設為 `'shutdown /r /f /t 0 /c \"packer restart\"'`。
- **restartCheckCommand** – 命令來檢查是否成功 （選擇性） 重新啟動。 
- **restartTimeout** -重新啟動指定為字串的大小和單位的逾時。 例如， `5m` （5 分鐘） 或`2h`（2 小時）。 預設值為：' 5m '


### <a name="powershell-customizer"></a>PowerShell 自訂 
執行 PowerShell 指令碼和內嵌命令 shell 自訂支援，指令碼必須可公開存取的存取 IB。

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

OS 支援：Windows 和 Linux

自訂屬性：

- **型別**– PowerShell。
- **scriptUri** -PowerShell 指令碼檔案位置的 URI。 
- **內嵌**– 內嵌命令，以執行，以逗號分隔。
- **valid_exit_codes** – 選擇性的可以從指令碼/內嵌傳回的有效程式碼命令，這可避免報告的失敗的指令碼/內嵌命令。

### <a name="file-customizer"></a>檔案的自訂

檔案的自訂可讓您從 GitHub 或 Azure 儲存體下載檔案的映像產生器。 如果您有依賴組建成品的映像組建管線時，您就可以再設定從組建共用下載檔案自訂，並將成品移到映像。  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

OS 支援：Linux 和 Windows 

檔案自訂屬性：

- **sourceUri** -存取的儲存體端點，這可以是 GitHub 或 Azure 儲存體。 您只能下載一個檔案，而不是整個目錄。 如果您需要下載目錄，使用壓縮的檔案，然後解壓縮使用殼層或 PowerShell 客。 
- **目的地**– 這是完整的目的地路徑和檔案名稱。 任何參考的路徑和子目錄必須存在，請使用殼層或 PowerShell 客事先設定這些。 您可以使用指令碼自訂者建立的路徑。 

這支援的 Windows 目錄與 Linux 路徑，但有一些差異： 
- Linux OS 的 – 映像產生器可以寫入的唯一路徑是 /tmp。
- Windows – 沒有路徑限制，而路徑必須存在。
 
 
如果發生錯誤嘗試下載檔案，或將它放在指定的目錄中，自訂步驟會失敗，而且這會位於 customization.log。

>> 請注意 ！ 檔案自訂只適合小型檔案下載，但 < 20 MB。 對於較大的檔案下載，請使用指令碼或內嵌命令，使用程式碼，例如下載檔案、 Linux`wget`或是`curl`，Windows， `Invoke-WebRequest`。

可以從 Azure 儲存體下載檔案的自訂中的檔案使用[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)。

### <a name="generalize"></a>一般化 
根據預設，Azure 映像產生器也將執行 '取消佈建' 的程式碼結尾的每個映像自訂階段中，將一般化映像。 一般化是其中的映像已設定以便可以建立多個 Vm 重複使用的程序。 適用於 Windows Vm，Azure 映像產生器會使用 Sysprep。 針對 Linux，Azure 映像產生器會執行 ' waagent-deprovision'。 

讓 Azure 映像產生器可讓您自訂此命令，如有需要可能無法適用於每種情況下，將一般化映像產生器使用者命令。 

如果您要移轉現有的自訂，而且您使用不同的 Sysprep/waagent 命令，您可以使用映像產生器的一般命令，並建立 VM 時，使用您自己的 Sysprep 或 waagent 命令。

如果成功，Azure 映像產生器建立的 Windows 自訂映像，您建立 VM，然後尋找 VM 建立失敗，或未順利完成，您必須檢閱 Windows Server Sysprep 文件，或提出支援要求Windows Server Sysprep 的客戶服務支援小組，他們可以進行疑難排解和正確的 Sysprep 使用針對通知。


#### <a name="default-sysprep-command"></a>預設 Sysprep 命令
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>預設 Linux 解除佈建命令

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>覆寫的命令
若要覆寫的命令，請使用 PowerShell 或 Shell 指令碼佈建的確切檔案名稱，建立命令檔並將它們放在正確的目錄：

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

映像產生器會讀取這些命令，這些會寫出到的 AIB 記錄檔中，'customization.log'。 請參閱[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)如何收集記錄檔。
 
## <a name="properties-distribute"></a>屬性： 散發

Azure 映像產生器支援三個發佈目標： 

- **managedImage** -受控映像。
- **sharedImage** -共用映像庫。
- **VHD** -儲存體帳戶中的 VHD。

您可以將映像到這兩個相同的組態中的目標類型，請參閱[範例](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)。

因為您可以發佈至多個目標，映像產生器會維護狀態可以藉由查詢存取每個發佈目標`runOutputName`。  `runOutputName`是您可以查詢的物件後發佈該發佈的相關資訊。 例如，您可以查詢的 VHD 或映像版本已複寫到區域的位置。 這是每個發佈目標的屬性。 `runOutputName`對每個散發的目標必須是唯一。
 
### <a name="distribute-managedimage"></a>散發： managedImage

影像輸出會是受控映像資源。

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
發佈屬性：
- **型別**– managedImage 
- **imageId** – 資源識別碼的目的地映像，必須是格式： /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **位置**-受管理的映像的位置。  
- **runOutputName** – 唯一名稱來識別分佈。  
- **artifactTags** -選用的使用者指定的機碼值組標記。
 
 
> [!NOTE]
> 目的地資源群組必須存在。
> 如果您想發佈到不同區域的映像時，它會增加部署時間。 

### <a name="distribute-sharedimage"></a>散發： sharedImage 
Azure 共用映像庫中是新的映像管理服務，可讓您的映像區域複寫，管理版本控制和共用的自訂映像。 Azure 的映像產生器支援使用此服務，散發，因此您可以將映像，到共用映像庫所支援的區域。 
 
共用映像庫組成的： 
 
- 資源庫-多個共用的映像的容器。 資源庫會部署在一個區域中。
- 映像定義-概念映像群組。 
- 映像版本-這是用來部署 VM 或擴展集映像類型。 映像版本可以複寫到其他 Vm 必須部署的區域。
 
您可以發佈至映像庫之前，您必須建立資源庫和映像定義，請參閱[共用映像](shared-images.md)。 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

發佈共用的映像組件庫的屬性：

- **type** - sharedImage  
- **galleryImageId** – 共用映像庫的識別碼。 格式為： /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>。
- **runOutputName** – 唯一名稱來識別分佈。  
- **artifactTags** -選用的使用者指定的機碼值組標記。
- **replicationRegions** -的區域複寫的陣列。 其中一個區域必須是資源庫部署所在的區域。
 
> [!NOTE]
> 您可以使用 Azure 映像產生器位於不同的區域，以資源庫中，但 Azure 映像產生器服務需要在資料中心之間傳輸映像，這會花費較多。 映像產生器會自動版本的映像中，然後再根據 單純的整數，您目前無法加以指定。 

### <a name="distribute-vhd"></a>將散發：VHD  
您可以輸出至 VHD。 您可以複製 VHD，然後使用它來發佈至 Azure MarketPlace，或搭配 Azure Stack 使用。  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
OS 支援：Windows 和 Linux

將散發 VHD 參數：

- **型別**-VHD。
- **runOutputName** – 唯一名稱來識別分佈。  
- **標記**-選用的使用者指定的機碼值組標記。
 
Azure 映像產生器不允許使用者指定的儲存體帳戶位置，但您可以查詢的狀態`runOutputs`以取得的位置。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> 一旦建立 VHD 之後，將它複製到不同的位置，儘速。 VHD 會儲存在儲存體帳戶中建立映像範本提交至 Azure 映像產生器服務時的暫時性資源群組。 如果您刪除映像範本時，您將遺失的 VHD。 
 
## <a name="next-steps"></a>後續步驟

有不同的案例中的範例.json 檔案[Azure 映像產生器 GitHub](https://github.com/danielsollondon/azvmimagebuilder)。
 
 
 
 
 
 
 
 
 
 
