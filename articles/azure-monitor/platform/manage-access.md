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
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 27db27d79a05f24461e63242c0395cfd81315432
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60782745"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>管理記錄檔資料與 Azure 監視器中的工作區
Azure 監視器會將記錄資料儲存在 Log Analytics 工作區中，這基本上是一個包含資料和設定資訊的容器。 若要管理對記錄資料的存取，您需執行與工作區相關的各種系統管理工作。 您或組織的其他成員可能會使用多個工作區來管理從所有或部分 IT 基礎結構收集而來的不同資料。

這篇文章說明如何管理記錄檔的存取權，以及如何管理包含它們的工作區。 

## <a name="create-a-workspace"></a>建立工作區
若要建立 Log Analytics 工作區，您需要：

1. 擁有 Azure 訂用帳戶。
2. 選擇工作區名稱。
3. 讓工作區與您其中一個訂用帳戶和資源群組產生關聯。
4. 選擇地理位置。

請參閱下列文章來建立工作區的詳細資訊：

- [在 Azure 入口網站中建立 Log Analytics 工作區](../learn/quick-create-workspace.md)
- [使用 Azure CLI 2.0 建立 Log Analytics 工作區](../learn/quick-create-workspace-cli.md)
- [使用 Azure PowerShell 建立 Log Analytics 工作區](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>判斷您需要的工作區數目
Log Analytics 工作區是一個 Azure 資源，也是「Azure 監視器」中收集、彙總、分析及呈現資料的容器。 每個 Azure 訂用帳戶可以有多個工作區，而且您可以存取多個工作區，並且能夠輕鬆地查詢所有這些工作區。 本節描述何時有利於建立多個工作區。

Log Analytics 工作區提供：

* 資料儲存體的地理位置。
* 若要在工作區為主的模式下定義不同的使用者存取權限的資料隔離。 以資源為主的模式工作時，則不相關。
* 組態設定的範圍喜歡[定價層](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)，[保留](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)並[資料達到上限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)。
* 工作區資源上進行資料擷取和保留相關的費用。

從耗用量的觀點來看，建議您建立愈少工作區愈好。 這可讓管理和查詢體驗變得更容易且快速。 但是，根據上述特性，您在下列情況下可能想要建立多個工作區︰

* 您是一家全球性公司，基於資料主權或合規性理由，您需要將記錄資料儲存在特定區域。
* 您使用 Azure，想要將工作區和它所管理的 Azure 資源放在相同區域中，以避免輸出資料傳輸費用。
* 您是受控服務提供者，需要將您管理的每個客戶的 Log Analytics 資料和其他客戶的資料保持隔離。
* 您管理多個客戶，您想要每個客戶 / 部門 / 商務群組以查看他們自己的資料，但不是從其他資料，而且沒有任何商務需要，合併的跨客戶 / 部門 / 商務群組檢視。"。

使用 Windows 代理程式收集資料時，您可以[設定每個代理程式向一或多個工作區回報](../../azure-monitor/platform/agent-windows.md)。

如果您使用 System Center Operations Manager，每個 Operations Manager 管理群組只能連接一個工作區。 您可以將 Microsoft Monitoring Agent 安裝在 Operations Manager 所管理的電腦上，由代理程式向 Operations Manager 和不同的 Log Analytics 工作區回報。

工作空間架構定義之後，您應該強制執行此原則的 Azure 資源[Azure 原則](../../governance/policy/overview.md)。 這可以提供的內建的定義，會自動套用到所有 Azure 資源。 例如，您可以設定原則以確保特定區域中的所有 Azure 資源，傳送到特定的工作區的所有診斷記錄檔。

## <a name="view-workspace-details"></a>檢視工作區詳細資料
當您在您的 Log Analytics 工作區，從分析資料**Azure 監視器**在 Azure 入口網站中的功能表上，您建立，並在管理工作區**Log Analytics 工作區**功能表。
 

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [所有服務]  。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]  。  

    ![Azure 入口網站](media/manage-access/azure-portal-01.png)  

3. 從清單中選取您的工作區。

