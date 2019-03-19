---
title: 判斷不符合的原因
description: 不符合規範的資源時，有許多可能的原因。 了解如何找出造成不符合規範的原因。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: dba8d9413229a0fa236b082e2e11dbd1a9fe5a5f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314121"
---
# <a name="determine-causes-of-non-compliance"></a>判斷不符合的原因

當 Azure 資源會判定為不符合規範的原則規則時，最好了解哪一部分的資源不符合規範的規則。 它也是有助於您了解哪些變更會改變先前符合規範的資源，以讓它不符合規範。 有兩種方式可找到這項資訊：

> [!div class="checklist"]
> - [合規性詳細資料](#compliance-details)
> - [變更歷程記錄 （預覽）](#change-history-preview)

## <a name="compliance-details"></a>相容性詳細資料

不符合規範的資源時，該資源的合規性詳細資料都是從**原則合規性**頁面。 合規性詳細資料 窗格包含下列資訊：

- 資源詳細資料，例如名稱、 類型、 位置和資源識別碼
- 合規性狀態和上次評估目前的原則指派的時間戳記
- 一份_原因_資源不符合規範

> [!IMPORTANT]
> 為合規性詳細資料_不符合規範_資源會顯示目前的屬性值在該資源，使用者必須擁有**讀取**作業**型別**的資源。 例如，如果_不符合規範_資源**microsoft.compute/virtualmachines**然後使用者必須擁有**Microsoft.Compute/virtualMachines/read**作業。 如果使用者沒有必要的操作，則會顯示存取錯誤。

若要檢視合規性詳細資料，請遵循下列步驟：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 上**概觀**或是**合規性**頁面上，選取中的原則**合規性狀態**也就是說_不符合規範_。

1. 底下**資源的合規性**索引標籤**原則合規性**頁面上，以滑鼠右鍵按一下，或選取省略符號中的資源**合規性狀態**也就是_不符合規範_。 然後選取**檢視合規性詳細資料**。

   ![檢視合規性詳細資料 選項](../media/determine-non-compliance/view-compliance-details.png)

1. **合規性詳細資料**窗格會顯示目前的原則指派至資源的最新評估資訊。 在此範例中，欄位**Microsoft.Sql/servers/version**發現_12.0_時預期的原則定義_14.0_。 如果資源是不符合規範的原因很多，每個會列出針對此窗格。

   ![合規性詳細資料窗格](../media/determine-non-compliance/compliance-details-pane.png)

   針對**auditIfNotExists**或是**deployIfNotExists**原則定義，詳細資料包括**details.type**屬性和任何選用的屬性。 如需清單，請參閱[auditIfNotExists 屬性](../concepts/effects.md#auditifnotexists-properties)並[deployIfNotExists 屬性](../concepts/effects.md#deployifnotexists-properties)。 **上次評估資源**是相關的資源，從**詳細資料**定義區段。

   範例部分**deployIfNotExists**定義：

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![合規性詳細資料 窗格-* ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 屬性值時，保護資料，_祕密_目前的值會顯示星號。

這些詳細資料解釋資源目前不符合規範，但不會顯示導致它變成不相容的資源進行變更時。 如需該資訊，請參閱[修訂歷程記錄 （預覽）](#change-history-preview)如下。

### <a name="compliance-reasons"></a>符合規範的原因

下列矩陣會將每一個可能的對應_原因_要負責[條件](../concepts/definition-structure.md#conditions)原則定義中：

|原因 | 條件 |
|-|-|
|目前的值必須包含作為索引鍵的目標值。 |containsKey 或**不**notContainsKey |
|目前的值必須包含目標值。 |包含或**不**notContains |
|目前的值必須等於目標值。 |等於或**不**notEquals |
|目前的值必須存在。 |exists |
|目前的值必須位於目標值內。 |在或**不**notIn |
|目前的值必須與目標值相同。 |例如或**不**notLike |
|目前的值必須符合目標值 (區分大小寫)。 |比對或**不**notMatch |
|目前的值必須符合目標值 (不區分大小寫)。 |matchInsensitively 或**不**notMatchInsensitively |
|目前的值不得包含作為索引鍵的目標值。 |notContainsKey 或**不**containsKey|
|目前的值不得包含目標值。 |notContains 或**不**包含 |
|目前的值不得等於目標值。 |notEquals 或**不**等於 |
|目前的值不得存在。 |**不**存在  |
|目前的值不得在目標值之中。 |notIn 或**不**中 |
|目前的值不得與目標值相同。 |notLike 或**不**等 |
|目前的值不得符合目標值 (區分大小寫)。 |notMatch 或**不**比對 |
|目前的值不得符合目標值 (不區分大小寫)。 |notMatchInsensitively 或**不**matchInsensitively |
|沒有任何相關的資源，符合原則定義中的效果詳細資料。 |類型中定義的資源**then.details.type**中所定義的資源相關**如果**部分原則規則不存在。 |

## <a name="change-history-preview"></a>變更歷程記錄 (預覽)

做為一部分的新**公開預覽版**、 過去 14 天變更的歷程記錄是適用於所有支援的 Azure 資源[完成模式刪除](../../../azure-resource-manager/complete-mode-deletion.md)。 變更歷程記錄會提供關於何時偵測到變更的詳細資料，以及每項變更的_視覺化差異_。 新增、 移除或更改資源管理員屬性時，會觸發變更偵測。

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 **概觀**或是**合規性**頁面上，選取原則，在任何**合規性狀態**。

1. 底下**資源的合規性**索引標籤**原則合規性**頁面上，選取資源。

1. 選取 [資源合規性] 頁面上的 [變更歷程記錄 (預覽)] 索引標籤。 偵測到的變更清單 (如果有的話) 會隨即顯示。

   ![原則變更歷程記錄 - 索引標籤](../media/determine-non-compliance/change-history-tab.png)

1. 選取其中一個偵測到的變更。 _視覺化 diff_的資源會顯示**修訂歷程記錄**頁面。

   ![原則變更歷程記錄 - 視覺化差異](../media/determine-non-compliance/change-history-visual-diff.png)

_視覺化差異_有助於識別資源的變更。 偵測到的變更可能不相關的資源目前的合規性狀態。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例
- 檢閱[原則定義結構](../concepts/definition-structure.md)
- 檢閱[了解原則效果](../concepts/effects.md)
- 了解如何[以程式設計方式建立原則](programmatically-create.md)
- 了解如何[取得合規性資料](getting-compliance-data.md)
- 了解如何[補救不符合規範的資源](remediate-resources.md)
- 檢閱[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)，以了解何謂管理群組