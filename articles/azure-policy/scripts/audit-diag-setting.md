---
title: Azure 原則 JSON 範例 - 稽核診斷設定 | Microsoft Docs
description: 此 JSON 範例原則會稽核是否未針對指定的資源類型啟用診斷設定。
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 04/27/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 1f87d411e244d10437e3b6f9befbdee13dde14e9
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2018
---
# <a name="audit-diagnostic-setting"></a>稽核診斷設定

此內建原則會稽核診斷系統是否未針對指定的資源類型啟用設定。 您需指定資源類型陣列，以檢查是否已啟用診斷設定。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>範例範本

[!code-json[main](../../../policy-templates/samples/Monitoring/audit-diagnostic-setting/azurepolicy.json "Audit diagnostic setting")]

您可以使用 [Azure 入口網站](#deploy-with-the-portal)[PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 來部署此範本。 若要取得內建原則，請使用識別碼 `7f89b1eb-583c-429a-8828-af049802c1d9`。

## <a name="parameters"></a>參數

若要傳入參數值，請使用下列格式：

```json
{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}
```

## <a name="deploy-with-the-portal"></a>使用入口網站部署

指派原則時，從可用的內建定義選取 [稽核診斷設定]。

## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/7f89b1eb-583c-429a-8828-af049802c1d9

New-AzureRmPolicyAssignment -name "Audit diagnostics" -PolicyDefinition $definition -PolicyParameter '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>清除 PowerShell 部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit diagnostics" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Audit diagnostics" --policy 7f89b1eb-583c-429a-8828-af049802c1d9 --params '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}'
```

### <a name="clean-up-azure-cli-deployment"></a>清除 Azure CLI 部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli-interactive
az policy assignment delete --name "Audit diagnostics" --resource-group myResourceGroup
```

## <a name="next-steps"></a>後續步驟

- 其他「Azure 原則」範本範例都位於[Azure 原則的範本](../json-samples.md)。
