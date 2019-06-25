---
title: 適用於 Azure Service Fabric Linux 叢集，啟用磁碟加密 |Microsoft Docs
description: 本文說明如何使用 Azure Resource Manager 和 Azure 金鑰保存庫啟用磁碟加密，在 Linux 中的 Azure Service Fabric 叢集節點。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258770"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>啟用磁碟加密，在 Linux 中的 Azure Service Fabric 叢集節點 
> [!div class="op_single_selector"]
> * [適用於 Linux 的磁碟加密](service-fabric-enable-azure-disk-encryption-linux.md)
> * [適用於 Windows 的磁碟加密](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

在本教學課程中，您將了解如何在 Linux 中的 Azure Service Fabric 叢集節點上啟用磁碟加密。 您必須針對每個節點類型與虛擬機器擴展集，請遵循下列步驟。 加密的節點，我們將虛擬機器擴展集上使用 Azure 磁碟加密功能。

本指南涵蓋下列主題：

* 要注意的是在 Linux 中的啟用磁碟加密，在 Service Fabric 叢集中虛擬機器擴展集時的重要概念。
* 啟用 Service Fabric 上的磁碟加密之前所需遵循的步驟執行的叢集在 Linux 中的節點。
* 若要在 Linux 中的 Service Fabric 叢集節點上啟用磁碟加密需遵循的步驟。



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

 **Self-registration**

虛擬機器擴展集的磁碟加密預覽需要自我登錄。 請使用下列步驟：

1. 執行以下命令： 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等候約 10 分鐘，直到狀態變成*Registered*。 您可以執行下列命令來檢查狀態：
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure 金鑰保存庫**

1. 在與擴展集相同的訂用帳戶和區域中建立金鑰保存庫。 然後選取**EnabledForDiskEncryption**使用其 PowerShell 指令程式來存取金鑰保存庫上的原則。 您也可以在 Azure 入口網站中使用下列命令中使用金鑰保存庫 UI 設定原則：
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安裝最新版[Azure CLI](/cli/azure/install-azure-cli)，其中包含新的加密命令。

3. 安裝最新版[從 Azure PowerShell 的 Azure SDK](https://github.com/Azure/azure-powershell/releases)版本。 以下是虛擬機器擴展集來啟用 Azure 磁碟加密 cmdlet ([設定](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 加密，擷取 ([取得](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 加密狀態] 和 [移除 ([停用](/powershell/module/az.compute/disable-azvmssdiskencryption))在標尺上的加密設定執行個體。


| 命令 | Version |  source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更新版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更新版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>所支援的磁碟加密案例
* 加密虛擬機器擴展集僅支援具有受控磁碟建立的擴展集。 其不支援用於原生 (或非受控) 磁碟擴展集。
* 在 Linux 中的虛擬機器擴展集的 OS 和資料磁碟區支援加密和停用加密。 
* 虛擬機器擴展集的虛擬機器 (VM) 重新安裝映像和升級作業並不支援目前的預覽版本。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>建立新的叢集並啟用磁碟加密

使用下列命令來建立叢集，並使用 Azure Resource Manager 範本和自我簽署的憑證來啟用磁碟加密。

### <a name="sign-in-to-azure"></a>登入 Azure  

使用下列命令登入：

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>使用既有的自訂範本 

如果您需要撰寫自訂範本，我們強烈建議您使用其中一個範本上[Azure Service Fabric 叢集中建立的範本範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)頁面。 

如果您已經有自訂範本，請仔細檢查所有三個與憑證相關參數的範本和參數檔案中的命名方式如下。 也請確定值是 null，如下所示：

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

唯一的資料磁碟加密支援在 Linux 中的虛擬機器擴展集，因為您必須使用 Resource Manager 範本來新增資料磁碟。 請更新您的資料磁碟佈建的範本，如下所示：

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

以下是對等的 CLI 命令。 Declare 陳述式中的值變更為適當的值。 CLI 支援所有其他參數可支援上述 PowerShell 命令。

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>掛接資料磁碟的 Linux 執行個體
您繼續使用在虛擬機器擴展集上的加密之前，請確定已新增的資料磁碟已正確掛接。 登入 Linux 叢集 VM 和執行**LSBLK**命令。 輸出應該會顯示在該加入的資料磁碟**掛接點**資料行。


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>部署至 Service Fabric 叢集在 Linux 中的應用程式
若要部署到您的叢集應用程式，請遵循的步驟和指引[快速入門：將 Linux 容器部署至 Service Fabric](service-fabric-quickstart-containers-linux.md)。


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>啟用磁碟加密，如先前所建立的虛擬機器擴展集
若要啟用磁碟加密的虛擬機器擴展設定您建立透過先前的步驟中，執行下列命令：
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>驗證是否已啟用的虛擬機器擴展集在 Linux 中的磁碟加密
若要取得擴展集中的整台虛擬機器擴展集或任何執行個體的狀態，請執行下列命令。
此外，您可以在 登入 Linux 叢集 VM，並執行**LSBLK**命令。 輸出應該顯示中加入的資料磁碟**掛接點**資料行，而**型別**資料行應該閱讀*Crypt*。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>停用的虛擬機器擴展集在 Service Fabric 叢集中的磁碟加密
停用磁碟加密的虛擬機器擴展集執行下列命令。 請注意，停用磁碟加密套用到整部虛擬機器擴展集，且不是個別的執行個體。

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>後續步驟
此時，您應該有一個安全的叢集，並了解如何啟用和停用 Service Fabric 叢集節點和虛擬機器擴展集磁碟加密。 如需類似指引在 Linux 中的 Service Fabric 叢集節點的詳細資訊，請參閱[磁碟加密的 Windows](service-fabric-enable-azure-disk-encryption-windows.md)。 
