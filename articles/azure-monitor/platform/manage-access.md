---
title: 在 Azure 監視器中管理 Log Analytics 工作區 | Microsoft Docs
description: 您可以在「Azure 監視器」中使用有關使用者、帳戶、工作區及 Azure 帳戶的各種系統管理工作來管理 Log Analytics 工作區。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: magoedte
ms.openlocfilehash: c6fa4df1fb2fc7559f706d81621ea198f5ca7cdc
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881432"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>管理 Azure 監視器中的記錄資料和工作區

Azure 監視器會將[記錄](data-platform-logs.md)資料儲存在 log Analytics 工作區中, 這基本上是包含資料和設定資訊的容器。 若要記錄管理資料的存取權, 您可以執行與工作區相關的各種系統管理工作。

本文說明如何記錄管理的存取權, 以及管理包含它們的工作區, 包括:

* 如何使用 Azure 角色型存取控制 (RBAC), 將存取權授與需要從特定資源存取記錄資料的使用者。

* 如何使用工作區許可權授與工作區的存取權。

* 如何使用 Azure RBAC, 將存取權授與需要存取工作區中特定資料表內之記錄資料的使用者。

## <a name="define-access-control-mode"></a>定義存取控制模式

您可以從 Azure 入口網站或透過 Azure PowerShell, 查看工作區上設定的存取控制模式。  您可以使用下列其中一種支援的方法來變更此設定:

* Azure 入口網站

* Azure PowerShell

* Azure Resource Manager 範本

### <a name="configure-from-the-azure-portal"></a>從 Azure 入口網站設定

您可以在**Log Analytics 工作區**功能表中, 于工作區的 [**總覽**] 頁面上, 查看目前的工作區存取控制模式。 

![View workspace 存取控制模式](media/manage-access/view-access-control-mode.png)

1. 在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。
1. 在 Azure 入口網站中, 選取工作區 > 的 Log Analytics 工作區。  

您可以從工作區的 [**屬性**] 頁面變更此設定。 如果您沒有設定工作區的許可權, 將會停用變更設定。

![變更工作區存取模式](media/manage-access/change-access-control-mode.png)

### <a name="configure-using-powershell"></a>使用 PowerShell 進行設定

使用下列命令來檢查訂用帳戶中所有工作區的存取控制模式:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

輸出應該如以下所示：

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

值為時`False` , 表示工作區是使用工作區內容存取模式來設定。  值為時`True` , 表示工作區是以資源內容存取模式來設定。 

>[!NOTE]
>如果傳回的工作區沒有布林值, 而且是空的, 這也會符合`False`值的結果。
>

使用下列腳本, 將特定工作區的存取控制模式設定為資源內容許可權:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用下列腳本, 將訂用帳戶中所有工作區的存取控制模式設定為資源內容許可權:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="configure-using-a-resource-manager-template"></a>使用 Resource Manager 範本進行設定

若要在 Azure Resource Manager 範本中設定存取模式, 請將工作區上的**enableLogAccessUsingOnlyResourcePermissions**功能旗標設為下列其中一個值。

* **false**:將工作區設定為工作區內容許可權。 如果未設定旗標, 這就是預設設定。
* **true**:將工作區設定為資源內容許可權。

## <a name="manage-accounts-and-users"></a>管理帳戶和使用者

