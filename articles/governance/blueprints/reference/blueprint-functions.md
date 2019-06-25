---
title: Azure 藍圖函式
description: 描述可搭配 Azure 藍圖定義和指派的函數。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209418"
---
# <a name="functions-for-use-with-azure-blueprints"></a>函式，以搭配 Azure 藍圖

Azure 藍圖提供製作藍圖定義更動態的函式。 這些函式與藍圖定義搭配使用，藍圖成品。 Resource Manager 範本的成品支援完整的 Resource Manager 函式，除了透過藍圖參數取得動態值使用。

支援下列功能：

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>成品

`artifacts(artifactName)`

傳回物件的屬性填入該藍圖成品輸出。

### <a name="parameters"></a>參數

| 參數 | 必要項 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| artifactName |是 |string |藍圖成品的名稱。 |

### <a name="return-value"></a>傳回值

輸出屬性的物件。 **輸出**屬性不相依於的 blueprint （藍圖） 所參考的成品類型。 所有類型中，請依照都下列格式：

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>原則指派成品

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Resource Manager 範本的成品

**輸出**傳回物件的屬性是在 Resource Manager 範本中定義和部署作業所傳回。

#### <a name="role-assignment-artifact"></a>角色指派成品

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>範例

識別碼是 Resource Manager 範本的成品_myTemplateArtifact_包含下列範例輸出屬性：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

擷取資料的一些範例_myTemplateArtifact_範例：

| 運算是 | 類型 | 值 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | 陣列 | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | string | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | string | 「 我的字串值" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | string | 「 我的值 」 |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

結合多個字串值，並傳回串連的字串。

### <a name="parameters"></a>參數

| 參數 | 必要項 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| string1 |是 |string |串連的第一個值。 |
| 其他引數 |否 |string |額外的值串連的循序順序 |

### <a name="return-value"></a>傳回值

串連值的字串。

### <a name="remarks"></a>備註

此 Azure blueprint （藍圖） 函式與不同 Azure Resource Manager 範本函式，因為它僅適用於字串。

### <a name="example"></a>範例

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

傳回藍圖參數值。 藍圖定義中或藍圖成品，則必須定義指定的參數名稱。

### <a name="parameters"></a>參數

| 參數 | 必要項 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |string |要傳回的參數名稱。 |

### <a name="return-value"></a>傳回值

指定的藍圖或藍圖成品參數的值。

### <a name="remarks"></a>備註

此 Azure blueprint （藍圖） 函式會不同於 Azure Resource Manager 範本函式，因為它只能配合藍圖參數的範圍。

### <a name="example"></a>範例

定義參數_principalIds_ blueprint （藍圖） 定義中：

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

然後使用_principalIds_做為引數`parameters()`藍圖成品中：

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

傳回代表目前資源群組的物件。

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>備註

此 Azure blueprint （藍圖） 函式不同於 Azure Resource Manager 範本函式。 `resourceGroup()`函式不能在訂用帳戶層級成品或藍圖定義。 它只能在屬於資源群組成品的藍圖成品。

常見用法`resourceGroup()`函式是在資源群組成品的相同位置中建立資源。

### <a name="example"></a>範例

若要使用資源群組的位置，集中藍圖定義或指派，做為其他的成品的位置期間宣告 blueprint （藍圖） 定義中的資源群組預留位置物件。 在此範例中， _NetworkingPlaceholder_是資源群組預留位置名稱。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

然後使用`resourceGroup()`藍圖成品的目標資源群組預留位置物件內容中的函式。 在此範例中，範本的成品部署到_NetworkingPlaceholder_資源群組，並提供參數_resourceLocation_以動態方式填入_NetworkingPlaceholder_範本的資源群組位置。 位置_NetworkingPlaceholder_資源群組可能已以靜態方式定義上定義 blueprint （藍圖） 或在指派期間以動態方式定義。 在任一情況下，範本的成品會提供該資訊做為參數，並使用它來將資源部署至正確的位置。

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

傳回物件，表示指定的資源群組成品。 不同於`resourceGroup()`需要內容的成品，此函數用來取得不在該資源群組的內容時的特定資源群組預留位置的屬性。

### <a name="parameters"></a>參數

| 參數 | 必要項 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| placeholderName |是 |string |要傳回之資源群組成品的預留位置名稱。 |

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>範例

若要使用資源群組的位置，集中藍圖定義或指派，做為其他的成品的位置期間宣告 blueprint （藍圖） 定義中的資源群組預留位置物件。 在此範例中， _NetworkingPlaceholder_是資源群組預留位置名稱。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

然後使用`resourceGroups()`函式，從任何藍圖成品，以取得資源群組預留位置物件的參考內容。 在此範例中，範本的成品部署外_NetworkingPlaceholder_資源群組，並提供參數_artifactLocation_以動態方式填入_NetworkingPlaceholder_範本的資源群組位置。 位置_NetworkingPlaceholder_資源群組可能已以靜態方式定義上定義 blueprint （藍圖） 或在指派期間以動態方式定義。 在任一情況下，範本的成品會提供該資訊做為參數，並使用它來將資源部署至正確的位置。

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

傳回目前的藍圖指派的訂用帳戶的相關詳細資料。

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>範例

使用訂用帳戶的顯示名稱和`concat()`函式來建立命名慣例，傳遞做為參數_resourceName_範本成品。

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。