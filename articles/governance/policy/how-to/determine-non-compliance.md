---
title: 判斷不符合的原因
description: 不符合規範的資源時，有許多可能的原因。 了解如何找出造成不符合規範的原因。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fb7f238bb5c04bb03ee500b1b953895cc88c0596
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298930"
---
# <a name="determine-causes-of-non-compliance"></a>判斷不符合的原因

當 Azure 資源會判定為不符合規範的原則規則時，最好了解哪一部分的資源不符合規範的規則。 它也是有助於您了解哪些變更會改變先前符合規範的資源，以讓它不符合規範。 有兩種方式可找到這項資訊：

> [!div class="checklist"]
> - [合規性詳細資料](#compliance-details)
> - [變更歷程記錄 （預覽）](#change-history-preview)

## <a name="compliance-details"></a>合規性詳細資料

不符合規範的資源時，該資源的合規性詳細資料都是從**原則合規性**頁面。 合規性詳細資料 窗格包含下列資訊：

- 資源詳細資料，例如名稱、 類型、 位置和資源識別碼
- 合規性狀態和上次評估目前的原則指派的時間戳記
- 一份_原因_資源不符合規範

> [!IMPORTANT]
> 為合規性詳細資料_不符合規範_資源會顯示目前的屬性值在該資源，使用者必須擁有**讀取**作業**型別**的資源。 例如，如果_不符合規範_資源**microsoft.compute/virtualmachines**然後使用者必須擁有**Microsoft.Compute/virtualMachines/read**作業。 如果使用者沒有必要的操作，則會顯示存取錯誤。

若要檢視合規性詳細資料，請遵循下列步驟：

1. 藉由按一下 [所有服務]  然後搜尋並選取 [原則]  ，在 Azure 入口網站中啟動 Azure 原則服務。

1. 上**概觀**或是**合規性**頁面上，選取中的原則**合規性狀態**也就是說_不符合規範_。

1. 底下**資源的合規性**索引標籤**原則合規性**頁面上，以滑鼠右鍵按一下，或選取省略符號中的資源**合規性狀態**也就是_不符合規範_。 然後選取**檢視合規性詳細資料**。

   ![檢視合規性詳細資料 選項](../media/determine-non-compliance/view-compliance-details.png)

1. **合規性詳細資料**窗格會顯示目前的原則指派至資源的最新評估資訊。 在此範例中，欄位**Microsoft.Sql/servers/version**發現_12.0_時預期的原則定義_14.0_。 如果資源是不符合規範的原因很多，每個會列出針對此窗格。

   ![合規性詳細資料窗格中的非合規性的原因](../media/determine-non-compliance/compliance-details-pane.png)

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

|`Reason` | 條件 |
|-|-|
|目前的值必須包含目標值，做為索引鍵。 |containsKey 或**不**notContainsKey |
|目前的值必須包含目標值。 |包含或**不**notContains |
|目前的值必須等於目標值。 |等於或**不**notEquals |
|目前的值必須小於目標值。 |較少或**不**greaterOrEquals |
|目前的值必須大於或等於目標值。 |greaterOrEquals 或**不**較少 |
|目前的值必須大於目標值。 |更新版本或是**不**lessOrEquals |
|目前的值必須是小於或等於目標值。 |lessOrEquals 或**不**更高 |
|目前的值必須存在。 |exists |
|目前的值必須是目標值。 |在或**不**notIn |
|目前的值必須與目標值類似。 |例如或**不**notLike |
|目前的值必須區分大小寫的比對目標值。 |比對或**不**notMatch |
|目前的值必須不區分大小寫的比對目標值。 |matchInsensitively 或**不**notMatchInsensitively |
|目前的值不得包含目標值做為索引鍵。 |notContainsKey 或**不**containsKey|
|目前的值必須包含目標值。 |notContains 或**不**包含 |
|目前的值不得等於目標值。 |notEquals 或**不**等於 |
|目前的值必須存在。 |**不**存在  |
|目前的值不能在目標值。 |notIn 或**不**中 |
|目前的值不能和目標值一樣。 |notLike 或**不**等 |
|目前的值必須不區分大小寫的比對目標值。 |notMatch 或**不**比對 |
|目前的值必須不區分大小寫的比對目標值。 |notMatchInsensitively 或**不**matchInsensitively |
|沒有相關的資源比對原則定義中的效果詳細資料。 |類型中定義的資源**then.details.type**中所定義的資源相關**如果**部分原則規則不存在。 |

## <a name="compliance-details-for-guest-configuration"></a>Guest 設定的合規性詳細資料

針對_稽核_中的原則_來賓設定_類別可能有多個評估 VM 內的設定和您要檢視每個設定的詳細資料。 例如，如果您要稽核的一份已安裝的應用程式並指派狀態是_不符合規範_，您必須知道哪一個特定的應用程式已遺失。

您也可能會無法存取直接登入 VM，但您要報告的 VM 為何_不符合規範_。 例如，您可能會稽核 Vm 已加入正確的網域和報告的詳細資料包括目前的網域成員資格。

### <a name="azure-portal"></a>Azure 入口網站

1. 藉由按一下 [所有服務]  然後搜尋並選取 [原則]  ，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 **概觀**或是**合規性**頁面上，選取包含 Guest 設定的原則定義任何計劃的原則指派的_不符合規範_。

1. 選取 _稽核_原則，在計劃中的_不符合規範_。

   ![檢視稽核定義詳細資料](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. 在 **資源的合規性**索引標籤上，提供下列資訊：

   - **名稱**-的客體設定指派名稱。
   - **父資源**層中的虛擬機器_不符合規範_狀態，表示所選的客體設定指派。
   - **資源類型**- _guestConfigurationAssignments_完整名稱。
   - **上次評估**-來賓設定服務收到通知的目標虛擬機器的狀態相關的 Azure 原則的最後一個時間。

   ![檢視合規性詳細資料](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. 選取中的客體設定指派名稱**名稱**若要開啟的資料行**資源合規性**頁面。

1. 選取 **檢視資源**以開啟頁面頂端的按鈕**來賓指派**頁面。

**來賓指派**頁面會顯示所有可用的合規性詳細資料。 在檢視中的每個資料列代表虛擬機器內執行評估。 在 **原因**資料行中，說明為何來賓指派詞語_不符合規範_會顯示。 例如，如果您要稽核 Vm 應該加入網域，請**原因**欄會顯示文字，包括目前的網域成員資格。

![檢視合規性詳細資料](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

您也可以檢視合規性詳細資料，從 Azure PowerShell。 首先，請確定您已安裝的來賓組態模組。

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

您可以檢視客體的所有指派的目前狀態的 VM，使用下列命令：

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

若只想檢視_原因_詞彙，描述 VM 為何_不符合規範_，傳回只有 [原因] 子屬性。

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

您也可以在範圍內的虛擬機器的來賓指派輸出合規性歷程記錄。 此命令輸出會包含 vm 的每份報表的詳細資料。

> [!NOTE]
> 輸出可能會傳回大量的資料。 建議您將輸出儲存在變數中。

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

若要簡化此檢視，請使用**ShowChanged**參數。 此命令的輸出只包含報告遵循合規性狀態的變更。

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>變更歷程記錄 （預覽）

新的一部分**公開預覽版**，過去 14 天的變更歷程記錄可供所有支援的 Azure 資源[完成模式刪除](../../../azure-resource-manager/complete-mode-deletion.md)。 變更歷程記錄會提供關於何時偵測到變更的詳細資料，以及每項變更的_視覺化差異_。 新增、 移除或更改資源管理員屬性時，會觸發變更偵測。

1. 藉由按一下 [所有服務]  然後搜尋並選取 [原則]  ，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 **概觀**或是**合規性**頁面上，選取原則，在任何**合規性狀態**。

1. 底下**資源的合規性**索引標籤**原則合規性**頁面上，選取資源。

1. 選取 [資源合規性]  頁面上的 [變更歷程記錄 (預覽)]  索引標籤。 偵測到的變更清單 (如果有的話) 會隨即顯示。

   ![在資源合規性 頁面上的 azure 原則變更歷程記錄 索引標籤](../media/determine-non-compliance/change-history-tab.png)

1. 選取其中一個偵測到的變更。 _視覺化 diff_的資源會顯示**修訂歷程記錄**頁面。

   ![Azure 原則變更歷程記錄視覺差異變更記錄 頁面上](../media/determine-non-compliance/change-history-visual-diff.png)

_視覺化差異_有助於識別資源的變更。 偵測到的變更可能不相關的資源目前的合規性狀態。

變更歷程記錄資料由提供[Azure 資源 Graph](../../resource-graph/overview.md)。 若要查詢 Azure 入口網站外部的這項資訊，請參閱[取得資源變更](../../resource-graph/how-to/get-resource-changes.md)。

## <a name="next-steps"></a>後續步驟

- 檢閱範例[「 Azure 原則範例](../samples/index.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 了解如何[取得合規性資料](getting-compliance-data.md)。
- 了解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。