適用于特定使用者工作區的許可權是由其[存取模式](design-logs-deployment.md#access-mode)和工作區的[存取控制模式](design-logs-deployment.md#access-control-mode)所定義。 使用**工作區內容**時, 您可以在工作區中查看您有許可權的所有記錄, 因為此模式中的查詢會將範圍設定為工作區中所有資料表的所有資料。 使用**資源內容**時, 您會在可存取的 Azure 入口網站中, 直接從資源執行搜尋時, 看到特定資源、資源群組或訂用帳戶工作區中的記錄資料。 此模式中的查詢範圍僅限於與該資源相關聯的資料。

### <a name="workspace-permissions"></a>工作區許可權

每個工作區可以有多個相關聯的帳戶，而每個帳戶可以存取多個工作區。 使用[Azure 角色型存取](../../role-based-access-control/role-assignments-portal.md)來管理存取權。

下列活動也需要 Azure 權限︰

|Action |所需的 Azure 權限 |注意 |
|-------|-------------------------|------|
| 新增和移除監視解決方案 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 必須在資源群組或訂用帳戶層級授與這些權限。 |
| 變更定價層 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 檢視 [備份] 和 [Site Recovery] 解決方案圖格中的資料 | 系統管理員/共同管理員 | 存取使用傳統部署模型所部署的資源 |
| 在 Azure 入口網站中建立工作區 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| 在入口網站中查看工作區基本屬性並輸入工作區分頁 | `Microsoft.OperationalInsights/workspaces/read` ||
| 使用任何介面查詢記錄 | `Microsoft.OperationalInsights/workspaces/query/read` ||
| 使用查詢存取所有記錄類型 | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 存取特定的記錄資料表 | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| 讀取工作區金鑰以允許將記錄傳送到此工作區 | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>使用 Azure 許可權來管理存取權

若要使用 Azure 權限授與 Log Analytics 工作區的存取權，請遵循[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)中的步驟。

Azure 有兩個內建的 Log Analytics 工作區角色：

* Log Analytics 讀取者
* Log Analytics 參與者

*Log Analytics 讀者*角色的成員可以：

* 檢視及搜尋所有監視資料
* 檢視監視設定，包括檢視所有 Azure 資源上檢視的 Azure 診斷組態。

Log Analytics 讀者角色包含下列 Azure 動作：

| Type    | 使用權限 | 描述 |
| ------- | ---------- | ----------- |
| Action | `*/read`   | 檢視所有 Azure 資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定。 <br> 針對工作區, 它允許完全不受限制的許可權來讀取工作區設定, 並對資料執行查詢。 查看更多更細微的選項。 |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 已淘汰, 不需要將它們指派給使用者。 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | 已淘汰, 不需要將它們指派給使用者。 |
| Action | `Microsoft.Support/*` | 開啟支援案例的能力 |
|不是動作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止讀取在使用資料收集 API 和安裝代理程式時所需的工作區金鑰。 這可防止使用者將新資源新增至工作區 |

*Log Analytics 參與者*角色的成員可以：

* 與 Log Analytics 讀者一樣可讀取所有監視資料
* 建立和設定自動化帳戶
* 新增及移除管理解決方案

    > [!NOTE]
    > 若要成功執行後面兩個動作，就必須在資源群組或訂用帳戶層級授與此權限。  

* 讀取儲存體帳戶金鑰
* 設定 Azure 儲存體的記錄集合  
* 編輯 Azure 資源的監視設定，包括
  * 將 VM 擴充功能新增至 VM
  * 在所有 Azure 資源上設定 Azure 診斷

> [!NOTE]
> 您可以使用將虛擬機器擴充功能新增至虛擬機器的功能，取得虛擬機器的完整控制權。

Log Analytics 參與者角色包含下列 Azure 動作：

| 使用權限 | 描述 |
| ---------- | ----------- |
| `*/read`     | 檢視所有資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定。 <br> 針對工作區, 它允許完全不受限制的許可權來讀取工作區設定, 並對資料執行查詢。 查看更多更細微的選項。 |
| `Microsoft.Automation/automationAccounts/*` | 建立及設定 Azure 自動化帳戶的能力，包括新增和編輯 Runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | 新增、更新及移除虛擬機器擴充功能，包括 Microsoft Monitoring Agent 擴充功能和 OMS Agent for Linux 擴充功能 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 檢視儲存體帳戶金鑰。 需具備此權限，才能設定 Log Analytics 以讀取 Azure Blob 儲存體帳戶中的記錄 |
| `Microsoft.Insights/alertRules/*` | 新增、更新和移除警示規則 |
| `Microsoft.Insights/diagnosticSettings/*` | 在 Azure 資源上新增、更新和移除診斷設定 |
| `Microsoft.OperationalInsights/*` | 新增、更新和移除 Log Analytics 工作區的組態 |
| `Microsoft.OperationsManagement/*` | 新增及移除管理解決方案 |
| `Microsoft.Resources/deployments/*` | 建立及刪除部署。 需具備此權限，才能新增及移除解決方案、工作區和自動化帳戶 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 建立及刪除部署。 需具備此權限，才能新增及移除解決方案、工作區和自動化帳戶 |

若要新增及移除某個使用者角色的使用者，必須具備 `Microsoft.Authorization/*/Delete` 和 `Microsoft.Authorization/*/Write` 權限。

使用下列角色，賦予使用者不同範圍的存取權：

* 訂用帳戶 - 存取訂用帳戶中的所有工作區
* 資源群組 - 存取資源群組中的所有工作區
* 資源 - 僅存取指定的工作區

您應在資源層級 (工作區) 執行指派, 以確保存取控制正確。  使用[自訂角色](../../role-based-access-control/custom-roles.md)建立具備所需特定權限的角色。

### <a name="resource-permissions"></a>資源許可權

當使用者使用資源內容存取來查詢工作區中的記錄時, 他們會在資源上擁有下列許可權:

| 使用權限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>範例:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 能夠查看資源的所有記錄資料。  |
| `Microsoft.Insights/diagnosticSettings/write ` | 能夠設定診斷設定, 以允許此資源的記錄檔。 |

`/read`許可權通常是從包含 _\*/read 或_ _\*_ 許可權 (例如內建[讀取器](../../role-based-access-control/built-in-roles.md#reader)和[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色) 的角色授與。 請注意, 包含特定動作或專屬內建角色的自訂角色可能不包含此許可權。

如果您想要為不同的資料表建立不同的存取控制, 請參閱以下[的定義每個資料表的存取控制](#table-level-rbac)。

## <a name="table-level-rbac"></a>資料表層級 RBAC

**資料表層級 RBAC**可讓您針對 Log Analytics 工作區中的資料定義更細微的控制, 以及其他許可權。 這個控制項可讓您定義只能存取一組特定使用者的特定資料類型。

您可以使用[Azure 自訂角色](../../role-based-access-control/custom-roles.md)來執行資料表存取控制, 以授與或拒絕存取工作區中的特定[資料表](../log-query/logs-structure.md)。 無論使用者的[存取模式](design-logs-deployment.md#access-mode)為何, 這些角色都會套用至具有工作區內容或資源內容[存取控制模式](design-logs-deployment.md#access-control-mode)的工作區。

使用下列動作建立[自訂角色](../../role-based-access-control/custom-roles.md), 以定義對資料表存取控制的存取權。

* 若要授與資料表的存取權, 請將它包含在角色定義的 [**動作**] 區段中。
* 若要拒絕資料表的存取, 請將它包含在角色定義的**NotActions**區段中。
* 使用 * 指定所有資料表。

例如, 若要建立具有 [_心跳_] 和 [ _AzureActivity_ ] 資料表存取權的角色, 請使用下列動作建立自訂角色:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

若要建立只能存取_SecurityBaseline_的角色, 而且沒有其他資料表, 請使用下列動作建立自訂角色:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>自訂記錄檔

 自訂記錄是從資料來源建立, 例如自訂記錄檔和 HTTP 資料收集器 API。 若要識別記錄類型, 最簡單的方式是檢查[記錄架構中的 [自訂記錄](../log-query/get-started-portal.md#understand-the-schema)] 底下所列的資料表。

 您目前無法授與或拒絕個別自訂記錄檔的存取權, 但是您可以授與或拒絕所有自訂記錄檔的存取權。 若要建立可存取所有自訂記錄的角色, 請使用下列動作建立自訂角色:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>考量

* 如果使用者被授與具有 [  _\*/read_ ] 動作之標準讀取者或參與者角色的全域讀取權限, 則會覆寫每個資料表的存取控制, 並為其提供所有記錄資料的存取權。
* 如果使用者被授與每個資料表的存取權, 但沒有其他許可權, 他們就能夠從 API 存取記錄資料, 而不是從 Azure 入口網站。 若要提供 Azure 入口網站的存取權, 請使用 Log Analytics 讀取器作為其基底角色。
* 不論任何其他許可權設定為何, 訂用帳戶的系統管理員都可以存取所有資料類型。
* 工作區擁有者會被視為每個資料表存取控制的任何其他使用者。
* 您應該將角色指派給安全性群組, 而不是個別使用者, 以減少指派的數目。 這也可協助您使用現有的群組管理工具來設定和驗證存取權。

## <a name="next-steps"></a>後續步驟

* 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，從您資料中心或其他雲端環境中的電腦收集資料。

* 請參閱[收集 azure 虛擬機器的相關資料](../../azure-monitor/learn/quick-collect-azurevm.md), 以設定從 azure vm 收集資料。