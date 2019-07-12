---
title: 適用於 Azure Service Fabric Windows 叢集，啟用磁碟加密 |Microsoft Docs
description: 本文說明如何使用 Azure 金鑰保存庫 Azure Resource Manager 中啟用 Azure Service Fabric 叢集節點的磁碟加密。
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471406"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>為 Azure Service Fabric 叢集節點，在 Windows 中啟用磁碟加密 
> [!div class="op_single_selector"]
> * [適用於 Windows 的磁碟加密](service-fabric-enable-azure-disk-encryption-windows.md)
> * [適用於 Linux 的磁碟加密](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

在本教學課程中，您將了解如何在 Windows 中的 Service Fabric 叢集節點上啟用磁碟加密。 您必須針對每個節點類型與虛擬機器擴展集，請遵循下列步驟。 加密的節點，我們將虛擬機器擴展集上使用 Azure 磁碟加密功能。

本指南涵蓋下列主題：

* 要注意的 Windows 中的 Service Fabric 叢集中虛擬機器擴展上的啟用磁碟加密設定時的重要概念。
* 啟用 Service Fabric 上的磁碟加密之前所需遵循的步驟中的叢集節點 Windows。
* 若要在 Windows 中的 Service Fabric 叢集節點上啟用磁碟加密需遵循的步驟。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

**Self-registration** 

虛擬機器擴展集的磁碟加密預覽需要自我登錄。 請使用下列步驟： 

1. 首先，執行下列命令：
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等候約 10 分鐘，直到狀態變成*Registered*。 您可以執行下列命令來檢查狀態： 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure 金鑰保存庫** 

1. 建立金鑰保存庫位於相同的訂用帳戶和區域擴展集，然後選取**EnabledForDiskEncryption**使用其 PowerShell 指令程式來存取金鑰保存庫上的原則。 您也可以在 Azure 入口網站中使用下列命令中使用金鑰保存庫 UI 設定原則：
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安裝最新版[Azure CLI](/cli/azure/install-azure-cli)，其中包含新的加密命令。
3. 安裝最新版[從 Azure PowerShell 的 Azure SDK](https://github.com/Azure/azure-powershell/releases)版本。 以下是虛擬機器擴展集來啟用 Azure 磁碟加密 cmdlet ([設定](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 加密，擷取 ([取得](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 加密狀態] 和 [移除 ([停用](/powershell/module/az.compute/disable-azvmssdiskencryption))在標尺上的加密設定執行個體。

| 命令 | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更新版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更新版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更新版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>所支援的磁碟加密案例
* 加密虛擬機器擴展集僅支援具有受控磁碟建立的擴展集。 其不支援用於原生 (或非受控) 磁碟擴展集。
* 支援加密的 OS 和 Windows 中的資料磁碟區中虛擬機器擴展集。 停用加密也支援的 OS 和 Windows 中的資料磁碟區的虛擬機器擴展集。
* 虛擬機器擴展集的虛擬機器重新安裝映像和升級作業並不支援目前的預覽版本。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>建立新的叢集並啟用磁碟加密

請使用下列命令來建立叢集，並使用 Azure Resource Manager 範本和自我簽署憑證來啟用磁碟加密。

### <a name="sign-in-to-azure"></a>登入 Azure 
使用下列命令登入：
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>使用既有的自訂範本 

如果您需要撰寫自訂的範本，以符合您的需求，我們強烈建議您開始使用其中一個可用的範本[Azure Service Fabric 叢集中建立的範本範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)頁面。 若要[來自訂您的叢集範本][ customize-your-cluster-template]區段中，請參閱下列指導方針。

如果您已經有自訂範本，請仔細檢查所有三個與憑證相關參數的範本和參數檔案中的命名方式如下而且值是 null，如下所示：

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>應用程式部署至 Windows 中的 Service Fabric 叢集
若要部署到您的叢集應用程式，請遵循的步驟和指引[使用 PowerShell 部署與移除應用程式](service-fabric-deploy-remove-applications.md)。


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>驗證如果虛擬機器擴展集的 Windows 中啟用磁碟加密
在擴展集執行下列命令中取得的整台虛擬機器擴展集或任何執行個體的狀態。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


此外，您可以登入虛擬機器擴展集，並請確定磁碟機加密。

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>停用的虛擬機器擴展集在 Service Fabric 叢集中的磁碟加密 
停用磁碟加密的虛擬機器擴展集執行下列命令。 請注意，停用磁碟加密套用到整部虛擬機器擴展集，且不是個別的執行個體。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>後續步驟
此時，您應該有一個安全的叢集，並了解如何啟用和停用 Service Fabric 叢集節點和虛擬機器擴展集磁碟加密。 如需類似指引在 Linux 中的 Service Fabric 叢集節點的詳細資訊，請參閱[適用於 Linux 的磁碟加密](service-fabric-enable-azure-disk-encryption-linux.md)。

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
