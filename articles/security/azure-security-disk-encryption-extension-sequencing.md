---
title: Azure 磁碟加密和 Azure 虛擬機器擴展集擴充功能排序
description: 本文提供啟用 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密的指示。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611260"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>使用 Azure 磁碟加密，使用虛擬機器擴展集擴充功能排序

延伸模組，例如 Azure 磁碟加密可新增至 Azure 虛擬機器擴展集中指定的順序。 若要這樣做，請使用[擴充功能排序](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)。 

一般情況下，您還應該加密套用至磁碟：

- 擴充功能 」 或 「 自訂指令碼之後，準備的磁碟區。
- 擴充功能 」 或 「 自訂指令碼之前，要存取，或使用加密的磁碟或磁碟區上的資料。

在任一情況下，`provisionAfterExtensions`屬性會指定順序中稍後應新增哪些延伸模組。

## <a name="sample-azure-templates"></a>範例 Azure 範本

如果您想要讓 Azure 磁碟加密會套用其他擴充功能之後，將`provisionAfterExtensions`AzureDiskEncryption 擴充功能區塊中的屬性。 

以下是使用 「 CustomScriptExtension"，會初始化並格式化 Windows 磁碟，後面接著"AzureDiskEncryption 」 的 Powershell 指令碼的範例：

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

如果您想要讓 Azure 磁碟加密套用另一個延伸模組之前，將`provisionAfterExtensions`要遵循的延伸模組的區塊中的屬性。

以下是使用"AzureDiskEncryption 「 後面 」 VMDiagnosticsSettings"、 擴充功能來監視和診斷功能以 Windows 為基礎的 Azure VM 上的範例：


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

如需更多深入的範本，請參閱：
* 套用 Azure 磁碟加密延伸模組格式的磁碟 (Linux) 的自訂殼層指令碼之後： [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* 自訂的 Powershell 指令碼，初始化並格式化磁碟 (Windows) 之後套用 Azure 磁碟加密延伸模組： [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* 套用自訂的 Powershell 指令碼，初始化並格式化磁碟 (Windows) 之前的 Azure 磁碟加密延伸模組： [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>後續步驟
- 深入了解擴充功能排序：[序列中的虛擬機器擴展集的延伸模組佈建](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)。
- 深入了解`provisionAfterExtensions`屬性：[Microsoft.Compute/virtualMachineScaleSets 延伸模組範本參考](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)。
