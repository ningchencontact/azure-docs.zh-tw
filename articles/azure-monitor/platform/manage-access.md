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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782745"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>管理 Azure Monitor 中的日志数据和工作区
Azure 監視器會將記錄資料儲存在 Log Analytics 工作區中，這基本上是一個包含資料和設定資訊的容器。 若要管理對記錄資料的存取，您需執行與工作區相關的各種系統管理工作。 您或組織的其他成員可能會使用多個工作區來管理從所有或部分 IT 基礎結構收集而來的不同資料。

本文介绍如何管理对日志的访问，以及如何管理包含日志的工作区。 

## <a name="create-a-workspace"></a>建立工作區
若要创建 Log Analytics 工作区，需要：

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

* 数据存储的地理位置。
* 数据隔离，用于定义以工作区为中心的模式下的不同用户访问权限。 在以资源为中心的模式下操作时，此功能没有作用。
* 设置配置的范围，如[定价层级](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)、[保留期](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)和[数据上限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)。
* 与数据引入和保留相关的费用是根据工作区资源计收的。

從耗用量的觀點來看，建議您建立愈少工作區愈好。 這可讓管理和查詢體驗變得更容易且快速。 但是，根據上述特性，您在下列情況下可能想要建立多個工作區︰

* 您是一家全球性公司，基於資料主權或合規性理由，您需要將記錄資料儲存在特定區域。
* 您使用 Azure，想要將工作區和它所管理的 Azure 資源放在相同區域中，以避免輸出資料傳輸費用。
* 您是受控服務提供者，需要將您管理的每個客戶的 Log Analytics 資料和其他客戶的資料保持隔離。
* 你要管理多个客户，并希望每个客户/部门/业务组能够看到自己的数据，但看不到其他人的数据；没有在整个客户/部门/业务组中整合视图的业务需求。

使用 Windows 代理程式收集資料時，您可以[設定每個代理程式向一或多個工作區回報](../../azure-monitor/platform/agent-windows.md)。

如果您使用 System Center Operations Manager，每個 Operations Manager 管理群組只能連接一個工作區。 您可以將 Microsoft Monitoring Agent 安裝在 Operations Manager 所管理的電腦上，由代理程式向 Operations Manager 和不同的 Log Analytics 工作區回報。

工作空間架構定義之後，您應該強制執行此原則的 Azure 資源[Azure 原則](../../governance/policy/overview.md)。 這可以提供的內建的定義，會自動套用到所有 Azure 資源。 例如，您可以設定原則以確保特定區域中的所有 Azure 資源，傳送到特定的工作區的所有診斷記錄檔。

## <a name="view-workspace-details"></a>查看工作区详细信息
通过 Azure 门户中的“Azure Monitor”菜单分析 Log Analytics 工作区中的数据时，可以在“Log Analytics 工作区”菜单中创建和管理工作区。
 

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]。  

    ![Azure 入口網站](media/manage-access/azure-portal-01.png)  

3. 從清單中選取您的工作區。

