---
title: Azure 資源上的陣列內容的作者原則
description: 了解建立陣列參數建立陣列的規則運算式語言、 評估 [*] 別名，並將項目附加至現有的陣列與 Azure 原則定義的規則。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 62267a4549355212a18654ff9781b2164ba19fa9
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2019
ms.locfileid: "57860246"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure 資源上的陣列內容的作者原則

Azure 資源管理員屬性通常會定義為字串和布林值。 一對多關聯性存在時，複雜屬性改為定義為陣列。 Azure 原則，在陣列使用數個不同的方式：

- 型別[definition 參數](../concepts/definition-structure.md#parameters)，以提供多個選項
- 一部分[原則規則](../concepts/definition-structure.md#policy-rule)使用條件**中**或**notIn**
- 原則規則，來評估的一部分[ \[ \* \]別名](../concepts/definition-structure.md#understanding-the--alias)來評估特定案例，例如**None**，**任何**，或**所有**
- 在 [附加效果](../concepts/effects.md#append)來取代或新增至現有的陣列

本文涵蓋每次使用 Azure 原則，並提供數個範例中定義。

## <a name="parameter-arrays"></a>參數陣列

### <a name="define-a-parameter-array"></a>定義參數陣列

做為陣列中定義的參數可讓原則的彈性，需要多個值時。
此原則定義可讓任何單一位置參數**allowedLocations**預設值為  _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

作為**型別**已_字串_，只有一個值可以設定時指派原則。 此原則指派，如果範圍中的資源，才允許在單一 Azure 區域內。 大部分的原則定義需要的核准選項，例如允許清單允許_eastus2_， _eastus_，並_westus2_。

若要建立原則定義，以允許多個選項，請使用_陣列_**型別**。 相同的原則可以改寫如下：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> 儲存原則定義時，一旦**型別**參數上的屬性不能變更。

這個新的參數定義會在指派原則期間接受一個以上的值。 具有陣列屬性**allowedValues**定義，在指派期間可用的值會進一步限制為預先定義的選擇清單。 利用**allowedValues**是選擇性的。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>將值傳遞給參數陣列中，在指派期間

當指派的原則，透過 Azure 入口網站的參數**型別**_陣列_會顯示為單一的文字方塊中。 此提示會指出 「 使用;若要分隔值。 （例如： 倫敦;New York) 」。 要傳遞的允許的位置值_eastus2_， _eastus_，並_westus2_給參數，使用下列字串：

`eastus2;eastus;westus2`

使用 Azure CLI、 Azure PowerShell 或 REST API 時，不同的參數值的格式。 值會傳遞 JSON 字串，其中也包含參數的名稱。

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

若要使用這個字串使用每個 SDK，使用下列命令：

- Azure CLI：命令[建立原則指派，az](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)參數與**params**
- Azure PowerShell：Cmdlet[新增 AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment)參數與**PolicyParameter**
- REST API：在 _放_[建立](/rest/api/resources/policyassignments/create)的值做為要求主體一部分的作業**properties.parameters**屬性

## <a name="policy-rules-and-arrays"></a>原則規則和陣列

### <a name="array-conditions"></a>陣列的條件

原則規則[條件](../concepts/definition-structure.md#conditions)可_陣列_
**型別**可能使用的參數使用僅限於`in`和`notIn`。 採取下列原則定義條件`equals`做為範例：

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

如果您嘗試建立此原則定義，透過 Azure 入口網站的潛在客戶，為錯誤，例如此錯誤訊息：

- 「 原則 '{GUID}' 無法參數化因為驗證錯誤。 請檢查原則參數是否已正確定義。 內部例外狀況 ' 評估語言運算式 '[parameters('allowedLocations')]' 的結果是類型 'Array'，類型應為 'String' '。 」

預期**型別**的條件`equals`是_字串_。 由於**allowedLocations**指**型別**_陣列_，原則引擎會評估語言運算式，並擲回的錯誤。 與`in`並`notIn`條件的原則引擎預期**型別**_陣列_語言運算式中。 若要解決此錯誤訊息，請變更`equals`設為`in`或`notIn`。

### <a name="evaluating-the--alias"></a>評估 [*] 別名

有的別名 **[\*]** 附加至其名稱表示**類型**是_陣列_。 而不是評估整個陣列中的值 **[\*]** 讓您能夠評估陣列的每個項目。 有三種情況，在每個項目評估這非常有用：無、 任何項目，以及所有。

原則引擎觸發程序**生效**中**然後**時，才**如果**規則會評估為 true。
這項事實，務必了解在內容中的方式 **[\*]** 評估陣列的每個個別項目。

原則的範例規則案例表：

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

**IpRules**陣列如下所示的案例表：

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

針對下列每個條件範例中，取代`<field>`與`"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`。

條件的範例原則規則和上述的現有值的陣列組成的結果的結果如下：

|條件 |結果 |說明 |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |執行任何動作 |一個陣列項目評估為 false (127.0.0.1 ！ = 127.0.0.1)，另一個為 true (127.0.0.1 ！ = 192.168.1.1)，因此**notEquals**條件_false_和效果不會觸發。 |
|`{<field>,"notEquals":"10.0.4.1"}` |原則效果 |這兩個陣列項目評估為 true (10.0.4.1 ！ = 127.0.0.1 與 10.0.4.1 ！ = 192.168.1.1)，因此**notEquals**條件 _，則為 true_和效果，就會觸發。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |原則效果 |一個陣列項目都評估為 true (127.0.0.1 = = 127.0.0.1)，另一個為 false (127.0.0.1 = = 192.168.1.1)，因此**Equals**條件_false_。 邏輯運算子會評估為 true (**未** _false_)，因此效果，就會觸發。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |原則效果 |這兩個陣列項目評估為 false (10.0.4.1 = = 127.0.0.1 與 10.0.4.1 = = 192.168.1.1)，因此**Equals**條件_false_。 邏輯運算子會評估為 true (**未** _false_)，因此效果，就會觸發。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |原則效果 |一個陣列項目評估為 false (127.0.0.1 ！ = 127.0.0.1)，另一個為 true (127.0.0.1 ！ = 192.168.1.1)，因此**notEquals**條件_false_。 邏輯運算子會評估為 true (**未** _false_)，因此效果，就會觸發。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |執行任何動作 |這兩個陣列項目評估為 true (10.0.4.1 ！ = 127.0.0.1 與 10.0.4.1 ！ = 192.168.1.1)，因此**notEquals**條件 _，則為 true_。 邏輯運算子會評估為 false (**未** _，則為 true_)，因此不會觸發效果。 |
|`{<field>,"Equals":"127.0.0.1"}` |執行任何動作 |一個陣列項目都評估為 true (127.0.0.1 = = 127.0.0.1)，另一個為 false (127.0.0.1 = = 192.168.1.1)，因此**Equals**條件_false_和效果不會觸發。 |
|`{<field>,"Equals":"10.0.4.1"}` |執行任何動作 |這兩個陣列項目評估為 false (10.0.4.1 = = 127.0.0.1 與 10.0.4.1 = = 192.168.1.1)，因此**Equals**條件_false_和效果不會觸發。 |

## <a name="the-append-effect-and-arrays"></a>附加的效果和陣列

[附加作用](../concepts/effects.md#append)的行為會根據 if **details.field**會 **[\*]** 別名或不。

- 若未 **[\*]** 別名，附加會取代整個陣列**值**屬性
- 當 **[\*]** 別名，附加新增**值**至現有的屬性陣列，或建立新的陣列

如需詳細資訊，請參閱 <<c0> [ 附加範例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例
- 檢閱[原則定義結構](../concepts/definition-structure.md)
- 檢閱[了解原則效果](../concepts/effects.md)
- 了解如何[以程式設計方式建立原則](programmatically-create.md)
- 了解如何[補救不符合規範的資源](remediate-resources.md)
- 檢閱[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)，以了解何謂管理群組