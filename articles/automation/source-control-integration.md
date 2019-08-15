---
title: Azure 自動化中的原始檔控制整合
description: 本文說明在 Azure 自動化中與 GitHub 的原始檔控制整合。
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 52fcd0d928ecbce5c617ff6a27175fccb8fd96f6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990250"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure 自動化中的原始檔控制整合

原始檔控制可讓您使用 GitHub 或 Azure Repos 原始檔控制存放庫中的腳本, 讓自動化帳戶中的 runbook 保持在最新狀態。 原始檔控制可讓您輕鬆地與小組共同作業、追蹤變更，以及回復至舊版的 Runbook。 例如，原始檔控制可讓您將原始檔控制中的不同分支同步至您的開發、測試或生產環境「自動化」帳戶。 這可讓您輕鬆地將已在開發環境中測試過的程式碼提升至生產環境「自動化」帳戶。 原始檔控制與 automation 的整合支援從您的原始檔控制儲存機制進行單一方向同步處理。

Azure 自動化支援三種類型的原始檔控制:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>先決條件

* 原始檔控制存放庫 (GitHub 或 Azure Repos)
* [執行身分帳戶](manage-runas-account.md)
* 確定您的自動化帳戶中已有[最新的 Azure 模組](automation-update-azure-modules.md)

> [!NOTE]
> 原始檔控制同步處理作業會在使用者的自動化帳戶之下執行，而且會以與其他自動化作業相同的費率計費。

## <a name="configure-source-control---azure-portal"></a>設定原始檔控制-Azure 入口網站

在您的自動化帳戶中, 選取 [**原始檔控制**], 然後按一下 [ **+ 新增**]

![選取原始檔控制](./media/source-control-integration/select-source-control.png)

選擇 [原始檔控制類型]，按一下 [驗證]。 瀏覽器視窗隨即開啟並提示您登入, 請遵循提示以完成驗證。

在 [原始檔控制摘要] 頁面上，填入資訊並按一下 [儲存]。 下表顯示可用欄位的說明。

|內容  |描述  |
|---------|---------|
|原始檔控制名稱     | 原始檔控制的易記名稱。 *此名稱必須只包含字母和數位。*        |
|原始檔控制類型     | 原始檔控制來源的類型。 可用選項包括：</br> GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
|存放庫     | 存放庫或專案的名稱。 系統會傳回前200存放庫。 若要搜尋存放庫, 請在欄位中輸入名稱, 然後按一下 [**在 GitHub 上搜尋**]。|
|分支     | 此分支可從中提取原始程式檔。 分支目標不適用於 TFVC 原始檔控制類型。          |
|資料夾路徑     | 包含要同步處理之 Runbook 的資料夾。範例：/Runbooks </br>*只會同步指定資料夾中的 runbook。不支援遞迴。*        |
|自動同步處理<sup>1</sup>     | 在原始檔控制存放庫中進行認可時開啟或關閉自動同步處理         |
|發佈 Runbook     | 如果設定為**On**, runbook 從原始檔控制同步處理之後, 就會自動發佈。         |
|描述     | 用來提供其他詳細資料的文字欄位        |

<sup>1</sup>若要在設定與 Azure Repos 的原始檔控制整合時啟用自動同步處理, 您必須是專案系統管理員。

![原始檔控制摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 您的原始檔控制儲存機制登入可能會與 Azure 入口網站的登入不同。 設定原始檔控制時, 請確定您是以正確的原始檔控制存放庫帳戶登入。 如果有所疑慮，請在您的瀏覽器中開啟新的索引標籤並從 visualstudio.com 或 github.com 登出，然後再試一次連線原始檔控制。

## <a name="configure-source-control---powershell"></a>設定原始檔控制-PowerShell

您也可以使用 PowerShell 來設定 Azure 自動化中的原始檔控制。 若要使用 PowerShell Cmdlet 設定原始檔控制, 需要個人存取權杖 (PAT)。 您可以使用[AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl)來建立原始檔控制連接。 此 Cmdlet 需要個人存取權杖的安全字串, 若要瞭解如何建立安全字串, 請參閱[convertto-html-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)。

### <a name="azure-repos-git"></a>Azure Repos (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>個人存取權杖權限

原始檔控制需要一些個人存取權杖的最基本權限。 下表包含 GitHub 和 Azure Repos 所需的最小許可權。

#### <a name="github"></a>GitHub

如需在 GitHub 中建立個人存取權杖的詳細資訊, 請造訪[為命令列建立個人存取權杖](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。

|`Scope`  |描述  |
|---------|---------|
|**存放庫**     |         |
|repo:status     | 存取認可狀態         |
|repo_deployment      | 存取部署狀態         |
|public_repo     | 存取公用存放庫         |
|**admin:repo_hook**     |         |
|write:repo_hook     | 寫入存放庫勾點         |
|read:repo_hook|讀取存放庫勾點|

#### <a name="azure-repos"></a>Azure Repos

如需在 Azure Repos 中建立個人存取權杖的詳細資訊, 請造訪[使用個人存取權杖來驗證存取權](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

|`Scope`  |
|---------|
|程式碼 (讀取)     |
|專案與小組 (讀取)|
|身分識別 (讀取)      |
|使用者設定檔 (讀取)     |
|工作項目 (讀取)    |
|服務連線 (讀取、查詢及管理)<sup>1</sup>    |

<sup>1</sup>只有在您已啟用自動同步時, 才需要服務連線許可權。

## <a name="syncing"></a>正在同步

從 [**原始檔控制**] 頁面上的資料表中選取來源。 按一下 [開始同步處理] 開始同步處理程序。

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

選取 [**原始檔控制同步處理作業摘要**] 頁面上的 [**所有記錄**], 即可取得其他記錄。 這些額外的記錄專案可協助您針對使用原始檔控制時可能發生的問題進行疑難排解。

## <a name="disconnecting-source-control"></a>中斷原始檔控制的連線

若要中斷與原始檔控制存放庫的連線, 請在自動化帳戶的 [**帳戶設定**] 下開啟**原始檔控制**。

選取您想要移除的原始檔控制。 在 [原始檔控制摘要] 頁面上，按一下 [刪除]。

## <a name="encoding"></a>編碼

如果有多位使用者正在使用不同的編輯器編輯原始檔控制存放庫中的 runbook, 就有機會遇到編碼問題。 這種情況可能會導致您的 runbook 中有不正確的字元。 若要深入瞭解這方面的資訊, 請參閱[編碼問題的常見原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>更新存取權杖

目前, 沒有任何方法可從入口網站更新原始檔控制中的存取權杖。 當您的個人存取權杖過期或撤銷之後, 您可以透過下列方式, 使用新的存取權杖來更新原始檔控制:

* 透過[REST Api](https://docs.microsoft.com/en-us/rest/api/automation/sourcecontrol/update)。
* 藉由使用[AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) Cmdlet。

## <a name="next-steps"></a>後續步驟

若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)
