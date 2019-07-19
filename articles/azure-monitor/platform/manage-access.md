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
ms.date: 07/16/2019
ms.author: magoedte
ms.openlocfilehash: fbfbd8e26ab3e92f06194322be7ec2be2fb180fd
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254456"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>管理 Azure 監視器中的記錄資料和工作區
Azure 監視器會將記錄資料儲存在 Log Analytics 工作區中，這基本上是一個包含資料和設定資訊的容器。 若要管理對記錄資料的存取，您需執行與工作區相關的各種系統管理工作。 您或組織的其他成員可能會使用多個工作區來管理從所有或部分 IT 基礎結構收集而來的不同資料。

本文說明如何記錄管理的存取權, 以及管理包含它們的工作區。 

## <a name="create-a-workspace"></a>建立工作區
若要建立 Log Analytics 工作區, 您需要:

1. 擁有 Azure 訂用帳戶。
2. 選擇工作區名稱。
3. 讓工作區與您其中一個訂用帳戶和資源群組產生關聯。
4. 選擇地理位置。

如需建立工作區的詳細資訊, 請參閱下列文章:

- [在 Azure 入口網站中建立 Log Analytics 工作區](../learn/quick-create-workspace.md)
- [建立具有 Azure CLI 2.0 的 Log Analytics 工作區](../learn/quick-create-workspace-cli.md)
- [建立具有 Azure PowerShell 的 Log Analytics 工作區](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>判斷您需要的工作區數目
Log Analytics 工作區是一個 Azure 資源，也是「Azure 監視器」中收集、彙總、分析及呈現資料的容器。 每個 Azure 訂用帳戶可以有多個工作區，而且您可以存取多個工作區，並且能夠輕鬆地查詢所有這些工作區。 本節描述何時有利於建立多個工作區。

Log Analytics 工作區提供：

* 資料儲存體的地理位置。
* 資料隔離可在以工作區為中心的模式中定義不同的使用者存取權限。 在以資源為中心的模式下工作時不相關。
* 設定的範圍, 例如[定價層](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)、[保留期](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)和[資料上限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)。
* 在工作區資源上進行資料內嵌和保留的相關費用。

從耗用量的觀點來看，建議您建立愈少工作區愈好。 這可讓管理和查詢體驗變得更容易且快速。 但是，根據上述特性，您在下列情況下可能想要建立多個工作區︰

* 您是一家全球性公司，基於資料主權或合規性理由，您需要將記錄資料儲存在特定區域。
* 您使用 Azure，想要將工作區和它所管理的 Azure 資源放在相同區域中，以避免輸出資料傳輸費用。
* 您是受控服務提供者，需要將您管理的每個客戶的 Log Analytics 資料和其他客戶的資料保持隔離。
* 您管理多個客戶, 而且想要讓每個客戶/部門/公司群組查看自己的資料, 但不會看到其他人的資料, 而且不需要企業的跨客戶/部門/業務群組視圖。」。

使用 Windows 代理程式收集資料時，您可以[設定每個代理程式向一或多個工作區回報](../../azure-monitor/platform/agent-windows.md)。

如果您使用 System Center Operations Manager，每個 Operations Manager 管理群組只能連接一個工作區。 您可以將 Microsoft Monitoring Agent 安裝在 Operations Manager 所管理的電腦上，由代理程式向 Operations Manager 和不同的 Log Analytics 工作區回報。

定義工作區架構之後, 您應該使用[Azure 原則](../../governance/policy/overview.md)在 Azure 資源上強制執行此原則。 這可提供自動套用至所有 Azure 資源的內建定義。 例如, 您可以設定原則, 以確保特定區域中的所有 Azure 資源都會將其所有診斷記錄傳送至特定工作區。

## <a name="view-workspace-details"></a>查看工作區詳細資料
當您從 Azure 入口網站的 [ **Azure 監視器**] 功能表分析 Log Analytics 工作區中的資料時, 您可以在**log analytics 工作區**功能表中建立和管理工作區。
 

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [所有服務]  。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]  。  

    ![Azure 入口網站](media/manage-access/azure-portal-01.png)  

3. 從清單中選取您的工作區。

