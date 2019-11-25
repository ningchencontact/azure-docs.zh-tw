---
title: Azure DSC extension for Linux
description: 安裝 OMI 和 DSC 套件，以允許使用「期望的狀態設定」來設定 Azure Linux VM。
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: b631a370c64522c201f1208819b5a76895d83b09
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457517"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC extension for Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) is a management platform that you can use to manage your IT and development infrastructure with configuration as code.

> [!NOTE]
> The DSC extension for Linux and the [Azure Monitor virtual machine extension for Linux](/azure/virtual-machines/extensions/oms-linux) currently present a conflict and aren't supported in a side-by-side configuration. Don't use the two solutions together on the same VM.

The DSCForLinux extension is published and supported by Microsoft. 此擴充功能可在 Azure 虛擬機器上安裝 OMI 和 DSC 代理程式。 The DSC extension can also do the following actions:


- Register the Linux VM to an Azure Automation account to pull configurations from the Azure Automation service (Register ExtensionAction).
- Push MOF configurations to the Linux VM (Push ExtensionAction).
- Apply meta MOF configuration to the Linux VM to configure a pull server in order to pull node configuration (Pull ExtensionAction).
- Install custom DSC modules to the Linux VM (Install ExtensionAction).
- Remove custom DSC modules from the Linux VM (Remove ExtensionAction).

 

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統

DSC Linux 延伸模組可支援所有 [Azure 背書的 Linux 散發套件](/azure/virtual-machines/linux/endorsed-distros)，但下列項目除外：

| 通路業 | 版本 |
|---|---|
| Debian | 所有版本 |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>網際網路連線

The DSCForLinux extension requires the target virtual machine to be connected to the internet. For example, the Register extension requires connectivity to the Automation service. For other actions such as Pull, Pull, Install requires connectivity to Azure Storage and GitHub. It depends on settings provided by the customer.

## <a name="extension-schema"></a>擴充功能結構描述

### <a name="public-configuration"></a>公用組態

以下是所有受支援的公用組態參數：

* `FileUri`: (optional, string) The uri of the MOF file, meta MOF file, or custom resource zip file.
* `ResourceName`: (optional, string) The name of the custom resource module.
* `ExtensionAction`：(選擇性，字串) 指定擴充功能的作用。 Valid values are Register, Push, Pull, Install, and Remove. If not specified, it's considered a Push Action by default.
* `NodeConfigurationName`: (optional, string) The name of a node configuration to apply.
* `RefreshFrequencyMins`: (optional, int) Specifies how often (in minutes) that DSC attempts to obtain the configuration from the pull server. 
       If configuration on the pull server differs from the current one on the target node, it's copied to the pending store and applied.
* `ConfigurationMode`：(選擇性，字串) 指定 DSC 套用組態的方式。 Valid values are ApplyOnly, ApplyAndMonitor, and ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`：(選擇性，int) 指定 DSC 確認組態處於適當狀態的頻率 (以分鐘為單位)。

> [!NOTE]
> If you use a version earlier than 2.3, the mode parameter is the same as ExtensionAction. Mode seems to be an overloaded term. To avoid confusion, ExtensionAction is used from version 2.3 onward. 為了顧及回溯相容性，此擴充功能同時支援模式和 ExtensionAction。 
>

### <a name="protected-configuration"></a>受保護的組態

以下是所有受支援和保護的組態參數：

* `StorageAccountName`: (optional, string) The name of the storage account that contains the file
* `StorageAccountKey`: (optional, string) The key of the storage account that contains the file
* `RegistrationUrl`: (optional, string) The URL of the Azure Automation account
* `RegistrationKey`: (optional, string) The access key of the Azure Automation account


## <a name="scenarios"></a>案例

### <a name="register-an-azure-automation-account"></a>Register an Azure Automation account
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell format
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Apply an MOF configuration file (in an Azure storage account) to the VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Apply an MOF configuration file (in public storage) to the VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell format
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Apply a meta MOF configuration file (in an Azure storage account) to the VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>將中繼 MOF 組態檔 (位於公用儲存體中) 套用至 VM
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell format
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Install a custom resource module (a zip file in an Azure storage account) to the VM
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell format
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Install a custom resource module (a zip file in public storage) to the VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell format
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>從 VM 中移除自訂資源模組
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell format
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 Templates are ideal when you deploy one or more virtual machines that require post-deployment configuration, such as onboarding to Azure Automation. 

範例 Resource Manager 範本為 [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) 和 [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu)。

For more information about the Azure Resource Manager template, see [Authoring Azure Resource Manager templates](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Azure CLI 部署

### <a name="use-azure-cliazure-cli"></a>Use [Azure CLI][azure-cli]
Before you deploy the DSCForLinux extension, configure your `public.json` and `protected.json` according to the different scenarios in section 3.

#### <a name="classic"></a>傳統
The classic deployment mode is also called Azure Service Management mode. 您可以執行下列命令以切換至該模式：
```
$ azure config mode asm
```

You can deploy the DSCForLinux extension by running:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

若要了解最新的可用擴充功能版本，請執行：
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
您可以執行下列命令，以切換至 Azure Resource Manager 模式：
```
$ azure config mode arm
```

You can deploy the DSCForLinux extension by running:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> In Azure Resource Manager mode, `azure vm extension list` isn't available for now.
>

### <a name="use-azure-powershellazure-powershell"></a>Use [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>傳統

You can sign in to your Azure account in Azure Service Management mode by running:

```powershell>
Add-AzureAccount
```

And deploy the DSCForLinux extension by running:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Change the content of $privateConfig and $publicConfig according to different scenarios in the previous section.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

You can sign in to your Azure account in Azure Resource Manager mode by running:

```powershell>
Login-AzAccount
```

To learn more about how to use Azure PowerShell with Azure Resource Manager, see [Manage Azure resources by using Azure PowerShell](../../azure-resource-manager/manage-resources-powershell.md).

You can deploy the DSCForLinux extension by running:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Change the content of $privateConfig and $publicConfig according to different scenarios in the previous section.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

Data about the state of extension deployments can be retrieved from the Azure portal and by using the Azure CLI. To see the deployment state of extensions for a given VM, run the following command by using the Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Error code: 51 represents either unsupported distribution or unsupported extension action.
In some cases, DSC Linux extension fails to install OMI when a higher version of OMI already exists in the machine. [錯誤回應：(000003) 不允許降級]



### <a name="support"></a>支援

If you need more help at any point in this article, contact the Azure experts on the [MSDN Azure and Stack Overflow forums](https://azure.microsoft.com/support/community/). Alternatively, you can file an Azure Support incident. Go to the [Azure Support site](https://azure.microsoft.com/support/options/), and select **Get support**. 如需關於使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Linux 功能](features-linux.md)。
