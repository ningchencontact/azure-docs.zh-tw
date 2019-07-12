---
title: 建立、 檢視及管理在 Azure 監視器中的活動記錄警示
description: 使用 Azure 入口網站、 Azure Resource Manager 範本和 Azure PowerShell 來建立活動記錄警示。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705287"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>建立、 檢視及使用 Azure 監視器管理活動記錄警示  

## <a name="overview"></a>總覽
活動記錄警示是發生符合警示中指定條件的新活動記錄事件時所啟動的警示。

這些警示是適用於 Azure 資源，並可由使用 Azure Resource Manager 範本。 也可以在 Azure 入口網站中將它們建立、更新或刪除。 一般而言，您建立活動記錄警示給您的 Azure 訂用帳戶中的資源發生特定變更時收到通知。 警示通常限於特定的資源群組或資源。 比方說，您可能想要時有任何通知的範例資源群組中的虛擬機器**myProductionResourceGroup**會被刪除。 或者，您可能想要任何新角色指派給您的訂用帳戶中的使用者時收到通知。

> [!IMPORTANT]
> 無法透過活動記錄警示建立的介面建立服務健康情況通知的警示。 若要深入了解如何建立和使用服務健康情況通知，請參閱[接收服務健康狀態通知活動記錄警示](alerts-activity-log-service-notifications.md)。

當您建立警示規則時，請確定下列各項：

- 範圍中的訂用帳戶不是不同的訂用帳戶建立警示的位置。
- 準則中必須是層級、 狀態、 呼叫端、 資源群組、 資源識別碼或在其設定警示的資源類型事件類別目錄。
- 沒有"anyOf"條件或警示設定 JSON 中的巢狀的條件。 基本上，只有一個"allOf"條件允許使用任何進一步"allOf"或"anyOf"條件。
- 「 系統管理 」 類別目錄時，您必須在警示中指定至少一個上述準則。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。


## <a name="azure-portal"></a>Azure 入口網站

您可以使用 Azure 入口網站來建立和修改活動記錄警示規則。 Azure 活動記錄，以確保無縫式警示建立特定的事件感興趣的整合體驗。

### <a name="create-with-the-azure-portal"></a>使用 Azure 入口網站建立

使用下列程序。