4. [工作區] 頁面會顯示工作區的詳細資料、使用者入門、設定和連結, 以取得其他資訊。  

    ![工作區詳細資料](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>工作區許可權和範圍
使用者可以存取的資料取決於下表所列的多個因素。 以下各節將說明每個步驟。

| 因素 | 描述 |
|:---|:---|
| [存取模式](#access-modes) | 使用者用來存取工作區的方法。  定義可用資料的範圍, 以及套用的存取控制模式。 |
| [存取控制模式](#access-control-mode) | 在工作區上設定, 以定義是否要在工作區或資源層級套用許可權。 |
| [Permissions](#manage-accounts-and-users) | 適用于工作區或資源的個人或使用者群組的許可權。 定義使用者可存取的資料。 |
| [資料表層級 RBAC](#table-level-rbac) | 選擇性的細微許可權, 適用于所有使用者, 不論其存取模式或存取控制模式為何。 定義使用者可以存取的資料類型。 |



## <a name="access-modes"></a>存取模式
_存取模式_指的是使用者存取 Log Analytics 工作區的方式, 並定義他們可以存取的資料範圍。 

以**工作區為中心**:在此模式中, 使用者可以在工作區中查看他們具有許可權的所有記錄。 在此模式中的查詢範圍為工作區中所有資料表的所有資料。 當您以工作區作為範圍來存取記錄時, 例如當您從 Azure 入口網站的 [ **Azure 監視器**] 功能表中選取 [**記錄**] 時, 這就是使用的存取模式。

以**資源為中心**:當您存取特定資源的工作區時 (例如當您從 Azure 入口網站中的 [資源] 功能表選取 [**記錄**] 時), 您可以只針對您有權存取的所有資料表中的該資源來查看記錄。 此模式中的查詢範圍僅限於與該資源相關聯的資料。 此模式也會啟用細微的角色型存取控制 (RBAC)。 

> [!NOTE]
> 記錄僅適用于以資源為中心的查詢 (如果它們已正確關聯至相關資源)。 目前, 下列資源有一些限制: 
> - Azure 外部的電腦
> - Service Fabric
> - Application Insights
> - 容器
>
> 您可以藉由執行查詢並檢查您感興趣的記錄, 來測試記錄是否已正確地與資源相關聯。 如果[_ResourceId](log-standard-properties.md#_resourceid)屬性中有正確的資源識別碼, 則資料可供以資源為中心的查詢使用。

### <a name="comparing-access-modes"></a>比較存取模式

下表摘要說明存取模式:

| | 以工作區為中心 | 以資源為中心 |
|:---|:---|:---|
| 每個模型的用途為何？ | 管理中心。 需要設定資料收集的系統管理員, 以及需要存取各種資源的使用者。 對於必須存取 Azure 外部資源記錄的使用者, 目前也需要此許可權。 | 應用程式小組。 受監視的 Azure 資源系統管理員。 |
| 使用者需要什麼才能查看記錄？ | 工作區的許可權。 請參閱[管理帳戶和使用者](#manage-accounts-and-users)中的**工作區許可權**。 | 資源的讀取權限。 請參閱[管理帳戶和使用者](#manage-accounts-and-users)中的**資源許可權**。 許可權可以是繼承的 (例如, 從包含的資源群組), 或直接指派給資源。 系統將會自動指派資源的記錄許可權。 |
| 許可權的範圍為何？ | 區域. 具有工作區存取權的使用者可以從他們有許可權的資料表查詢該工作區中的所有記錄。 請參閱[資料表存取控制](#table-level-rbac) | Azure 資源。 使用者可以從任何工作區查詢可存取之資源的記錄, 但無法查詢其他資源的記錄。 |
| 使用者存取記錄的方式為何？ | 從**Azure 監視器**功能表或**Log Analytics 工作區**啟動**記錄**。 | 從 Azure 資源的功能表啟動**記錄**。 |


## <a name="access-control-mode"></a>存取控制模式
_存取控制模式_是每個工作區上的設定, 定義如何決定該工作區的許可權。

**需要工作區許可權**:這個控制模式不允許細微的 RBAC。 若要讓使用者存取工作區, 他們必須被授與工作區或特定資料表的許可權。 

如果使用者以工作區為中心模式存取工作區, 他們就可以存取所有已被授與存取權之資料表的資料。 如果使用者在以資源為中心的模式下存取工作區, 他們就只能存取其已授與存取權之任何資料表中的該資源資料。

這是在2019年3月之前建立之所有工作區的預設設定。

**使用資源或工作區許可權**:這個控制模式允許細微的 RBAC。 使用者只能存取與資源相關聯的資料, 他們可以透過 Azure 許可權來查看他們具有`read`許可權的資源。 

當使用者以工作區為中心模式存取工作區時, 將會套用工作區許可權。 當使用者在以資源為中心的模式下存取工作區時, 只會驗證資源許可權, 並會忽略工作區許可權。 將使用者從工作區許可權中移除, 並允許辨識其資源許可權, 以啟用 RBAC。

這是2019年3月之後所建立之所有工作區的預設設定。

> [!NOTE]
> 如果使用者只有工作區的資源許可權, 他們將只能使用以[資源為中心的模式](#access-modes)來存取工作區。


### <a name="define-access-control-mode-in-azure-portal"></a>在 Azure 入口網站中定義存取控制模式
您可以在**Log Analytics 工作區**功能表中, 于工作區的 [**總覽**] 頁面上, 查看目前的工作區存取控制模式。

![View workspace 存取控制模式](media/manage-access/view-access-control-mode.png)

您可以在工作區的 [**屬性**] 頁面上變更此設定。 如果您沒有設定工作區的許可權, 將會停用變更設定。

![變更工作區存取模式](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>在 PowerShell 中定義存取控制模式

使用下列命令來檢查訂用帳戶中所有工作區的存取控制模式:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

使用下列腳本來設定特定工作區的存取控制模式:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用下列腳本來設定訂用帳戶中所有工作區的存取控制模式

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>在 Resource Manager 範本中定義存取模式
若要在 Azure Resource Manager 範本中設定存取模式, 請將工作區上的**enableLogAccessUsingOnlyResourcePermissions**功能旗標設為下列其中一個值。

- **false**:將工作區設定為以工作區為中心的許可權。 如果未設定旗標, 這就是預設設定。
- **true**:將工作區設定為以資源為中心的許可權。


## <a name="manage-accounts-and-users"></a>管理帳戶和使用者
套用至特定使用者之工作區的許可權是由其存取模式和工作區的[存取控制模式](#access-control-mode)所定義。 當使用者使用以工作區為中心的[模式](#access-modes)來  存取任何工作區時, 就會套用**工作區許可權**。 當使用者存取具有**使用資源或工作區許可權**[存取控制模式](#access-control-mode)的工作區時, 會套用**資源許可權**(使用以[資源為中心的模式)](#access-modes)。

### <a name="workspace-permissions"></a>工作區許可權
每個工作區可以有多個相關聯的帳戶，而每個帳戶可以存取多個工作區。 可透過 [Azure 角色型存取](../../role-based-access-control/role-assignments-portal.md)來管理存取權。 


下列活動也需要 Azure 權限︰

||Action |所需的 Azure 權限 |注意 |
|-------|-------------------------|------|
| 新增和移除監視解決方案 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 必須在資源群組或訂用帳戶層級授與這些權限。 |
| 變更定價層 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 檢視 [備份]  和 [Site Recovery]  解決方案圖格中的資料 | 系統管理員/共同管理員 | 存取使用傳統部署模型所部署的資源 |
| 在 Azure 入口網站中建立工作區 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| 在入口網站中查看工作區基本屬性並輸入工作區分頁 | `Microsoft.OperationalInsights/workspaces/read` ||
| 使用任何介面查詢記錄 | `Microsoft.OperationalInsights/workspaces/query/read` ||
| 使用查詢存取所有記錄類型 | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 存取特定的記錄資料表 | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| 讀取工作區金鑰以允許將記錄傳送到此工作區 | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||



#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>使用 Azure 版權管理對 Log Analytics 工作區的存取 
若要使用 Azure 權限授與 Log Analytics 工作區的存取權，請遵循[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)中的步驟。

Azure 有兩個內建的 Log Analytics 工作區角色：
- Log Analytics 讀者
- Log Analytics 參與者

*Log Analytics 讀者*角色的成員可以：
- 檢視及搜尋所有監視資料 
- 檢視監視設定，包括檢視所有 Azure 資源上檢視的 Azure 診斷組態。

Log Analytics 讀者角色包含下列 Azure 動作：

| Type    | 權限 | 描述 |
| ------- | ---------- | ----------- |
| Action | `*/read`   | 檢視所有 Azure 資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定。 <br> 針對工作區, 它允許完全不受限制的許可權來讀取工作區設定, 並對資料執行查詢。 查看更多更細微的選項。 |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 已淘汰, 不需要將它們指派給使用者。 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | 已淘汰, 不需要將它們指派給使用者。 |
| Action | `Microsoft.Support/*` | 開啟支援案例的能力 |
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
| `*/read`     | 檢視所有 Azure 資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定。 <br> 針對工作區, 它允許完全不受限制的許可權來讀取工作區設定, 並對資料執行查詢。 查看更多更細微的選項。 |
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

您應在資源層級 (工作區) 執行指派, 以確保存取控制正確。  使用[自訂角色](../../role-based-access-control/custom-roles.md)建立具備所需特定權限的角色。

### <a name="resource-permissions"></a>資源許可權 
當使用者使用以資源為中心的存取來查詢工作區中的記錄時, 他們會在資源上擁有下列許可權:

| 權限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>範例：<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 能夠查看資源的所有記錄資料。  |
| `Microsoft.Insights/diagnosticSettings/write ` | 能夠設定診斷設定, 以允許此資源的記錄檔。 |

這個許可權通常是從包含 _\*/read 或_ _\*_ 許可權 (例如內建[讀取器](../../role-based-access-control/built-in-roles.md#reader)和[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色) 的角色授與。 請注意, 包含特定動作或專屬內建角色的自訂角色可能不包含此許可權。

如果您想要為不同的資料表建立不同的存取控制, 請參閱以下[的定義每個資料表的存取控制](#table-level-rbac)。


## <a name="table-level-rbac"></a>資料表層級 RBAC
**資料表層級 RBAC**可讓您針對 Log Analytics 工作區中的資料提供更細微的控制, 以及其他許可權。 這個控制項可讓您定義只能存取一組特定使用者的特定資料類型。

您可以使用[Azure 自訂角色](../../role-based-access-control/custom-roles.md)來執行資料表存取控制, 以授與或拒絕存取工作區中的特定[資料表](../log-query/logs-structure.md)。 無論使用者的[存取模式](#access-modes)為何, 這些角色都會套用至具有以工作區為主或以資源為中心的[存取控制模式](#access-control-mode)的工作區。

使用下列動作建立[自訂角色](../../role-based-access-control/custom-roles.md), 以定義對資料表存取控制的存取權。

- 若要授與資料表的存取權, 請將它包含在角色定義的 [**動作**] 區段中。
- 若要拒絕資料表的存取, 請將它包含在角色定義的**NotActions**區段中。
- 使用 * 指定所有資料表。

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

### <a name="custom-logs"></a>自訂的記錄
 自訂記錄是由「自訂記錄」和「HTTP 資料收集器 API」等資料來源所建立。 若要識別記錄類型, 最簡單的方式是檢查[記錄架構中的 [自訂記錄](../log-query/get-started-portal.md#understand-the-schema)] 底下所列的資料表。

 您目前無法授與或拒絕個別自訂記錄檔的存取權, 但是您可以授與或拒絕所有自訂記錄檔的存取權。 若要建立可存取所有自訂記錄的角色, 請使用下列動作建立自訂角色:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>考量

- 如果使用者被授與具有 [  _\*/read_ ] 動作之標準讀取者或參與者角色的全域讀取權限, 則會覆寫每個資料表的存取控制, 並為其提供所有記錄資料的存取權。
- 如果使用者被授與每個資料表的存取權, 但沒有其他許可權, 他們就能夠從 API 存取記錄資料, 而不是從 Azure 入口網站。 若要提供 Azure 入口網站的存取權, 請使用 Log Analytics 讀取器作為其基底角色。
- 無論任何其他許可權設定為何, 訂用帳戶的系統管理員都可以存取所有資料類型。
- 工作區擁有者會被視為每個資料表存取控制的任何其他使用者。
- 您應該將角色指派給安全性群組, 而不是個別使用者, 以減少指派的數目。 這也可協助您使用現有的群組管理工具來設定和驗證存取權。




## <a name="next-steps"></a>後續步驟
* 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，從您資料中心或其他雲端環境中的電腦收集資料。
* 請參閱[收集關於 Azure 虛擬機器的資料](../../azure-monitor/learn/quick-collect-azurevm.md)，以進行從 Azure VM 收集資料的設定。  