4. [工作區] 頁面會顯示工作區，快速入門、 設定和相關之其他資訊連結的詳細資料。  

    ![工作區詳細資料](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>工作區的權限和範圍
使用者可存取的資料是由下列表格中所列的多個因素決定。 每個都是以下列各節所述。

| 因素 | 描述 |
|:---|:---|
| [存取模式](#access-modes) | 使用者使用的方法存取工作區。  定義可用資料的範圍以及套用的存取控制模式。 |
| [存取控制模式](#access-control-mode) | 定義是否在工作區或資源層級套用權限的工作區的設定。 |
| [Permissions](#manage-accounts-and-users) | 套用至一或多個工作區或資源的使用者權限。 定義哪些資料的使用者會有存取權。 |
| [資料表層級 RBAC](#table-level-rbac) | 選擇性的細微權限，適用於所有的使用者，而不論他們的存取模式或存取控制模式。 定義使用者可以存取哪些資料類型。 |



## <a name="access-modes"></a>存取模式
_存取模式_是指使用者如何存取 Log Analytics 工作區，並定義其可以存取的資料範圍。 

**工作區為主**:在此模式中，使用者可以檢視他們擁有權限的工作區中的所有記錄檔。 在此模式中的查詢都限於工作區中的所有資料表中的所有資料。 這是做為範圍，例如當您選取 存取記錄與工作區時所使用的存取模式**記錄檔**從**Azure 監視器**在 Azure 入口網站中的功能表。

**資源中心**:當您存取特定的資源，例如當您選取的工作區**記錄檔**從 Azure 入口網站中的 [資源] 功能表中，您可以檢視只有該資源的記錄檔，您可以存取的所有資料表中。 在此模式中查詢的範圍為該資源相關聯的資料。 此模式也可讓細微的角色型存取控制 (RBAC)。 

> [!NOTE]
> 記錄檔可供資源為主的查詢只就彷彿它們正確地相關聯的相關資源。 目前，下列資源會有限制： 
> - 在 Azure 外部電腦
> - Service Fabric
> - Application Insights
> - 容器
>
> 您可以測試是否記錄是正確聯其資源來執行查詢，並且檢查記錄您感興趣。 如果正確的資源識別碼[_ResourceId](log-standard-properties.md#_resourceid)屬性，則資料可供資源為主的查詢。

### <a name="comparing-access-modes"></a>比較的存取模式

下表摘要說明存取模式：

| | 工作區為主 | 資源中心 |
|:---|:---|:---|
| 為每個模型的適用對象？ | 中央系統管理。 系統管理員必須設定資料收集和使用者需要存取各種不同的資源。 也目前需要有存取 Azure 外部的資源的記錄檔的使用者。 | 應用程式的團隊。 受監視的 Azure 資源的系統管理員。 |
| 哪些使用者需要檢視記錄檔？ | 工作區的權限。 請參閱**工作區的權限**中[管理帳戶和使用者](#manage-accounts-and-users)。 | 讀取資源的存取權。 請參閱**資源的權限**中[管理帳戶和使用者](#manage-accounts-and-users)。 權限可以是繼承的 （例如從內含的資源群組），或直接指派給資源。 將自動指派資源的記錄檔的權限。 |
| 什麼是權限範圍？ | 工作區。 存取工作區的使用者可以查詢資料表中他們擁有權限的該工作區中的所有記錄檔。 請參閱[資料表存取控制](#table-level-rbac) | Azure 資源。 使用者可以查詢記錄檔的資源有從任何工作區的存取權的人員，但無法查詢其他資源的記錄檔。 |
| 如何使用者存取記錄檔？ | 開始**記錄檔**從**Azure 監視器**功能表或**Log Analytics 工作區**。 | 開始**記錄檔**功能表中的 Azure 資源。 |


## <a name="access-control-mode"></a>存取控制模式
_存取控制模式_是每個工作區上設定，會定義如何決定該工作區的權限。

**需要工作區的權限**:此控制項模式中不允許細微的 RBAC。 使用者存取工作區，它們必須被授與工作區或特定資料表權限。 

如果使用者存取工作區為主的模式中的工作區時，它們必須存取所有資料已被授與存取權的任何資料表。 如果使用者存取資源中心的模式中的工作區時，他們必須資料的存取權只針對該資源已被授與存取權的任何資料表中。

這是在 2019 年 3 月之前建立的所有工作區的預設設定。

**使用資源或工作區的權限**:此控制項模式可讓細微的 RBAC。 使用者會被授與他們可以透過 Azure 權限，他們擁有的資源檢視的資源相關聯的資料存取`read`權限。 

當使用者存取工作區為主的模式中的工作區時，會套用工作區的權限。 當使用者存取資源中心的模式中的工作區時，將會驗證只有資源的權限，和工作區的權限將會被忽略。 為使用者啟用 RBAC，請移除工作區的權限，並讓他們的資源權限，可辨識。

這是在 2019 年 3 月之後建立的所有工作區的預設設定。

> [!NOTE]
> 如果使用者具有工作區的資源權限，他們將只能存取工作區中使用[資源中心的模式](#access-modes)。


### <a name="define-access-control-mode-in-azure-portal"></a>在 Azure 入口網站中定義的存取控制模式
您可以檢視目前的工作區的存取控制模式**概觀**工作區中的頁面**Log Analytics 工作區**功能表。

![檢視工作區的存取控制模式](media/manage-access/view-access-control-mode.png)

您可以變更此設定上**屬性**工作區的頁面。 變更此設定將會停用，如果您沒有權限可設定的工作區。

![變更工作區的存取模式](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>在 PowerShell 中定義的存取控制模式

您可以使用下列命令來檢查訂用帳戶中的所有工作區的存取控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

您可以使用下列指令碼來設定特定的工作區的存取控制模式：

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用下列指令碼來設定訂用帳戶中的所有工作區的存取控制模式

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>在 Resource Manager 範本中定義的存取模式
若要在 Azure Resource Manager 範本中設定的存取模式，請設定**enableLogAccessUsingOnlyResourcePermissions**功能旗標上下列值的其中一個工作區。

- **False**:設定工作區為中心的權限的工作區。 這是未設定此旗標時的預設值。
- **True**:設定以資源為中心的權限的工作區。


## <a name="manage-accounts-and-users"></a>管理帳戶和使用者
其存取模式所定義的工作區的權限套用至特定使用者和[存取控制模式](#access-control-mode)工作區。 **工作區的權限**使用者存取的任何工作區使用時，會套用**工作區為主**中[工作區為主的模式](#access-modes)。 **資源的權限**使用者存取的工作區時，會套用**使用資源或工作區的權限**[存取控制模式](#access-control-mode)使用[資源中心的模式](#access-modes).

### <a name="workspace-permissions"></a>工作區的權限
每個工作區可以有多個相關聯的帳戶，而每個帳戶可以存取多個工作區。 可透過 [Azure 角色型存取](../../role-based-access-control/role-assignments-portal.md)來管理存取權。 


下列活動也需要 Azure 權限︰

| 動作                                                          | 所需的 Azure 權限 | 注意 |
|-----------------------------------------------------------------|--------------------------|-------|
| 新增和移除監視解決方案                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 必須在資源群組或訂用帳戶層級授與這些權限。 |
| 變更定價層                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 檢視 [備份]  和 [Site Recovery]  解決方案圖格中的資料 | 系統管理員/共同管理員 | 存取使用傳統部署模型所部署的資源 |
| 在 Azure 入口網站中建立工作區                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>管理對 Log Analytics 工作區，利用 Azure 權限的存取 
若要使用 Azure 權限授與 Log Analytics 工作區的存取權，請遵循[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)中的步驟。

Azure 有兩個內建的 Log Analytics 工作區角色：
- Log Analytics 讀者
- Log Analytics 參與者

*Log Analytics 讀者*角色的成員可以：
- 檢視及搜尋所有監視資料 
- 檢視監視設定，包括檢視所有 Azure 資源上檢視的 Azure 診斷組態。

Log Analytics 讀者角色包含下列 Azure 動作：

| 類型    | 權限 | 描述 |
| ------- | ---------- | ----------- |
| 動作 | `*/read`   | 檢視所有 Azure 資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定 |
| 動作 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 執行記錄搜尋 v2 查詢的能力 |
| 動作 | `Microsoft.OperationalInsights/workspaces/search/action` | 執行記錄搜尋 v1 查詢的能力 |
| 動作 | `Microsoft.Support/*` | 開啟支援案例的能力 |
|不是動作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止讀取在使用資料收集 API 和安裝代理程式時所需的工作區金鑰。 這可防止使用者將新資源新增至工作區 |


*Log Analytics 參與者*角色的成員可以：
- 與 Log Analytics 讀者一樣可讀取所有監視資料  
- 建立和設定自動化帳戶  
- 新增及移除管理解決方案    
    > [!NOTE] 
    > 若要成功執行後面兩個動作，就必須在資源群組或訂用帳戶層級授與此權限。  

- 讀取儲存體帳戶金鑰   
- 設定 Azure 儲存體的記錄集合  
- 編輯 Azure 資源的監視設定，包括
  - 將 VM 擴充功能新增至 VM
  - 在所有 Azure 資源上設定 Azure 診斷

> [!NOTE] 
> 您可以使用將虛擬機器擴充功能新增至虛擬機器的功能，取得虛擬機器的完整控制權。

Log Analytics 參與者角色包含下列 Azure 動作：

| 權限 | 描述 |
| ---------- | ----------- |
| `*/read`     | 檢視所有資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定 |
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
- 訂用帳戶 - 存取訂用帳戶中的所有工作區
- 資源群組 - 存取資源群組中的所有工作區
- 資源 - 僅存取指定的工作區

您應該執行資源層級 （工作區），以確保正確的存取控制的指派。  使用[自訂角色](../../role-based-access-control/custom-roles.md)建立具備所需特定權限的角色。

### <a name="resource-permissions"></a>資源的權限 
當使用者查詢都會從工作區中使用資源中心存取記錄時，它們會擁有下列權限資源上：

| 權限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>範例：<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 若要檢視資源的所有記錄資料的能力。  |


此權限通常授與角色，其中包含從 _\*/讀取或_ _\*_ 例如內建的權限[讀取器](../../role-based-access-control/built-in-roles.md#reader)並[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色。 請注意，包含特定動作的自訂角色或專用的內建角色可能不會包含此權限。

請參閱[定義每個資料表的存取控制](#table-level-rbac)下方如果您想要建立不同資料表的不同的存取控制。


## <a name="table-level-rbac"></a>資料表層級 RBAC
**資料表 層級 RBAC**可讓您提供更細微的控制，除了其他的權限的 Log Analytics 工作區中的資料。 這個控制項可讓您定義只有一組特定的使用者能夠存取的特定資料類型。

實作使用的資料表存取控制[Azure 的自訂角色](../../role-based-access-control/custom-roles.md)授與或拒絕存取特定[資料表](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized)工作區中。 這些角色會套用至工作區為主或以資源為中心的工作區[存取控制模式](#access-control-mode)使用者不論[存取模式](#access-modes)。

建立[自訂角色](../../role-based-access-control/custom-roles.md)執行下列動作，以定義資料表的存取控制的存取權。

- 若要授與資料表的存取權，請將它併入**動作**角色定義的區段。
- 若要拒絕資料表的存取權，將它併入**NotActions**角色定義的區段。
- 使用 * 指定所有資料表。

例如，若要建立具有存取權的角色_活動訊號_並_AzureActivity_資料表，建立自訂角色，使用下列動作：

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

若要建立角色的存取權僅_SecurityBaseline_和任何其他資料表中，建立自訂角色，使用下列動作：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>自訂的記錄
 自訂記錄檔會建立資料來源，例如自訂的記錄檔和 HTTP 資料收集器 API。 最簡單的方式，來識別記錄檔的類型是藉由檢查底下所列的資料表[記錄檔結構描述中的自訂記錄檔](../log-query/get-started-portal.md#understand-the-schema)。

 您目前無法授與或拒絕存取個別的自訂記錄檔，但您可以授與或拒絕存取所有的自訂記錄檔。 若要建立具有所有的自訂記錄檔的存取權的角色，建立自訂角色，使用下列動作：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>考量

- 如果使用者被授與全域讀取包含標準讀取者或參與者角色的權限 _\*/讀取_動作，它會覆寫的每一個資料表存取控制和提供這些使用者存取記錄檔的所有資料。
- 如果每一個資料表的存取，但沒有其他權限授與使用者時，他們就能夠存取記錄資料，從 API，而不是從 Azure 入口網站。 若要提供存取 Azure 入口網站，作為其基底的角色中的 Log Analytics 讀者。
- 訂用帳戶的系統管理員能夠存取所有的資料類型，不論其他的使用權限設定。
- 工作區擁有者被視為任何其他使用者，讓每個資料表的存取控制。
- 您應該將角色指派給安全性群組，而不是減少指派個別使用者。 這將能協助您使用現有的群組管理工具來設定及驗證存取。




## <a name="next-steps"></a>後續步驟
* 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，從您資料中心或其他雲端環境中的電腦收集資料。
* 請參閱[收集關於 Azure 虛擬機器的資料](../../azure-monitor/learn/quick-collect-azurevm.md)，以進行從 Azure VM 收集資料的設定。  

