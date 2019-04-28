---
title: 了解部署順序
description: 了解藍圖定義將會經歷的生命週期和每個階段的詳細資料。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b05a7ce260e8cc1da4ac8a0c186694ae097a3b1e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766347"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>了解 Azure 藍圖中的部署順序

Azure 藍圖會使用**排序順序**處理指派藍圖定義時，決定資源的建立順序。 本文說明下列概念：

- 使用的預設排序順序
- 如何自訂順序
- 如何處理自訂的順序

在 JSON 範例中有一些變數。您需要使用自己的值取代它們：

- `{YourMG}` - 以您的管理群組名稱取代

## <a name="default-sequencing-order"></a>預設排序順序

如果藍圖定義包含才能部署成品沒有指示詞，或指示詞為 null，則會使用下列順序：

- 訂用帳戶層級**角色指派**成品，依成品名稱排序
- 訂用帳戶層級**原則指派**成品，依成品名稱排序
- 訂用帳戶層級 **Azure Resource Manager 範本**成品，依成品名稱排序
- **資源群組**成品 (包含子成品)，依預留位置名稱排序

在每個**資源群組**成品中，下列排序順序會適用於要在該資源群組內建立的成品：

- 資源群組子**角色指派**成品，依成品名稱排序
- 資源群組子**原則指派**成品，依成品名稱排序
- 資源群組子 **Azure Resource Manager 範本**成品，依成品名稱排序

> [!NOTE]
> 利用[artifacts()](../reference/blueprint-functions.md#artifacts)會建立隱含的相依性所參考的成品。

## <a name="customizing-the-sequencing-order"></a>自訂排序順序

當您在撰寫大型藍圖定義，可能必須在以特定順序中建立的資源。 此案例的最常見的使用模式時，藍圖定義包含數個 Azure Resource Manager 範本。 藍圖會藉由允許定義排序順序來處理此模式。

排序可透過在 JSON 中定義 `dependsOn` 屬性來完成。 藍圖定義中，資源群組和成品物件支援此屬性。 `dependsOn` 為成品名稱字串陣列，代表在其建立前必須建立的特定成品。

### <a name="example---ordered-resource-group"></a>範例-排序資源群組

此範例藍圖定義有資源群組宣告的值定義自訂的排序順序`dependsOn`，以及標準的資源群組。 在此範例中，名為 **assignPolicyTags** 的成品會在 **ordered-rg** 資源群組之前進行處理。
**standard-rg** 會根據預設排序順序進行處理。

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>範例 - 使用自訂順序的成品

此範例是相依於 Azure Resource Manager 範本的原則成品。 根據預設順序，原則成品會在 Azure Resource Manager 範本之前建立。 這順序可讓原則成品等待建立 Azure Resource Manager 範本。

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>範例-根據資源群組的訂用帳戶層級範本成品

這個範例是在訂用帳戶層級取決於資源群組部署的 Resource Manager 範本。 在預設排序，會在任何資源群組和資源群組中的子成品之前建立的訂用帳戶層級成品。 像這樣的 blueprint （藍圖） 定義中定義的資源群組：

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

取決於訂用帳戶層級範本成品**等候-對-我**資源群組定義如下：

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>處理自訂順序

在建立過程期間，會使用拓撲排序來建立藍圖成品的相依性關係圖。 此檢查可確保支援資源群組與成品之間的每個相依性層級。

如果成品相依性宣告為不會改變預設順序，則不會進行任何變更。 有個範例是相依於訂用帳戶層級原則的資源群組。 另一個範例則是相依於資源群組 ’standard-rg’ 子角色指派的資源群組 ’standard-rg’ 子原則指派。 在這兩個案例中，`dependsOn` 皆不會改變預設排序順序，因此不會產生任何變更。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](lifecycle.md)。
- 了解如何使用[靜態與動態參數](parameters.md)。
- 了解如何使用[藍圖資源鎖定](resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。