4. 工作区页会显示有关工作区、入门、配置的详细信息，以及其他信息的链接。  

    ![工作區詳細資料](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>工作区权限和范围
用户有权访问的数据由下表中列出的多种因素决定。 后续部分会描述每种因素。

| 因素 | 描述 |
|:---|:---|
| [访问模式](#access-modes) | 用户访问工作区的方法。  定义可用数据的范围，以及应用的访问控制模式。 |
| [访问控制模式](#access-control-mode) | 工作区中的设置，用于定义是要在工作区级别还是资源级别应用权限。 |
| [權限](#manage-accounts-and-users) | 应用到工作区或资源的个人用户或用户组的权限。 定义用户有权访问哪些数据。 |
| [表级 RBAC](#table-level-rbac) | 应用到所有用户（无论他们使用的是访问模式还是访问控制模式）的可选精细权限。 定义用户可以访问哪些数据类型。 |



## <a name="access-modes"></a>访问模式
访问模式是指用户如何访问 Log Analytics 工作区，并定义他们有权访问的数据范围。 

**以工作区为中心**：在此模式下，用户可以查看他们有权访问的工作区中的所有日志。 在此模式下，只能查询该工作区中所有表内的所有数据。 使用工作区作为范围来访问日志时（例如，在 Azure 门户上的“Azure Monitor”菜单中选择“日志”时），将使用此访问模式。

**以资源为中心**：访问特定资源的工作区时（例如，在 Azure 门户上的资源菜单中选择“日志”时），只能查看所有表中你有权访问的资源的日志。 在此模式下，只能查询与该资源关联的数据。 此模式还会启用精细的基于角色的访问控制 (RBAC)。 

> [!NOTE]
> 仅当日志已适当地关联到相关资源时，才能对日志进行以资源为中心的查询。 目前，以下资源存在限制： 
> - Azure 外部的计算机
> - Service Fabric
> - Application Insights
> - 容器
>
> 可以通过运行一个查询并检查所需的记录，来测试日志是否已适当关联到其资源。 如果 [_ResourceId](log-standard-properties.md#_resourceid) 属性中包含正确的资源 ID，则可以对数据进行以资源为中心的查询。

### <a name="comparing-access-modes"></a>比较访问模式

下表汇总了访问模式：

| | 以工作区为中心 | 以资源为中心 |
|:---|:---|:---|
| 每种模式适合哪类用户？ | 集中管理。 需要配置数据收集的管理员，以及需要访问各种资源的用户。 此外，必须访问 Azure 外部资源的日志的用户目前也需要使用此模式。 | 应用程序团队。 受监视 Azure 资源的管理员。 |
| 用户需要哪些权限才能查看日志？ | 对工作区的权限。 请参阅[管理帐户和用户](#manage-accounts-and-users)中的“工作区权限”。 | 对资源的读取访问权限。 请参阅[管理帐户和用户](#manage-accounts-and-users)中的“资源权限”。 权限可以继承（例如，从包含资源组继承），也可以直接分配给资源。 系统会自动分配对资源日志的权限。 |
| 权限范围是什么？ | 工作区。 有权访问工作区的用户可以通过他们有权访问的表查询该工作区中的所有日志。 请参阅[表访问控制](#table-level-rbac) | Azure 资源。 用户可以通过任何工作区查询他们有权访问的资源的日志，但无法查询其他资源的日志。 |
| 用户如何访问日志？ | 从“Azure Monitor”菜单或“Log Analytics 工作区”启动“日志”。 | 从 Azure 资源的菜单启动“日志”。 |


## <a name="access-control-mode"></a>存取控制模式
访问控制模式是每个工作区中的一项设置，定义如何确定该工作区的权限。

**需要工作区权限**：此控制模式不允许精细的 RBAC。 用户若要访问工作区，必须获得对该工作区或特定表的权限。 

如果用户使用以工作区为中心的模式访问工作区，将可以访问他们有权访问的任何表中的所有数据。 如果用户使用以资源为中心的模式访问工作区，只能访问他们有权访问的任何表中的此类资源的数据。

这是在 2019 年 3 月之前创建的所有工作区的默认设置。

**使用资源或工作区权限**：此控制模式允许精细的 RBAC。 用户只有权访问他们可以通过 Azure 权限查看的资源的关联数据，他们对这些资源拥有 `read` 权限。 

当用户使用以工作区为中心的模式访问工作区时，将应用工作区权限。 当用户使用以资源为中心的模式访问工作区时，只会验证资源权限，而会忽略工作区权限。 要为用户启用 RBAC，可将其从工作区权限中删除，并允许识别其资源权限。

这是在 2019 年 3 月之后创建的所有工作区的默认设置。

> [!NOTE]
> 如果用户只对工作区拥有资源权限，则他们只能使用[以资源为中心的模式](#access-modes)访问工作区。


### <a name="define-access-control-mode-in-azure-portal"></a>在 Azure 门户中定义访问控制模式
可以在工作区“概述”页上的“Log Analytics 工作区”菜单中查看当前的工作区访问控制模式。

![查看工作区访问控制模式](media/manage-access/view-access-control-mode.png)

可以在工作区的“属性”页上更改此设置。 如果你无权配置工作区，则会禁止更改此设置。

![更改工作区访问模式](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>在 PowerShell 中定义访问控制模式

使用以下命令检查订阅中所有工作区的访问控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

使用以下脚本设置特定工作区的访问控制模式：

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用以下脚本为订阅中的所有工作区设置访问控制模式

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>在资源管理器模板中定义访问模式
若要在 Azure 资源管理器模板中配置访问模式，请将工作区中的 **enableLogAccessUsingOnlyResourcePermissions** 功能标志设置为以下值之一。

- **false**：将工作区设置为以工作区为中心的权限。 如果未设置该标志，则这是默认设置。
- **true**：将工作区设置为以资源为中心的权限。


## <a name="manage-accounts-and-users"></a>管理帳戶和使用者
应用到特定用户的工作区权限由该用户的访问模式以及工作区的[访问控制模式](#access-control-mode)定义。 当用户在[以工作区为中心的模式](#access-modes)下访问使用**以工作区为中心**的任何工作区时，将应用**工作区权限**。 当用户在[以资源为中心的模式](#access-modes)下，使用“使用资源或工作区权限”[访问控制模式](#access-control-mode)访问工作区时，将应用**资源权限**。

### <a name="workspace-permissions"></a>工作区权限
每個工作區可以有多個相關聯的帳戶，而每個帳戶可以存取多個工作區。 可透過 [Azure 角色型存取](../../role-based-access-control/role-assignments-portal.md)來管理存取權。 


下列活動也需要 Azure 權限︰

|  動作                                                          | 所需的 Azure 權限 | 注意 |
|-----------------------------------------------------------------|--------------------------|-------|
| 添加和删除监视解决方案                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 必須在資源群組或訂用帳戶層級授與這些權限。 |
| 變更定價層                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 檢視 [備份] 和 [Site Recovery] 解決方案圖格中的資料 | 系統管理員/共同管理員 | 存取使用傳統部署模型所部署的資源 |
| 在 Azure 入口網站中建立工作區                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>使用 Azure 权限管理对 Log Analytics 工作区的访问 
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
|  動作 | `*/read`   | 檢視所有 Azure 資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定 |
|  動作 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 執行記錄搜尋 v2 查詢的能力 |
|  動作 | `Microsoft.OperationalInsights/workspaces/search/action` | 執行記錄搜尋 v1 查詢的能力 |
|  動作 | `Microsoft.Support/*` | 開啟支援案例的能力 |
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

应在资源（工作区）级别执行分配，以确保准确的访问控制。  使用[自訂角色](../../role-based-access-control/custom-roles.md)建立具備所需特定權限的角色。

### <a name="resource-permissions"></a>资源权限 
当用户使用以资源为中心的访问权限查询工作区中的日志时，他们对资源拥有以下权限：

| 權限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>範例：<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 可以查看资源的所有日志数据。  |


此权限通常是从含有 _\*/read 或_ _\*_ 权限的角色授予的，例如内置的[读取者](../../role-based-access-control/built-in-roles.md#reader)和[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色。 请注意，含有特定操作的自定义角色或专用内置角色可能没有此权限。

若要针对不同的表创建不同的访问控制，请参阅下面的[定义按表进行的访问控制](#table-level-rbac)。


## <a name="table-level-rbac"></a>表级 RBAC
使用**表级 RBAC** 可以针对 Log Analytics 工作区中的数据提供更精细的控制，此外还能分配其他权限。 使用此控制措施可以定义仅供特定一组用户访问的特定数据类型。

使用 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)实现表访问控制，以授予或拒绝对工作区中特定[表](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized)的访问权限。 无论用户的[访问模式](#access-modes)是什么，这些角色都会应用到使用以工作区为中心或者以资源为中心[访问控制模式](#access-control-mode)的工作区。

使用以下操作创建[自定义角色](../../role-based-access-control/custom-roles.md)，以定义对表访问控制的访问权限。

- 若要授予对某个表的访问权限，请将该表包含在角色定义的 **Actions** 节中。
- 若要拒绝对某个表的访问权限，请将该表包含在角色定义的 **NotActions** 节中。
- 使用 * 可指定所有表。

例如，若要创建一个有权访问 _Heartbeat_ 和 _AzureActivity_ 表的角色，请使用以下操作创建自定义角色：

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

若要创建一个只有权访问 _SecurityBaseline_，而无权访问其他任何表的角色，请使用以下操作创建自定义角色：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>自訂的記錄
 自定义日志是由自定义日志和 HTTP 数据收集器 API 等数据源创建的。 识别日志类型的最简单方法是查看[日志架构中的自定义日志](../log-query/get-started-portal.md#understand-the-schema)下所列的表。

 目前无法授予或拒绝对单个自定义日志的访问权限，但可以授予或拒绝对所有自定义日志的访问权限。 若要创建一个有权访问所有自定义日志的角色，请使用以下操作创建自定义角色：

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>考量

- 如果为某个用户授予全局读取权限以及含有 _\*/read_ 操作的标准“读取者”或“参与者”角色，则会替代按表进行的访问控制，并向该用户授予所有日志数据的访问权限。
- 如果为某个用户授予按表访问权限但未授予其他任何权限，则该用户可以通过 API 访问日志数据，但不能通过 Azure 门户进行访问。 若要提供对 Azure 门户的访问权限，请使用“Log Analytics 读取者”作为用户的基本角色。
- 无论其他任何权限设置如何，订阅管理员都有权访问所有数据类型。
- 应用按表进行的访问控制时，工作区所有者被视为类似于其他任何用户。
- 应该将角色分配到安全组而不是个人用户，以减少分配数目。 这还有助于使用现有的组管理工具来配置和验证访问权限。




## <a name="next-steps"></a>後續步驟
* 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，從您資料中心或其他雲端環境中的電腦收集資料。
* 請參閱[收集關於 Azure 虛擬機器的資料](../../azure-monitor/learn/quick-collect-azurevm.md)，以進行從 Azure VM 收集資料的設定。  

