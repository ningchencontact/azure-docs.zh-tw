---
title: 管理 Azure 自動化中的 Python 2 封裝
description: 本文說明如何管理 Azure 自動化中的 Python 2 封裝。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 81ce9cb2667ce9f21d7c18a92e417e47768d7efb
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407928"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>管理 Azure 自動化中的 Python 2 封裝

Azure 自動化可讓您在 Azure 和 Linux 混合式 Runbook 背景工作角色上執行 Python 2 Runbook。 若要協助簡化 Runbook，您可以使用 Python 封裝來匯入需要的模組。 本文說明如何在 Azure 自動化中管理和使用 Python 封裝。

## <a name="import-packages"></a>匯入封裝

從您的自動化帳戶中，選取 [共用資源] 下的 [Python 2 封裝]。 按一下 [+ 新增 Python 2 封裝]。

![新增 Python 封裝](media/python-packages/add-python-package.png)

在 [新增 Python 2 封裝] 頁面上，選取要上載的本機封裝。 此封裝可為 `.whl` 檔案或 `.tar.gz` 檔案。 選取後，按一下 [確定] 以上載封裝。

![新增 Python 封裝](media/python-packages/upload-package.png)

导入包之后，该包将在自动化帐户中的“Python 2 包”页中列出。 如果您需要移除封裝，請在封裝頁面上選取封裝，然後選擇 [刪除]。

![封裝清單](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>导入带依赖项的包

Azure 自动化不在导入过程中解析 Python 包的依赖项。 可以通过两种方式导入包及其所有依赖项。 只需使用下述步骤之一将包导入自动化帐户中。

### <a name="manually-download"></a>手动下载

在安装了 [python2.7](https://www.python.org/downloads/release/latest/python2) 和 [pip](https://pip.pypa.io/en/stable/) 的 Windows 64 位计算机上运行以下命令，以便下载包及其所有依赖项：

```
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

等到这些包下载以后，即可将其导入自动化帐户中。

### <a name="runbook"></a>Runbook

匯入 python runbook [pypi 到 Azure 自動化帳戶中的匯入 Python 2 套件](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509)從資源庫到您的自動化帳戶。 請確定執行的設定會設定為**Azure**和啟動 runbook 的參數。 Runbook 需要執行身分帳戶來工作的自動化帳戶。 基於每個參數，請確定您啟動它以參數中的下列清單和映像所示：

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![封裝清單](media/python-packages/import-python-runbook.png)

Runbook 可讓您指定哪些封裝要下載，比方說， `Azure` （第四個參數） 會下載所有的 Azure 模組和其所有相依性，這是即將 105。

Runbook 完成之後您可以檢查**Python 2 封裝**下方的頁面上**共用資源**在您的自動化帳戶，以確認它們封裝已正確匯入。

## <a name="use-a-package-in-a-runbook"></a>在 Runbook 中使用封裝

导入包之后，即可在 Runbook 中使用它。 下列範例會使用 [Azure 自動化公用程式封裝](https://github.com/azureautomation/azure_automation_utility)。 此封裝可讓您更輕鬆配合使用 Python 與 Azure 自動化。 若要使用此封裝，請遵循 GitHub 存放庫中的指示，並透過使用 `from azure_automation_utility import get_automation_runas_credential` 將其新增至 Runbook (例如，匯入函式以擷取 RunAs 帳戶)。

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