1. 在 Azure 入口網站中，選取**監視器** > **警示**。
2. 選取 **新的警示規則**的左上角**警示**視窗。

     ![新增警示規則](media/alerts-activity-log/AlertsPreviewOption.png)

     [建立規則]  視窗隨即出現。

      ![新增警示規則選項](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 底下**定義警示條件**，提供下列資訊，然後選取**完成**:

   - **警示目標：** 若要檢視和選取新的警示的目標，請使用**依訂用帳戶篩選** / **依資源類型篩選**。 從顯示的清單中選取的資源或資源群組。

     > [!NOTE]
     > 
     > 您可以選取資源、 資源群組或整個訂用帳戶的活動記錄訊號。

     **警示目標範例檢視**

     ![選取目標](media/alerts-activity-log/select-target.png)

   - 底下**目標準則**，選取**新增條件**。 顯示所有可用的訊號，目標，其中包含來自各種類別目錄的**活動記錄**。 類別目錄名稱會附加至**監視服務**名稱。

   - 從類型 [活動記錄]  的各種可能作業顯示的清單中選取訊號。

     您可以選取此目標訊號的記錄歷程記錄時間軸和對應的警示邏輯：

     **新增準則畫面**

     ![新增準則](media/alerts-activity-log/add-criteria.png)

     - **歷程記錄時間**：最後 6、 12，或 24 小時或過去一週，則可以繪製事件以供選取的作業。

     - **警示邏輯**：

       - **事件層級**:事件的嚴重性層級：詳細資訊_資訊_ _警告_ _錯誤_或_嚴重_。
       - **狀態**：事件的狀態：已啟動_失敗_或_成功_。
       - **事件起始者**：也就是呼叫端。 執行作業之使用者的電子郵件地址或 Azure Active Directory 識別碼。

       此範例訊號圖表已經套用警示邏輯：

       ![選取準則](media/alerts-activity-log/criteria-selected.png)

4. 底下**定義警示詳細資料**，提供下列詳細資料：

    - **警示規則名稱**:新的警示規則的名稱。
    - **描述**：新的警示規則的描述。
    - **將警示儲存到資源群組**:選取您要儲存此新規則的資源群組。

5. 在 [動作群組]  下，從下拉式功能表中指定您想要指派給此新警示規則的動作群組。 或者，[建立新的動作群組](../../azure-monitor/platform/action-groups.md)並將它指派給新規則。 若要建立新的群組，請選取 **+ 新增群組**。

6. 若要啟用的規則，您在建立之後，選取 **[是]** for**在建立時啟用規則**選項。
7. 選取 **建立警示規則**。

    建立活動記錄檔的新警示規則時，並確認訊息出現在視窗的右上角。

    您可以啟用、停用、編輯或刪除規則。 深入了解如何管理活動記錄規則。


簡單的比喻，可以在活動記錄檔中建立警示規則所在的了解狀況，就是瀏覽或篩選事件，透過[在 Azure 入口網站中的活動記錄](activity-log-view.md#azure-portal)。 在 [ **Azure 監視-活動記錄**畫面中，您可以篩選或找出所需的事件和使用，以建立警示**新增活動記錄警示**] 按鈕。 然後遵循所示的步驟 4 到 7，像之前一樣。
    
 ![新增活動記錄檔中的警示](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>檢視及管理在 Azure 入口網站

1. 在 Azure 入口網站中，選取**監視器** > **警示**。 選取 **管理警示規則**視窗左上角。

    ![管理警示規則](media/alerts-activity-log/manage-alert-rules.png)

    可用規則清單隨即出現。

2. 搜尋要修改的活動記錄規則。

    ![搜尋活動記錄警示規則](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    您可以使用可用的篩選器_訂用帳戶_，_資源群組_，_資源_，_訊號類型_，或_狀態_，若要尋找您想要編輯的活動規則。

   > [!NOTE]
   > 
   > 您可以只編輯**描述**，**目標準則**，並**動作群組**。

3. 選取規則，然後按兩下以編輯規則選項。 進行必要的變更，然後按**儲存**。

   ![管理警示規則](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 您可以啟用、 停用，或刪除規則。 步驟 2 中所述，選取規則之後，請選取適當的選項，在視窗的頂端。


## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
若要使用 Azure Resource Manager 範本建立活動記錄警示，您會建立型別的資源`microsoft.insights/activityLogAlerts`。 然後要填入所有相關的屬性。 以下是建立活動記錄警示範本：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
先前的範例 JSON 可以儲存為，比方說，sampleActivityLogAlert.json 基於本逐步解說的目的，而且可以部署使用[在 Azure 入口網站中的 Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md)。

> [!NOTE]
> 它可能需要 5 分鐘的時間的新活動記錄警示規則變成使用中。

## <a name="rest-api"></a>REST API 
[Azure 監視器活動記錄警示 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts)是 REST API。 它是使用 Azure Resource Manager REST API 完全相容。 它可以使用 PowerShell 透過使用 Resource Manager cmdlet 或 Azure CLI。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>部署 Resource Manager 範本，使用 PowerShell
若要使用 PowerShell 來部署先前所示的範例 Resource Manager 範本[Azure Resource Manager 範本](#azure-resource-manager-template)區段中，使用下列命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中 sampleActivityLogAlert.parameters.json 包含提供給警示規則建立作業所需的參數的值。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活動記錄檔的 PowerShell cmdlet

活動記錄警示有專用的 PowerShell Cmdlet 可供使用：

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert):建立新的活動記錄警示，或更新現有的活動記錄警示。
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert):取得一或多個活動記錄警示的資源。
- [啟用 AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert):啟用現有活動記錄警示並設定其標籤。
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert):停用現有活動記錄警示，並設定其標籤。
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert):移除活動記錄警示。

## <a name="azure-cli"></a>Azure CLI

[az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) 集合底下的專用 Azure CLI 命令可用來管理活動記錄警示規則。

若要建立新的活動記錄警示規則，請依此順序使用下列命令：

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：建立新的活動記錄警示規則資源。
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)：加入建立的活動記錄警示規則的範圍。
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)：加入活動記錄警示規則的動作群組。

若要擷取一個活動記錄警示規則的資源，使用 Azure CLI 命令[az 監視器活動記錄警示顯示](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)。 若要檢視資源群組中的所有活動記錄警示規則的資源，請使用[az 監視器活動記錄警示的清單](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
活動記錄警示規則的資源可以使用 Azure CLI 命令來移除[az 監視器活動記錄警示刪除](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)。

## <a name="next-steps"></a>後續步驟

- 深入了解[webhook 結構描述的活動記錄](../../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 讀取[活動記錄概觀](../../azure-monitor/platform/activity-log-alerts.md)。
- 深入了解[動作群組](../../azure-monitor/platform/action-groups.md)。  
- 深入了解[服務健康狀態通知](../../azure-monitor/platform/service-notifications.md)。
