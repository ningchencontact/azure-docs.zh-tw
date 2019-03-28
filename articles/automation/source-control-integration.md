---
title: Azure 自動化中的原始檔控制整合
description: 本文說明在 Azure 自動化中與 GitHub 的原始檔控制整合。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3b2df5b24a12f3d2ea5d8a03721c08f8d2a742ad
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58539984"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure 自動化中的原始檔控制整合

原始檔控制可讓您保留您的 runbook 在您的自動化帳戶是最新的 GitHub 或 Azure 儲存機制來源控制存放庫中指令碼。 原始檔控制可讓您輕鬆地與小組共同作業、追蹤變更，以及回復至舊版的 Runbook。 例如，原始檔控制可讓您將原始檔控制中的不同分支同步至您的開發、測試或生產環境「自動化」帳戶。 這可讓您輕鬆地將已在開發環境中測試過的程式碼提升至生產環境「自動化」帳戶。 使用自動化的原始檔控制整合支援單向同步處理從您的原始檔控制儲存機制。

Azure 自動化支援三種類型的原始檔控制：

* GitHub
* Azure 儲存機制 (Git)
* Azure 儲存機制 (TFVC)

## <a name="pre-requisites"></a>先決條件

* 原始檔控制儲存機制 （GitHub 或 Azure 儲存機制）
* A[執行身分帳戶](manage-runas-account.md)
* 請確定您已[最新的 Azure 模組](automation-update-azure-modules.md)在您的自動化帳戶

> [!NOTE]
> 原始檔控制同步處理作業會在使用者的自動化帳戶之下執行，而且會以與其他自動化作業相同的費率計費。

## <a name="configure-source-control---azure-portal"></a>設定原始檔控制-Azure 入口網站

在您的自動化帳戶，選取**原始檔控制**，按一下  **+ 新增**

![選取原始檔控制](./media/source-control-integration/select-source-control.png)

選擇 [原始檔控制類型]，按一下 [驗證]。 瀏覽器視窗隨即開啟，並提示您登入，請遵循提示以完成驗證。

在 [原始檔控制摘要] 頁面上，填入資訊並按一下 [儲存]。 下表顯示可用欄位的說明。

|屬性  |描述  |
|---------|---------|
|原始檔控制名稱     | 原始檔控制的易記名稱        |
|原始檔控制類型     | 原始檔控制來源的類型。 可用選項包括：</br> GitHub</br>Azure 儲存機制 (Git)</br> Azure 儲存機制 (TFVC)        |
|存放庫     | 存放庫或專案的名稱。 會傳回前 200 個儲存機制。 若要搜尋存放庫，在欄位中輸入名稱，然後按一下**GitHub 上的搜尋**。|
|分支     | 此分支可從中提取原始程式檔。 目標分支不適用於 TFVC 原始檔控制類型。          |
|資料夾路徑     | 包含要同步處理之 Runbook 的資料夾。範例：/Runbooks </br>*只有在指定的資料夾中的 runbook 會同步處理。不支援遞迴。*        |
|自動同步處理     | 在原始檔控制存放庫中進行認可時開啟或關閉自動同步處理         |
|發佈 Runbook     | 如果設定為**上**之後從原始檔控制它們將會自動發佈的 runbook 會同步處理。         |
|描述     | 用來提供其他詳細資料的文字欄位        |

![原始檔控制摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 確定您在設定原始檔控制時使用了正確的帳戶登入。 如果有所疑慮，請在您的瀏覽器中開啟新的索引標籤並從 visualstudio.com 或 github.com 登出，然後再試一次連線原始檔控制。

## <a name="configure-source-control---powershell"></a>設定原始檔控制-PowerShell

您也可以使用 PowerShell 在 Azure 自動化中設定原始檔控制。 若要使用 PowerShell cmdlet 設定原始檔控制，需要個人存取權杖 (PAT)。 您使用[新增 AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl)建立原始檔控制連接。 此 cmdlet 需要安全字串的個人存取權杖，以了解如何建立安全字串，請參閱 < [Convertto-securestring](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)。

### <a name="azure-repos-git"></a>Azure 儲存機制 (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure 儲存機制 (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>個人存取權杖權限

原始檔控制需要一些個人存取權杖的最基本權限。 下表包含 GitHub 和 Azure 儲存機制所需的最低權限。

#### <a name="github"></a>GitHub

如需有關如何建立 GitHub 個人存取權杖的詳細資訊，請瀏覽[建立個人存取權杖以供命令列](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。

|影響範圍  |描述  |
|---------|---------|
|**存放庫**     |         |
|repo:status     | 存取認可狀態         |
|repo_deployment      | 存取部署狀態         |
|public_repo     | 存取公用存放庫         |
|**admin:repo_hook**     |         |
|write:repo_hook     | 寫入存放庫勾點         |
|read:repo_hook|讀取存放庫勾點|

#### <a name="azure-repos"></a>Azure Repos

如需有關如何建立 Azure 儲存機制中的個人存取權杖的詳細資訊，請瀏覽[驗證使用個人存取權杖的存取](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

|影響範圍  |
|---------|
|程式碼 (讀取)     |
|專案與小組 (讀取)|
|身分識別 (讀取)      |
|使用者設定檔 (讀取)     |
|工作項目 (讀取)    |
|服務連線 (讀取、查詢及管理)<sup>1</sup>    |

<sup>1</sup>服務連線權限，才需要您是否已啟用自動同步。

## <a name="syncing"></a>同步處理

在選取資料表的來源**原始檔控制**頁面。 按一下 [開始同步處理] 開始同步處理程序。

您可以按一下 [同步處理作業] 索引標籤，以檢視目前或先前同步處理作業的狀態。在 [原始檔控制] 下拉式清單中選取原始檔控制。

![同步處理狀態](./media/source-control-integration/sync-status.png)

按一下作業可讓您檢視作業輸出。 以下範例是原始檔控制同步作業的輸出。

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

額外的記錄功能使用，請選取**所有的記錄檔**上**原始檔控制同步處理作業摘要**頁面。 這些額外的記錄項目可協助您疑難排解使用原始檔控制時可能遇到的問題。

## <a name="disconnecting-source-control"></a>中斷原始檔控制的連線

若要中斷連線的原始檔控制存放庫，請開啟**原始檔控制**下方**帳戶設定**自動化帳戶中。

選取您想要移除的原始檔控制。 在 [原始檔控制摘要] 頁面上，按一下 [刪除]。

## <a name="encoding"></a>編碼

如果多人正在使用不同的編輯器編輯您的原始檔控制儲存機制中的 runbook，就沒有機會執行為編碼問題。 這種情況可能會導致不正確的字元，在您的 runbook。 若要深入了解，請參閱[常見編碼問題的原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>後續步驟

若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)
