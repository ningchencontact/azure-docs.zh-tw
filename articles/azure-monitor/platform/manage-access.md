---
title: 在 Azure Log Analytics 和 OMS 入口網站中管理工作區 | Microsoft Docs
description: 您可以在 Azure Log Analytics 和 OMS 入口網站中使用有關使用者、帳戶、工作區及 Azure 帳戶的各種系統管理工作來管理工作區。
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
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 32a31a87bacbb13cd3b2cb4561ac04e54d51ba46
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656748"
---
# <a name="manage-workspaces"></a>管理工作區

若要管理對 Log Analytics 的存取，請執行與工作區相關的各種系統管理工作。 本文提供管理工作區的建議和程序。 工作區本質上是一個容器，包含帳戶資訊和帳戶的簡單組態資訊。 您或組織的其他成員可能會使用多個工作區來管理從所有或部分 IT 基礎結構收集而來的不同資料。

若要建立工作區，您需要︰

1. 擁有 Azure 訂用帳戶。
2. 選擇工作區名稱。
3. 讓工作區與您其中一個訂用帳戶和資源群組產生關聯。
4. 選擇地理位置。

## <a name="determine-the-number-of-workspaces-you-need"></a>判斷您需要的工作區數目
工作區是一種 Azure 資源，也是 Azure 入口網站中收集、彙總、分析及呈現資料的容器。

每個 Azure 訂用帳戶可以有多個工作區，而且您可以存取多個工作區，並且能夠輕鬆地在其間查詢。 本節描述何時有利於建立多個工作區。

現今的工作區可提供︰

* 資料儲存體的地理位置
* 用來定義不同使用者存取權的資料隔離
* 設定組態的範圍 (例如[定價層](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)、[保留期](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)和[資料上限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)) 

從耗用量的觀點來看，建議您建立愈少工作區愈好。 這可讓管理和查詢體驗變得更容易且快速。 但是，根據上述特性，您在下列情況下可能想要建立多個工作區︰

* 您是一家全球性公司，基於資料主權或合規性理由，您需要將資料儲存在特定區域。
* 您使用 Azure，想要將工作區和它所管理的 Azure 資源放在相同區域中，以避免輸出資料傳輸費用。
* 您想要在每個部門或事業群本身的 Azure 訂用帳戶中分別建立其工作區，以根據不同部門或事業群的使用量來配置費用。
* 您是受控服務提供者，需要將您管理的每個客戶的 Log Analytics 資料和其他客戶的資料保持隔離。
* 您管理多個客戶，並且想要讓每個客戶/部門/事業群只看到他們自己的資料，而不是其他客戶/部門/事業群的資料。

使用 Windows 代理程式收集資料時，您可以[設定每個代理程式向一或多個工作區回報](../../azure-monitor/platform/agent-windows.md)。

如果您使用 System Center Operations Manager，每個 Operations Manager 管理群組只能連接一個工作區。 您可以將 Microsoft Monitoring Agent 安裝在 Operations Manager 所管理的電腦上，由代理程式向 Operations Manager 和不同的 Log Analytics 工作區回報。

## <a name="workspace-information"></a>工作區資訊

您可以在 Azure 入口網站中檢視工作區的相關詳細資料。 

1. 如果您尚未登入 [Azure 入口網站](https://portal.azure.com)，請先登入。

2. 在 Azure 入口網站中，按一下 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。  

    ![Azure 入口網站](media/manage-access/azure-portal-01.png)  

3. 在 [Log Analytics 訂用帳戶] 窗格上選取工作區。

4. [工作區] 頁面會顯示關於如何開始使用的詳細資料、組態和其他資訊的連結。  

    ![工作區詳細資料](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>管理帳戶和使用者
每個工作區可以有多個相關聯的帳戶，而每個帳戶可以存取多個工作區。 可透過 [Azure 角色型存取](../../role-based-access-control/role-assignments-portal.md)來管理存取權。 這些存取權適用於 Azure 入口網站和 API 存取權。


下列活動也需要 Azure 權限︰

|  動作                                                          | 所需的 Azure 權限 | 注意 |
|-----------------------------------------------------------------|--------------------------|-------|
| 新增及移除管理解決方案                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 必須在資源群組或訂用帳戶層級授與這些權限。 |
| 變更定價層                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 檢視 [備份] 和 [Site Recovery] 解決方案圖格中的資料 | 系統管理員/共同管理員 | 存取使用傳統部署模型所部署的資源 |
| 在 Azure 入口網站中建立工作區                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>使用 Azure 權限管理對 Log Analytics 的存取
若要使用 Azure 權限授與 Log Analytics 工作區的存取權，請遵循[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)中的步驟。

Azure 有兩個適用於 Log Analytics 的內建使用者角色：
- Log Analytics 讀者
- Log Analytics 參與者

*Log Analytics 讀者*角色的成員可以：
- 檢視及搜尋所有監視資料 
- 檢視監視設定，包括檢視所有 Azure 資源上檢視的 Azure 診斷組態。

Log Analytics 讀者角色包含下列 Azure 動作：

| 類型    | 權限 | 說明 |
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

| 權限 | 說明 |
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

建議您在資源層級 (工作區) 執行指派，以確保存取控制正確無誤。  使用[自訂角色](../../role-based-access-control/custom-roles.md)建立具備所需特定權限的角色。

## <a name="next-steps"></a>後續步驟
* 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，從您資料中心或其他雲端環境中的電腦收集資料。
* 請參閱[收集關於 Azure 虛擬機器的資料](../../azure-monitor/learn/quick-collect-azurevm.md)，以進行從 Azure VM 收集資料的設定。  
* [從方案庫新增 Log Analytics 方案](../../azure-monitor/insights/solutions.md)，以新增功能和收集資料。

