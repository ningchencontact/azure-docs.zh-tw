---
title: 適用於 Windows 的金鑰保存庫 VM 擴充功能 | Microsoft Docs
description: 在虛擬機器上，使用虛擬機器擴充功能部署執行自動重新整理金鑰保存庫祕密的代理程式。
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944750"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>適用於 Windows 的金鑰保存庫虛擬機器擴充功能

## <a name="overview"></a>概觀

金鑰保存庫 VM 擴充功能可自動重新整理儲存在 Azure 金鑰保存庫中的祕密。 具體來說，此擴充功能會監視儲存在金鑰保存庫中觀察之憑證的清單，並在偵測到變更之後，擷取並安裝對應的憑證。 Microsoft 目前會在 Windows VM 上發行金鑰保存庫 VM 擴充功能並提供支援。 本文件詳述適用於 Windows 的金鑰保存庫 VM 擴充功能所支援的平台、組態和部署選項。 

## <a name="prerequisites"></a>先決條件

金鑰保存庫 VM 擴充功能與受控服務識別 VM 擴充功能有關，可向金鑰保存庫服務驗證本身。 如需進一步的詳細資料，請參閱 MSI VM 擴充功能的文件。

### <a name="operating-system"></a>作業系統

金鑰保存庫 VM 擴充功能目前支援所有 Windows 版本。

| 配送映像 | 版本 |
|---|---|
| Windows | 核心 |

### <a name="internet-connectivity"></a>網際網路連線

適用於 Windows 的金鑰保存庫 VM 擴充功能會要求目標虛擬機器連線到網際網路。 

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示金鑰保存庫 VM 擴充功能的結構描述。 此擴充功能不需要受保護的設定，其所有設定都會被視為沒有安全性影響的資訊。 此擴充功能需要有受監視的祕密、輪詢頻率和目的地憑證存放區的清單。 具體而言：  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>屬性值

| Name | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | 日期 |
| publisher | Microsoft.Azure.KeyVault.Edp | 字串 |
| type | KeyVaultForWindows | 字串 |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | 字串 |
| certificateStoreLocation  | LOCAL_MACHINE | 字串 |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | 字串陣列


## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後重新整理憑證的虛擬機器時，很適合使用範本。 此擴充功能可以部署到個別的 VM 或 VM 擴展集。 結構描述與組態對於這兩種範本類型都是通用的。 

虛擬機器擴充功能的 JSON 組態必須在內嵌在範本的虛擬機器資源片段，具體來說，就是虛擬機器的 `"resources": []` 物件。

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Azure PowerShell 部署

Azure PowerShell 可以用來將金鑰保存庫 VM 擴充功能部署到現有的虛擬機器或虛擬機器擴展集。 

* 若要在 VM 上部署擴充功能：
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 例如，若要在 VM 擴展集上部署擴充功能，以作為 Service Fabric 叢集的一部分：

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

請留意下列限制/需求：
- 部署必須在美國中南部地區完成
- 金鑰保存庫限制：
    - 部署時必須存在 
    - 必須與部署位於相同的區域和資源群組
    - 必須針對部署和範本部署啟用

## <a name="azure-cli-deployment"></a>Azure CLI 部署

很快就會提供在 Azure CLI 中部署金鑰保存庫 VM 擴充功能的範例。 

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看所指定 VM 的擴充功能部署狀態，請使用 Azure PowerShell 執行下列命令。

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

擴充功能執行輸出會記錄至下列檔案︰

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
