---
title: 透過 Azure 原則以程式設計方式建立原則並檢視合規性資料 | Microsoft Docs
description: 本文會逐步引導您以程式設計方式建立及管理 Azure 原則的原則。
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/28/2018
ms.topic: article
ms.service: azure-policy
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 1809f0b7ef386bb9eeaa55982178e4cd5e1dd2e2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>以程式設計方式建立原則並檢視合規性資料

本文會逐步引導您以程式設計方式建立及管理原則。 並說明如何檢視資源的合規性狀態及原則。 原則定義會對您的資源強制執行不同規則和動作。 強制作業可確保資源會符合您的公司標準及服務等級協定規範。

## <a name="prerequisites"></a>先決條件

開始之前，請確定您已符合下列必要條件：

1. 請安裝 [ARMClient](https://github.com/projectkudu/ARMClient) (如果尚未安裝)。 此工具會將 HTTP 要求傳送至以 Azure Resource Manager 為基礎的 API。
2. 將您的 AzureRM PowerShell 模組更新為最新版本。 如需最新版本的詳細資訊，請參閱 Azure PowerShell https://github.com/Azure/azure-powershell/releases。
3. 使用 Azure PowerShell 註冊 Policy Insights 資源提供者，以確保您的訂用帳戶可搭配資源提供者使用。 若要註冊資源提供者，您必須有權執行資源提供者的註冊動作作業。 這項作業包含在「參與者」和「擁有者」角色中。 執行下列命令以註冊資源提供者：

    ```
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
    ```

    如需註冊及檢視資源提供者的詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/resource-manager-supported-services.md)。

4. 如果尚未安裝 Azure CLI，請先安裝。 您可以在＜[在 Windows 上安裝 Azure CLI 2.0](/azure/install-azure-cli-windows?view=azure-cli-latest)＞中取得最新版本。

## <a name="create-and-assign-a-policy-definition"></a>建立及指派原則定義

要更清楚看見您資源的首要步驟，是透過您的資源建立及指派原則。 下一步是了解如何以程式設計方式建立及指派原則。 範例原則會使用 PowerShell、Azure CLI 和 HTTP 要求，來稽核已對所有公用網路開放的儲存體帳戶。

