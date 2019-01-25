---
title: 原則定義結構的詳細資料
description: 說明「Azure 原則」如何使用資源原則定義，藉由描述強制執行原則的時機及所產生的效果，為您組織中的資源建立慣例。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0fe15cc87e0d30f58dc26ae925efa6d65b243f5b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851652"
---
# <a name="azure-policy-definition-structure"></a>Azure 原則定義結構

「Azure 原則」會使用資源原則定義為資源建立慣例。 每個定義都會描述資源合規性，以及當資源不符合規範時應套用的效果。
藉由定義慣例，您可以控制成本以及更輕鬆地管理您的資源。 例如，您可以指定僅允許特定類型的虛擬機器。 或者，您可以要求所有資源都有特定標籤。 原則會由所有子資源繼承。 如果將某個原則套用至資源群組，它會適用於該資源群組中的所有資源。

Azure 原則所使用的結構描述位於此處：[https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

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

所有 Azure 原則範例都位於[原則範例](../samples/index.md)。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Mode

**Mode** 決定原則要評估哪些資源類型。 支援的模式如下：

- `all`：評估資源群組和所有資源類型
- `indexed`：只評估支援標記和位置的資源類型

我們建議您在大部分的情況下都將 **mode** 設定為 `all`。 透過入口網站使用 `all` 模式建立的所有原則定義。 如果您是使用 PowerShell 或 Azure CLI，則可手動指定 **mode** 參數。 如果原則定義未包含 **mode** 值，則在 Azure PowerShell 中會預設為 `all`，而在 Azure CLI 中會預設為 `null`。 `null` 模式與使用 `indexed` 來支援回溯相容性相同。

建立會強制執行標籤或位置的原則時，應該使用 `indexed`。 雖然並非必要，但它可防止不支援標籤和位置的資源在合規性結果中顯示為不符合規範。 有一個例外，就是**資源群組**。 原則如果會在資源群組上強制執行位置或標籤，就應該將 **mode** 設定為 `all`，並明確地以 `Microsoft.Resources/subscriptions/resourceGroup` 類型作為目標。 如需範例，請參閱[強制執行資源群組標籤](../samples/enforce-tag-rg.md)。

## <a name="parameters"></a>參數

參數可減少原則定義數量來幫助您簡化原則管理。 將參數想像成就像表單上的欄位一樣 – `name``address`、`city``state`。 這些參數一律保持不變，不過它們的值則會根據填寫表單的個人而有所變更。
在建立原則時，參數也是以相同的方式運作。 藉由在原則定義中納入參數，您便可以針對不同的案例使用不同的值，來重複使用該原則。

> [!NOTE]
> 您只能在一開始建立原則或計畫時，設定原則的參數定義或計畫定義。 之後便無法變更參數定義。
> 這可避免原則或計畫的現有指派間接地變成無效。

舉例來說，您可以定義一個原則來限制可部署資源的位置。
當您建立原則時，會宣告下列參數：

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

在中繼資料屬性內，您可以使用 **strongType** 在 Azure 入口網站內提供可複選的選項清單。 **strongType** 所允許的值目前包括：

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

建立方案或原則時，必須指定定義位置。 定義位置必須是管理群組或訂用帳戶。 此位置可決定方案或原則的指派範圍。 資源必須是定義位置階層內的直屬成員或其子系，才可作為指派的目標。

如果定義位置為：

- **訂用帳戶** - 只能將原則指派給該訂用帳戶內的資源。
- **管理群組** - 只能將原則指派給子管理群組與子訂用帳戶內的資源。 如果您打算將原則定義套用至數個訂用帳戶，則位置必須是包含那些訂用帳戶的管理群組。

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
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
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

使用 **like** 和 **notLike** 條件時，您可以在值中提供 `*` 萬用字元。
值不應包含多個 `*` 萬用字元。

使用 **match** 和 **notMatch** 條件時，請提供 `#` 來比對數字、`?` 來比對字母、`.` 來比對所有字元，以及任何其他字元來比對該實際字元。 如需範例，請參閱[允許數個名稱模式](../samples/allow-multiple-name-patterns.md)。

### <a name="fields"></a>欄位

條件是透過欄位所形成。 欄位會比對資源要求裝載中的屬性，並描述資源的狀態。

支援下列欄位：

- `name`
- `fullName`
  - 傳回資源的完整名稱。 資源的完整名稱是資源名稱前面加上任何父系資源名稱 (例如 "myServer/myDatabase")。
- `kind`
- `type`
- `location`
  - 針對不受特定位置限制的資源，請使用 **global**。 如需範例，請參閱[範例 - 允許的位置](../samples/allowed-locations.md)。
- `identity.type`
  - 傳回資源上所啟用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)的類型。
