---
title: 針對 Windows 的 azure 監視相依性的虛擬機器擴充功能 |Microsoft Docs
description: 部署使用虛擬機器擴充功能的 Windows 虛擬機器上的 Azure 監視相依性代理程式。
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 2cdde2f9dbba9676ba8a4d35ed6b79940668d684
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521782"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>針對 Windows 的 azure 監視相依性的虛擬機器擴充功能

適用於 VM 的 Azure 監視器對應功能會從 Microsoft Dependency Agent 取得其資料。 Windows Azure VM 的相依性代理程式虛擬機器擴充功能是發行，並由 Microsoft 支援。 Azure 虛擬機器上安裝相依性代理程式擴充功能。 本文件詳述支援的平台、 組態和 Windows 的 Azure VM 的相依性代理程式虛擬機器擴充功能部署選項。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>作業系統

可以針對支援的作業系統列在執行 Windows 的 Azure VM 的相依性代理程式擴充功能[Supported operating systems](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) Azure 監視的 Vm 部署文件的區段。

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 Azure Windows VM 上的 Azure VM 的相依性代理程式擴充功能的結構描述。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節中詳述的 JSON 結構描述可以用於 Azure Resource Manager 範本，在 Azure Resource Manager 範本部署期間執行 Azure VM 的相依性代理程式擴充功能。 

虛擬機器擴充功能的 JSON 可以巢狀方式置於虛擬機器資源內部，或放在 Resource Manager JSON 範本的根目錄或最上層。 JSON 的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources)。 

下列範例假設虛擬機器資源內部巢狀相依性代理程式擴充功能。 在巢狀處理擴充資源時，JSON 會放在虛擬機器的 `"resources": []` 物件中。


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

將擴充 JSON 置於範本的根目錄時，資源名稱包含父系虛擬機器的參考，而類型可反映巢狀的組態。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 部署

`Set-AzVMExtension`命令可用來將相依性代理程式虛擬機器擴充功能部署到現有的虛擬機器。 執行命令之前，必須將公用和私人組態儲存在 PowerShell 雜湊表中。 

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell 模組，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看所指定 VM 的擴充功能部署狀態，請使用 Azure PowerShell 模組來執行下列命令。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

擴充功能執行輸出會記錄至下列目錄中的檔案︰

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
