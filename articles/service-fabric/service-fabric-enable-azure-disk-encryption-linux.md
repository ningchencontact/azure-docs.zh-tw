---
title: 適用於 Azure Service Fabric Linux 叢集，啟用磁碟加密 |Microsoft Docs
description: 本文說明如何使用 Azure Resource Manager 和 Azure Key Vault 在 Azure 中啟用 Service Fabric 叢集擴展集的磁碟加密功能。
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
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161795"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>為服務網狀架構 Linux 叢集節點啟用磁碟加密 
> [!div class="op_single_selector"]
> * [適用於 Linux 的磁碟加密](service-fabric-enable-azure-disk-encryption-linux.md)
> * [適用於 Windows 的磁碟加密](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

請遵循下列步驟來啟用 Service Fabric Linux 叢集節點的磁碟加密功能。 您必須針對每個節點類型/虛擬機器擴展集執行這些步驟。 為了加密節點，我們會利用虛擬機器擴展集上的 Azure 磁碟加密功能。

本指南涵蓋下列程序：

* 要在 Service Fabric Linux 叢集虛擬機器擴展集上啟用磁碟加密所需注意的重要概念。
* 在 Service Fabric Linux 叢集虛擬機器擴展集上啟用磁碟加密前，所要遵循的先決條件步驟。
* 在 Service Fabric Linux 叢集虛擬機器擴展集上啟用磁碟加密所要遵循的步驟。



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

* **自行註冊** - 為了能夠使用，虛擬機器擴展集磁碟加密預覽版需要自行註冊
* 您可以執行下列步驟來自行註冊訂用帳戶： 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* 等候約 10 分鐘，直到狀態變為「已註冊」。 您可以執行下列命令來檢查狀態： 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault** - 在與虛擬機器擴展集相同的訂用帳戶與區域中建立 KeyVault，並使用其 PS Cmdlet 在 KeyVault 上設定存取原則 'EnabledForDiskEncryption'。 您也可以在 Azure 入口網站中使用 KeyVault UI 來設定原則： 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* 安裝最新版[Azure CLI](/cli/azure/install-azure-cli) ，其中包含新的加密命令。
* 安裝最新版的 [Azure SDK (來自 Azure PowerShell)](https://github.com/Azure/azure-powershell/releases) 版本。 以下是虛擬機器擴展集來啟用 ADE cmdlet ([設定](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 加密，擷取 ([取得](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 加密狀態] 和 [移除 ([停用](/powershell/module/az.compute/disable-azvmssdiskencryption)) 的標尺上的加密設定執行個體。 

| 命令 | 版本 |  `Source`  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更新版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更新版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>所支援的磁碟加密案例
* 虛擬機器擴展集加密僅支援用於以受控磁碟建立的擴展集，不支援用於原生 (或非受控) 磁碟擴展集。
* 虛擬機器擴展集加密支援用於 Linux 虛擬機器擴展集的資料磁碟區。 目前的 Linux 預覽中「不」支援 OS 磁碟加密。
* 虛擬機器擴展集 VM 重新安裝映像，並在目前的預覽版本中不支援升級的作業。


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>建立新的 Linux 叢集並啟用磁碟加密

使用下列命令來建立叢集並啟用磁碟加密使用 Azure Resource Manager 範本和自我簽署的憑證。

### <a name="sign-in-to-azure"></a>登入 Azure  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>使用既有的自訂範本 

如果您需要撰寫自訂範本以符合需求，強烈建議您使用適用於 Linux 叢集的 [Azure Service Fabric 範本範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)上所提供的其中一個範本。 

如果您已有自訂範本，則請再次確認範本及參數檔案中三個與憑證相關的參數，其命名如下所示，且值也如下所示為 Null。

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

因為對於 Linux 虛擬機器擴展集來說 - 僅支援資料磁碟加密，所以我們需要使用 Azure Resource Manager 範本新增資料磁碟。 請更新資料磁碟佈建範本，如下所示：

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

以下是對等的 CLI 命令，可執行相同動作。 將宣告陳述式中的值變更為適當的值。 CLI 可支援上述 powershell 命令所支援的所有其他參數。

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

#### <a name="linux-data-disk-mounting"></a>Linux 資料磁碟掛接
繼續進行 Linux 虛擬機器擴展集的加密之前，我們需要確定所新增的資料磁碟是否已正確掛接。 登入叢集的 Linux VM，並執行 LSBLK 命令。 輸出應該會顯示所新增的資料磁碟位於掛接點資料行上。


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>將應用程式部署到 Linux Service Fabric 叢集
請依照步驟和指導方針[將應用程式部署至叢集](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>為上面所建立的 Service Fabric Linux 叢集虛擬機器擴展集啟用磁碟加密
 
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

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>驗證 Linux 虛擬機器擴展集是否已啟用磁碟加密。
取得整個虛擬機器擴展集或擴展集內任何執行個體 VM 的狀態。 請參閱下列命令。
此外使用者可以登入叢集的 Linux VM，並執行 LSBLK 命令。 輸出應該會顯示所新增的資料磁碟位於掛接點資料行上，且所新增資料磁碟的 Type 資料行為 Crypt。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>為 Service Fabric 叢集虛擬機器擴展集停用磁碟加密 
停用磁碟加密會套用至整個虛擬機器擴展集，而不是依執行個體來套用 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>後續步驟
到目前為止，您已擁有安全的叢集，並知道如何為 Linux Service Fabric 叢集虛擬機器擴展集啟用/停用磁碟加密。 接下來，請進行[適用於 Windows 的磁碟加密](service-fabric-enable-azure-disk-encryption-windows.md) 