- `tags`
- `tags.<tagName>`
  - 其中 **\<tagName\>** 是要接受條件驗證的標籤名稱。
  - 範例：`tags.CostCenter`，其中 **CostCenter** 是標籤的名稱。
- `tags[<tagName>]`
  - 此括號語法支援包含句號的標籤名稱。
  - 其中 **\<tagName\>** 是要接受條件驗證的標籤名稱。
  - 範例：`tags[Acct.CostCenter]`，其中 **Acct.CostCenter** 是標籤的名稱。
- 屬性別名 - 如需清單，請參閱[別名](#aliases)。

### <a name="effect"></a>效果

原則支援下列類型的效果：

- **Deny**：會在活動記錄中產生事件，並讓要求失敗
- **Audit**：會在活動記錄中產生警告事件，但不會讓要求失敗
- **Append**：會在要求中加入一組已定義的欄位
- **AuditIfNotExists**：如果資源不存在，便啟用稽核
- **DeployIfNotExists**：如果資源不存在，便部署該資源
- **Disabled**：不會評估資源是否符合原則規則的規範

對於 **append**，您必須提供下列詳細資料：

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

值可以是字串或 JSON 格式物件。

**AuditIfNotExists** 和 **DeployIfNotExists**會評估相關資源是否存在並套用規則。 如果資源與規則不符，就會實作效果。 例如，您可以要求網路監看員針對所有虛擬網路部署。 如需詳細資訊，請參閱[稽核擴充功能是否不存在](../samples/audit-ext-not-exist.md)範例。

**DeployIfNotExists** 效果需要 **roleDefinitionId** 屬性 (在原則規則的 **details** 部分中)。 如需詳細資訊，請參閱[補救 - 設定原則定義](../how-to/remediate-resources.md#configure-policy-definition)。

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

如須每個效果的完整詳細資訊、評估順序、屬性和範例，請參閱[了解原則效果](effects.md)。

### <a name="policy-functions"></a>原則函式

有數個 [Resource Manager 範本函式](../../../azure-resource-manager/resource-group-template-functions.md) 可供在原則規則內使用。 目前支援的函式如下︰

- [參數](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [訂用帳戶](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

此外，`field` 函式可用於原則規則。 `field` 主要是與 **AuditIfNotExists** 和 **DeployIfNotExists** 搭配使用，以參考所評估資源上的欄位。 如需此用法的範例，請參閱 [DeployIfNotExists 範例](effects.md#deployifnotexists-example)。

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

您可以使用屬性別名來存取資源類型的特定屬性。 別名可讓您針對資源上的屬性限制所允許的值或條件。 每個別名會對應至指定資源類型之不同 API 版本中的路徑。 在原則評估期間，原則引擎會取得該 API 版本的屬性路徑。

別名清單會不斷成長。 若要了解「Azure 原則」目前支援哪些別名，請使用下列其中一種方法：

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzPolicyAlias -NamespaceMatch 'automation'
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

### <a name="understanding-the--alias"></a>了解 [*] 別名

許多可用的別名都有會一個顯示為「正常」名稱的版本，和另一個附加 **[\*]** 的版本。 例如︰

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

第一個範例用來評估整個陣列，而  **[\*]** 別名會評估陣列的每個元素。

讓我們看看原則規則的範例。 此原則會**拒絕**已設定 ipRules 且**沒有**任何 ipRules 值為 "127.0.0.1" 的儲存體帳戶。

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

此範例的 **IpRules** 陣列如下所示：

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

以下是此範例的處理方式：

- `networkAcls.ipRules` - 確認陣列不是 Null。 評估結果為 true，因此評估會繼續。

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` - 檢查 **ipRules** 陣列中每個_值_的屬性。

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - 作為陣列，每個元素都將進行處理。

    - "127.0.0.1" != "127.0.0.1" 會評估為 false。
    - "127.0.0.1" != "192.168.1.1" 會評估為 true。
    - **ipRules** 陣列中至少有一個_值_的屬性評估為 false 時，評估就會停止。

由於有條件被評估為 false，因此不會觸發 **Deny** 效果。

## <a name="initiatives"></a>計畫

計畫可讓您將數個相關的原則定義組成群組來簡化指派和管理，因為您可以將一個群組當作單一項目來使用。 例如，您可以將相關的標籤原則定義組成單一方案。 您可以套用該計畫，而不個別指派每個原則。

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

- 在 [Azure 原則範例](../samples/index.md)檢閱範例
- 檢閱[了解原則效果](effects.md)
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)
- 了解如何[取得合規性資料](../how-to/getting-compliance-data.md)
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)
- 檢閱[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)，以了解何謂管理群組