下列命令會建立標準層的原則定義。 標準層可協助您達到大規模管理、合規性評估和補救。 如需定價層的詳細資訊，請參閱 [Azure 原則定價](https://azure.microsoft.com/pricing/details/azure-policy)。

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>使用 PowerShell 建立並指派原則定義

1. 使用下列 JSON 程式碼片段建立名稱為 AuditStorageAccounts.json 的 JSON 檔案。

    ```
    {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
          "equals": "Allow"
        }
      ]
    },
    "then": {
      "effect": "audit"
    }
  }

    ```

    如需撰寫原則定義的詳細資訊，請參閱 [Azure 原則定義結構](policy-definition.md)。

2. 使用 AuditStorageAccounts.json 檔案，執行下列命令來建立原則定義。

    ```
    PS C:\>New-AzureRmPolicyDefinition -Name "AuditStorageAccounts" -DisplayName "Audit Storage Accounts Open to Public Networks" -Policy C:\AuditStorageAccounts.json
    ```

    此命令會建立名為_對公用網路開放的稽核儲存體帳戶_的原則定義。 如需您可使用的其他參數詳細資訊，請參閱 [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition?view=azurermps-4.4.1)。

3. 建立原則定義之後，您可以執行下列命令來建立原則指派：

    ```
$rg = Get-AzureRmResourceGroup -Name "ContosoRG"
```

    ```
$Policy = Get-AzureRmPolicyDefinition -Name "AuditStorageAccounts"
    ```

    ```
New-AzureRmPolicyAssignment -Name "AuditStorageAccounts" -PolicyDefinition $Policy -Scope $rg.ResourceId –Sku @{Name='A1';Tier='Standard'}
    ```

    以您想要的資源群組名稱取代 _ContosoRG_。

如需使用 Azure Resource Manager PowerShell 模組來管理資源原則的詳細資訊，請參閱 [AzureRM.Resources](/powershell/module/azurerm.resources/?view=azurermps-4.4.1#policies)。

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>使用 ARMClient 來建立及指派原則定義

請使用下列程序來建立原則定義。

1. 複製下列 JSON 程式碼片段以建立 JSON 檔案。 您會在下一個步驟中呼叫該檔案。

    ```
    {
    "properties": {
        "displayName": "Audit Storage Accounts Open to Public Networks",
        "policyType": "Custom",
        "mode": "Indexed",
        "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
        "parameters": {},
        "policyRule": {
              "if": {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                    "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                    "equals": "Allow"
                  }
                ]
              },
              "then": {
                "effect": "audit"
              }
            }
    }
}
```

2. 使用下列呼叫建立原則定義︰

    ```
    armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01 @<path to policy definition JSON file>"
    ```

    以您要用的訂用帳戶識別碼取代 preceding_ &lt;subscriptionId&gt;。

如需查詢之結構的詳細資訊，請參閱[原則定義 – 建立或更新](/rest/api/resources/policydefinitions/createorupdate)。


使用下列程序來建立原則指派，並在資源群組層級指派原則定義。

1. 複製下列 JSON 程式碼片段以建立 JSON 原則指派檔案。 以您自己的值取代 &lt;&gt; 符號中的範例資訊。

    ```
    {
  "properties": {
"description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
"displayName": "Audit Storage Accounts Open to Public Networks Assignment",
"parameters": {},
"policyDefinitionId":"/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
"scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
},
"sku": {
    "name": "A1",
    "tier": "Standard"
    }
}
    ```

2. 使用下列呼叫建立原則指派︰

    ```
    armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
    ```

    以您自己的值取代 &lt;&gt; 符號中的範例資訊。

 如需對 REST API 執行 HTTP 呼叫的詳細資訊，請參閱 [Azure REST API 資源](/rest/api/resources/)。

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>使用 Azure CLI 建立並指派原則定義

若要建立原則定義，請使用下列程序：

1. 複製下列 JSON 程式碼片段以建立 JSON 原則指派檔案。

    ```
    {
                  "if": {
                    "allOf": [
                      {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                      },
                      {
                        "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                        "equals": "Allow"
                      }
                    ]
                  },
                  "then": {
                    "effect": "audit"
                  }
    }
    ```

2. 執行下列命令以建立原則定義：

    ```
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
    ```

使用下列命令以建立原則指派。 以您自己的值取代 &lt;&gt; 符號中的範例資訊。

```
az policy assignment create --name '<Audit Storage Accounts Open to Public Networks in Contoso RG' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

您可以使用 PowerShell 與下列命令取得原則定義識別碼：

```
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

針對您所建立的原則定義，其原則定義識別碼應該類似下列範例：

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

如需有關如何使用 Azure CLI 來管理資源原則的詳細資訊，請參閱 [Azure CLI 資源原則](/cli/azure/policy?view=azure-cli-latest)。

## <a name="identify-non-compliant-resources"></a>識別不相容的資源

在指派中，如果資源沒有遵循原則或方案規則，則該資源不符合規範。 下表顯示不同的原則動作如何與結果合規性狀態的條件評估搭配使用：

| **資源狀態** | **Action** | **原則評估** | **合規性狀態** |
| --- | --- | --- | --- |
| exists | 拒絕、稽核、附加\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不符合規範 |
| exists | 拒絕、稽核、附加\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 相容 |
| 新增 | 稽核、AuditIfNotExist\* | True | 不符合規範 |
| 新增 | 稽核、AuditIfNotExist\* | False | 相容 |

\* 附加、DeployIfNotExist 和 AuditIfNotExist 動作需要 IF 陳述式為 TRUE。 這些動作也需要存在條件為 FALSE，以呈現不符合規範。 若為 TRUE，IF 條件會觸發相關資源的存在條件評估。

為了進一步了解資源如何標示為不符合規範，讓我們使用上面建立的原則指派範例。

例如，假設您有資源群組 – ContosoRG，且部分的儲存體帳戶 (以紅色醒目提示) 會公開至公用網路。

![公開至公用網路的儲存體帳戶](./media/policy-insights/resource-group01.png)

在此範例中，您必須注意安全性風險。 現在您已建立原則指派，其會針對 ContosoRG 資源群組中的所有儲存體帳戶來進行評估。 其會稽核三個不符合規範的儲存體帳戶，並因此將其狀態變更為**不符合規範**。

![已稽核不符合規範的儲存體帳戶](./media/policy-insights/resource-group03.png)

使用下列程序來識別資源群組中不符合原則指派的資源。 在範例中，資源是 ContosoRG 資源群組中的儲存體帳戶。

1. 執行下列命令以取得原則指派識別碼：

    ```
    $policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Storage Accounts with Open Public Networks"}
    ```

    ```
    $policyAssignment.PolicyAssignmentId
    ```

    如需有關取得原則指派識別碼的詳細資訊，請參閱 [Get-AzureRMPolicyAssignment](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment?view=azurermps-4.4.1)。

2. 執行下列命令，以將不合規資源的資源識別碼複製到 JSON 檔案中：

    ```
    armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
    ```

3. 結果會類似下列範例：

  ```
      {
  "@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
  "@odata.count": 3,
  "value": [
  {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
      },
      {
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
             },
  {
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
             }
  ]
  }
  ```

結果同等於通常在 [Azure 入口網站檢視](assign-policy-definition.md#identify-non-compliant-resources)中看到列在 [不符合規範的資源] 之下的內容。

目前，不符合規範的資源是只能使用 Azure 入口網站和 HTTP 要求來識別。 如需有關查詢原則狀態的詳細資訊，請參閱[原則狀態](/rest/api/policy-insights/policystates) API 參考文章。

## <a name="view-policy-events"></a>檢視原則事件

當建立或更新資源時，會產生原則評估結果。 結果稱為_原則事件_。 執行下列查詢來檢視與原則指派相關聯的所有原則事件。

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

您的結果類似下列範例：

```
{
  "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
  "@odata.count": 1,
  "value": [
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
      "NumAuditEvents": 3
    }
  ]
}

```

如同原則狀態，您只能使用 HTTP 要求檢視原則事件。 如需有關查詢原則事件的詳細資訊，請參閱[原則事件](/rest/api/policy-insights/policyevents)參考文章。

## <a name="change-a-policy-assignments-pricing-tier"></a>變更原則指派定價層

您可以使用 Set-AzureRmPolicyAssignment PowerShell Cmdlet 將現有原則指派的定價層更新為為「標準」或「免費」。 例如︰

```
Set-AzureRmPolicyAssignment -Id /subscriptions/<subscriptionId/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/<policyAssignmentID> -Sku @{Name='A1';Tier='Standard'}
```

如需有關 Cmdlet 的詳細資訊，請參閱 [Set-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Set-AzureRmPolicyAssignment?view=azurermps-4.4.1)。

## <a name="next-steps"></a>後續步驟

如需本文中查詢與命令的詳細資訊，請檢閱以下文章。

- [Azure REST API 資源](/rest/api/resources/)
- [Azure RM PowerShell 模組](/powershell/module/azurerm.resources/?view=azurermps-4.4.1#policies)
- [Azure CLI 原則命令](/cli/azure/policy?view=azure-cli-latest)
- [資源提供者 REST API 參考](/rest/api/policy-insights)
