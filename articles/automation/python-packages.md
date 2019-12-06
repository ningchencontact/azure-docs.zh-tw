---
title: 管理 Azure 自動化中的 Python 2 封裝
description: 本文說明如何管理 Azure 自動化中的 Python 2 封裝。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f20865b92df2a197410f209cf921c5e573723286
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850188"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>管理 Azure 自動化中的 Python 2 封裝

Azure 自動化可讓您在 Azure 和 Linux 混合式 Runbook 背景工作角色上執行 Python 2 Runbook。 若要協助簡化 Runbook，您可以使用 Python 封裝來匯入需要的模組。 本文說明如何在 Azure 自動化中管理和使用 Python 封裝。

## <a name="import-packages"></a>匯入封裝

從您的自動化帳戶中，選取 [共用資源] 下的 [Python 2 封裝]。 按一下 [+ 新增 Python 2 封裝]。

![新增 Python 封裝](media/python-packages/add-python-package.png)

在 [新增 Python 2 封裝] 頁面上，選取要上載的本機封裝。 此封裝可為 `.whl` 檔案或 `.tar.gz` 檔案。 選取後，按一下 [確定] 以上載封裝。

![新增 Python 封裝](media/python-packages/upload-package.png)

匯入套件之後，它會列在您的自動化帳戶中的 [ **Python 2 封裝**] 頁面上。 如果您需要移除封裝，請在封裝頁面上選取封裝，然後選擇 [刪除]。

![封裝清單](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>匯入具有相依性的套件

在匯入過程中，Azure 自動化不會解析 python 套件的相依性。 有兩種方式可匯入套件及其所有相依性。 只有下列其中一個步驟需要用來將套件匯入至您的自動化帳戶。

### <a name="manually-download"></a>手動下載

在安裝[python 2.7](https://www.python.org/downloads/release/latest/python2)和[pip](https://pip.pypa.io/en/stable/)的 Windows 64 位電腦上，執行下列命令以下載套件及其所有相依性：

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

下載套件之後，您就可以將它們匯入到您的自動化帳戶。

### <a name="runbook"></a>Runbook

匯入 python runbook 將[python 2 套件從 pypi](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509)匯入到您的自動化帳戶，從資源庫匯入到 Azure 自動化帳戶。 請確定回合設定已設定為**Azure** ，並使用參數啟動 runbook。 Runbook 需要執行身分帳戶，自動化帳戶才能運作。 針對每個參數，請確定您已使用交換器來啟動它，如下列清單和影像所示：

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![封裝清單](media/python-packages/import-python-runbook.png)

Runbook 可讓您指定要下載的套件，例如 `Azure` （第四個參數）會下載所有 Azure 模組及其所有相依性，大約是105。

Runbook 完成後，您可以在您的自動化帳戶中，檢查 [**共用資源**] 底下的 [ **Python 2 套件**] 頁面，確認是否已正確匯入套件。

## <a name="use-a-package-in-a-runbook"></a>在 Runbook 中使用封裝

匯入套件之後，您現在可以在 runbook 中使用它。 下列範例會使用 [Azure 自動化公用程式封裝](https://github.com/azureautomation/azure_automation_utility)。 此封裝可讓您更輕鬆配合使用 Python 與 Azure 自動化。 若要使用此封裝，請遵循 GitHub 存放庫中的指示，並透過使用 `from azure_automation_utility import get_automation_runas_credential` 將其新增至 Runbook (例如，匯入函式以擷取 RunAs 帳戶)。

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>離線開發和測試 Runbook

若要離線開發和測試 Python 2 Runbook，您可以使用 GitHub 上的 [Azure 自動化 Python 模擬資產模組](https://github.com/azureautomation/python_emulated_assets)。 此模組可讓您參考共用的資源，例如認證、變數、連線和憑證。

## <a name="next-steps"></a>後續步驟

若要開始使用 Python 2 Runbook，請參閱[我的第一個 Python 2 Runbook](automation-first-runbook-textual-python2.md)
