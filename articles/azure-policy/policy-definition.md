---
title: Azure 原則定義結構
description: 說明「Azure 原則」如何使用資源原則定義，藉由描述強制執行原則的時機及所產生的效果，為您組織中的資源建立慣例。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/16/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: ac561be75306cab6b73b457a7d450bd640aac067
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818692"
---
# <a name="azure-policy-definition-structure"></a>Azure 原則定義結構

「Azure 原則」使用的資源原則定義可讓您藉由描述強制執行原則的時機及所產生的效果，為組織中的資源建立慣例。 藉由定義慣例，您可以控制成本以及更輕鬆地管理您的資源。 例如，您可以指定僅允許特定類型的虛擬機器。 或者，您可以要求所有資源都有特定標籤。 原則會由所有子資源繼承。 所以，如果原則套用至資源群組，它會適用於該資源群組中的所有資源。

Azure 原則所使用的結構描述位於此處：[https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

使用 JSON 來建立原則定義。 原則定義中包含以下的項目︰

- 模式
- parameters
- 顯示名稱
- 說明
- 原則規則
  - 邏輯評估
  - 效果

例如，下列 JSON 示範一個會限制資源部署位置的原則：

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

所有 Azure 原則範例都位於[原則範例](json-samples.md)。

## <a name="mode"></a>Mode

**Mode** 決定原則要評估哪些資源類型。 支援的模式如下：

- `all`：評估資源群組和所有資源類型
- `indexed`：只評估支援標記和位置的資源類型

我們建議您在大部分的情況下都將 **mode** 設定為 `all`。 透過入口網站使用 `all` 模式建立的所有原則定義。 如果您是使用 PowerShell 或 Azure CLI，則可手動指定 **mode** 參數。 如果原則定義未包含 **mode** 值，則在 Azure PowerShell 中會預設為 `all`，在 Azure CLI 中預設為 `null` (等同於 `indexed`)，以獲得回溯相容性。

當建立會強制執行標籤或位置的原則時，應該使用 `indexed`。 這不是必要的，但它會防止不支援標籤和位置的資源在合規性結果中顯示為不符合規範。 唯一的例外是**資源群組**。 嘗試在資源群組上強制執行位置或標籤的原則應該將 **mode** 設定為 `all`，並明確地將 `Microsoft.Resources/subscriptions/resourceGroup` 類型設定為目標。 如需範例，請參閱[強制執行資源群組標籤](scripts/enforce-tag-rg.md)。

## <a name="parameters"></a>參數

參數可減少原則定義數量來幫助您簡化原則管理。 將參數想像成就像表單上的欄位一樣 – `name``address`、`city``state`。 這些參數一律保持不變，不過它們的值則會根據填寫表單的個人而有所變更。 在建立原則時，參數也是以相同的方式運作。 藉由在原則定義中納入參數，您便可以針對不同的案例使用不同的值，來重複使用該原則。

例如，您可以為資源特性定義一個原則，來限制可部署資源的位置。 在此情況下，您會在建立原則時宣告下列參數：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

參數的類型可以是字串或陣列。 中繼資料屬性會用於 Azure 入口網站之類的工具，可顯示使用者易懂的資訊。

在中繼資料屬性內，您可以使用 **strongType** 在 Azure 入口網站內提供可複選的選項清單。  **strongType** 所允許的值目前包括：

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

在原則規則中，您可以使用下列 `parameters` 部署值函式語法來參考參數︰

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>定義位置

在建立方案或原則定義時，您必須指定定義位置。

定義位置可決定方案或原則定義的指派範圍。 位置可以指定為管理群組，或指定為訂用帳戶。

> [!NOTE]
> 如果您計劃要將此原則定義套用至多個訂用帳戶，則作為位置的管理群組必須包含您要對其指派方案或原則的訂用帳戶。

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用 **displayName** 和 **description** 來識別原則定義，以及提供其使用時機的內容。

## <a name="policy-rule"></a>原則規則

原則規則包含 **If** 和 **Then**區塊。 在 **If** 區塊中，您可以定義一個或多個指定強制執行此原則時間的條件。 您可以將邏輯運算子套用至這些條件，以精確地定義原則的案例。

在 **Then** 區塊中，定義當 **If** 條件履行時所發生的效果。

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>邏輯運算子

支援的邏輯運算子包括︰

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 語法會反轉條件的結果。 **allOf** 語法 (類似於邏輯**And** 作業) 需要所有的條件為 true。 **anyOf** 語法 (類似於邏輯**Or** 作業) 需要一或多個條件為 true。

您可以巢狀邏輯運算子。 下列範例顯示 **allOf** 作業中的巢狀 **not** 作業。

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>條件

條件會評估某個**欄位**是否符合特定準則。 支援的條件如下︰

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

使用 **like** 和 **notLike** 條件時，您可以在值中提供萬用字元 `*`。
值不能包含一個以上的萬用字元 `*`。

當使用 **match** 和 **notMatch** 條件時，請提供 `#` 來表示數字、`?` 來表示字母，以及任何其他字元來表示該實際字元。 如需範例，請參閱[允許多個名稱模式](scripts/allow-multiple-name-patterns.md)。

### <a name="fields"></a>欄位

條件是透過欄位所形成。 欄位會顯示用來描述資源狀態的資源要求裝載屬性。  

支援下列欄位：

- `name`
- `fullName`
  - 傳回資源的完整名稱。 資源的完整名稱是資源名稱前面加上任何父系資源名稱 (例如 "myServer/myDatabase")。
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - 其中 **\<tagName\>** 是要接受條件驗證的標籤名稱。
  - 範例：`tags.CostCenter`，其中 **CostCenter** 是標籤的名稱。
- `tags[<tagName>]`
  - 此括號語法支援包含句號的標籤名稱。
  - 其中 **\<tagName\>** 是要接受條件驗證的標籤名稱。
  - 範例：`tags.[Acct.CostCenter]`，其中 **Acct.CostCenter** 是標籤的名稱。
- 屬性別名 - 如需清單，請參閱[別名](#aliases)。

### <a name="effect"></a>效果

原則支援下列類型的效果：

- **Deny**：會在稽核記錄中產生事件，並讓要求失敗
- **Audit**：會在稽核記錄中產生事件，但不會讓要求失敗
- **Append**：會在要求中加入一組已定義的欄位
- **AuditIfNotExists**：如果資源不存在，便啟用稽核
- **DeployIfNotExists**：如果資源不存在，便部署該資源。 目前，僅支援透過內建原則達到這個效果。

對於 **append**，您必須提供下列詳細資料：

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

值可以是字串或 JSON 格式物件。

使用 **AuditIfNotExists** 及 **DeployIfNotExists** 時，您可以評估相關資源是否存在，並在該資源不存在時，套用規則及對應的效果。 例如，您可以要求網路監看員針對所有虛擬網路部署。
如需在未部署虛擬機器擴充功能時進行稽核的範例，請參閱[稽核擴充功能是否不存在](scripts/audit-ext-not-exist.md)。

如須每個效果的完整詳細資訊、評估順序、屬性和範例，請參閱[了解原則效果](policy-effects.md)。

### <a name="policy-functions"></a>原則函式

[Resource Manager 範本函式](../azure-resource-manager/resource-group-template-functions.md)的子集可用於原則規則中。 目前支援的函式如下︰

- [參數](../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [訂用帳戶](../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

此外，`field` 函式可用於原則規則。 此函數主要會與 **AuditIfNotExists** 和 **DeployIfNotExists** 搭配使用，來參考所評估資源上的欄位。 舉例來說，您可以在 [DeployIfNotExists 範例](policy-effects.md#deployifnotexists-example)上看到此情形。

#### <a name="policy-function-examples"></a>原則函式範例

此原則規則範例會使用 `resourceGroup` 資源函式來取得**名稱**屬性，並與 `concat` 陣列和物件函式結合來建置 `like` 條件，以強制資源名稱的開頭使用資源群組名稱。

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

此原則規則範例會使用 `resourceGroup` 資源函式來取得資源群組上 **CostCenter** 標記的**標記**屬性陣列值，並將此值附加至新資源上的 **CostCenter** 標記。

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>別名

您可以使用屬性別名來存取資源類型的特定屬性。 別名可讓您限制某個資源上的某個值所允許的值或條件。 每個別名會對應至指定資源類型之不同 API 版本中的路徑。 在原則評估期間，原則引擎會取得該 API 版本的屬性路徑。

別名清單會不斷成長。 若要了解 Azure 原則目前支援哪些別名，請使用以下其中一種方式：

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
    'Authorization'='Bearer ' + $token.AccessToken
  }

  # Create a splatting variable for Invoke-RestMethod
  $invokeRest = @{
    Uri = 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases'
    Method = 'Get'
    ContentType = 'application/json'
    Headers = $authHeader
  }

  # Invoke the REST API
  $response = Invoke-RestMethod @invokeRest

  # Create an List to hold discovered aliases
  $aliases = [System.Collections.Generic.List[pscustomobject]]::new()

  foreach ($ns in $response.value)
  {
      foreach ($rT in $ns.resourceTypes)
      {
          if ($rT.aliases)
          {
              foreach ($obj in $rT.aliases)
              {
                  $alias = [PSCustomObject]@{
                      Namespace    = $ns.namespace
                      resourceType = $rT.resourceType
                      alias        = $obj.name
                  }
                  $aliases.Add($alias)
              }
          }
      }
  }

  # Output the list and sort it by Namespace, resourceType and alias. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

## <a name="initiatives"></a>計畫

計畫可讓您將數個相關的原則定義組成群組來簡化指派和管理，因為您可以將一個群組當作單一項目來使用。 例如，您可以將所有相關的標籤原則定義組成單一計畫。 您可以套用該計畫，而不個別指派每個原則。

下列範例說明如何建立一個處理兩個標籤 (`costCenter` 和 `productName`) 的計畫。 它會使用兩個內建的原則來套用預設標籤值。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](json-samples.md)檢閱更多範例。
