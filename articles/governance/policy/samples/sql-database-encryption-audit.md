---
title: 範例 - 稽核 SQL 上的透明資料加密
description: 此原則定義範例會稽核 SQL 資料庫是否未啟用透明資料加密。
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 4027ad28b6589872dd2f52961710c4db95a257e7
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254788"
---
# <a name="sample---audit-sql-database-encryption"></a>範例 - 稽核 SQL 資料庫加密

此內建原則會稽核 SQL 資料庫是否未啟用透明資料加密。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>範例範本

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

您可以使用 [Azure 入口網站](#deploy-with-the-portal) [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 來部署此範本。 若要取得內建原則，請使用識別碼 `17k78e20-9358-41c9-923c-fb736d382a12`。

## <a name="deploy-with-the-portal"></a>使用入口網站部署

指派原則時，從可用的內建定義選取 [稽核透明資料加密狀態]  。

## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>清除 PowerShell 部署

執行下列命令以移除原則指派。

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>清除 Azure CLI 部署

執行下列命令以移除原則指派。

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](index.md)中檢閱更多範例