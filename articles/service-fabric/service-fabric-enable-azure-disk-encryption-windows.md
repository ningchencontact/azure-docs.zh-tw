---
title: 為服務網狀架構 Windows 叢集啟用磁碟加密 | Microsoft Docs
description: 本文說明如何使用 Azure Resource Manager 和 Azure Key Vault 在 Azure 中啟用 Service Fabric 叢集節點的磁碟加密功能。
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/23/2018
ms.author: v-viban
ms.openlocfilehash: 0b84d270cc50888822b8463df2b95aedaa34ee9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659187"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>為服務網狀架構 Windows 叢集節點啟用磁碟加密 
> [!div class="op_single_selector"]
> * [適用於 Windows 的磁碟加密](service-fabric-enable-azure-disk-encryption-windows.md)
> * [適用於 Linux 的磁碟加密](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

請遵循下列步驟來啟用 Service Fabric Windows 叢集節點的磁碟加密功能。 您必須針對每個節點類型/虛擬機器擴展集執行這些步驟。 為了加密節點，我們會利用虛擬機器擴展集上的 Azure 磁碟加密功能。

本指南涵蓋下列程序：

* 要在 Service Fabric Windows 叢集虛擬機器擴展集上啟用磁碟加密所需注意的重要概念。
* 在 Service Fabric Windows 叢集虛擬機器擴展集上啟用磁碟加密前，所要遵循的先決條件步驟。
* 在 Service Fabric Windows 叢集虛擬機器擴展集上啟用磁碟加密所要遵循的步驟。


## <a name="prerequisites"></a>先決條件
1. **自行註冊** - 為了能夠使用，虛擬機器擴展集磁碟加密預覽版需要自行註冊
2. 您可以執行下列步驟來自行註冊訂用帳戶： 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. 等候約 10 分鐘，直到狀態變為「已註冊」。 您可以執行下列命令來檢查狀態： 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Azure Key Vault** - 在與擴展集相同的訂用帳戶與區域中建立 KeyVault，並使用其 PS Cmdlet 在 KeyVault 上設定存取原則 'EnabledForDiskEncryption'。 您也可以在 Azure 入口網站中使用 KeyVault UI 來設定原則： 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. 安裝最新的 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)，其含有新的加密命令。
6. 安裝最新版的 [Azure SDK (來自 Azure PowerShell)](https://github.com/Azure/azure-powershell/releases) 版本。 以下是虛擬機器擴展集 ADE Cmdlet，其可用來啟用 ([設定](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) 加密功能、擷取 ([取得](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) 加密狀態，以及移除 ([停用](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) 擴展集執行個體的加密功能。

| 命令 | 版本 |  來源  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 或更新版本 | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 或更新版本 | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 或更新版本 | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 或更新版本 | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 或更新版本 | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 或更新版本 | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>所支援的磁碟加密案例
* 虛擬機器擴展集加密僅支援用於以受控磁碟建立的擴展集，不支援用於原生 (或非受控) 磁碟擴展集。
* 虛擬機器擴展集加密支援用於 Windows VMSS 的 OS 和資料磁碟區。 停用加密支援用於 Windows 擴展集的 OS 和資料磁碟區。
* 目前的預覽中不支援虛擬機器擴展集 VM 的重新製作映像和升級作業。


### <a name="create-new-cluster-and-enable-disk-encryption"></a>建立新叢集並啟用磁碟加密

請使用下列命令來建立叢集，並使用 Azure Resource Manager 範本和自我簽署憑證來啟用磁碟加密。

### <a name="log-in-to-azure"></a>登入 Azure 

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>使用既有的自訂範本 

如果您需要撰寫自訂範本以符合需求，強烈建議您使用 [Azure Service Fabric 範本範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)上提供的其中一個範本。 請依照下面 [自訂叢集範本][customize-your-cluster-template] 一節的指導方針和說明來進行。

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


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```CLI

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>將應用程式部署到 Windows Service Fabric 叢集
請依照步驟和指導方針[將應用程式部署至叢集](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>為上面所建立的 Service Fabric 叢集虛擬機器擴展集啟用磁碟加密
 
```Powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>驗證 Windows 虛擬機器擴展集是否已啟用磁碟加密。
取得整個虛擬機器擴展集或擴展集內任何執行個體的狀態。 請參閱下列命令。
此外，使用者還可以登入擴展集內的 VM，並確定磁碟機是否已加密

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>為 Service Fabric 叢集虛擬機器擴展集停用磁碟加密 
停用磁碟加密會套用至整個虛擬機器擴展集，而不是依執行個體來套用 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>後續步驟
到目前為止，您已擁有安全的叢集，並知道如何為 Service Fabric 叢集虛擬機器擴展集啟用/停用磁碟加密。 接下來，請進行[適用於 Linux 的磁碟加密](service-fabric-enable-azure-disk-encryption-linux.md) 

