---
title: "我在 Azure 自動化中的第一個 Python Runbook | Microsoft Docs"
description: "教學課程將逐步引導您建立、測試和發佈簡單的 Python Runbook。"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: gwallace
ms.openlocfilehash: 0cf0b1829c7e1f9b0b8be90983a705d82784c062
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="my-first-python-runbook"></a>我的第一個 Python Runbook

> [!div class="op_single_selector"]
> * [圖形化](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流程](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

本教學課程將逐步引導您在「Azure 自動化」中建立 [Python Runbook](automation-runbook-types.md#python-runbooks)。 您開始測試，並將發行的簡易 runbook。 接著您會修改 Runbook 以實際管理 Azure 資源，在此情況下會啟動 Azure 虛擬機器。 最後，您將 runbook 更穩固加入 runbook 參數。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 您會停止並啟動這部電腦，因此它不該是生產 VM。

## <a name="create-a-new-runbook"></a>建立新的 Runbook

您開始建立輸出文字的簡單 runbook *Hello World*。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。

    [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 其中大部分資產是自動包含在新自動化帳戶的模組。 您應該也擁有 [必要條件](#prerequisites)中所述的認證資產。<br>

1. 選取 [程序管理] 之下的 [Runbook]，以開啟 Runbook 清單。
2. 選取**+ 加入 runbook**來建立新的 runbook。
3. 將 Runbook 命名為「MyFirstRunbook-Python」。
4. 在此情況下，您將建立[Python runbook](automation-runbook-types.md#python-runbooks)因此選取**Python 2**如**Runbook 類型**。
5. 按一下 [建立]  來建立 Runbook 並開啟文字式編輯器。

## <a name="add-code-to-the-runbook"></a>將程式碼加入 Runbook

現在您可以新增要列印的文字"Hello World"簡單的命令：

```python
print("Hello World!")
```

按一下 [儲存] 以儲存 Runbook。

## <a name="test-the-runbook"></a>測試 Runbook

在您發佈 Runbook 之前，為了使其可用於生產環境，您想要測試以確定它可以正常運作。 測試 Runbook 時，您會執行其 **草稿** 版本，並以互動方式檢視其輸出。

1. 按一下 [測試窗格]  來開啟 [測試] 窗格。
   ![Test Pane](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. 按一下 [開始]  以開始測試。 這應該是唯一啟用的選項。
1. 隨即會建立 [Runbook 工作](automation-runbook-execution.md) ，並顯示其狀態。
   作業狀態一開始為「已排入佇列」，表示正在等候雲端中的 Runbook 背景工作可供使用。 移到*起始*時的背景工作宣告該作業，然後*執行*runbook 實際開始執行時。
1. Runbook 工作完成時，會顯示其輸出。 在此情況下，您應該會看到*Hello World*。
1. 關閉 [測試] 窗格以返回畫布。

## <a name="publish-and-start-the-runbook"></a>發佈和啟動 Runbook

您建立 runbook 仍處於草稿模式。 您必須將它發行，才能在生產環境中執行。
當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。
在此情況下，您已發佈的版本還沒有因為您剛才建立的 runbook。

1. 按一下 [發佈] 來發佈 Runbook，然後出現提示時按一下 [是]。
   ![發佈按鈕](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. 如果您向左捲動到檢視中的 runbook **Runbook**窗格現在，它會顯示**撰寫狀態**的**發佈**。
1. 捲動回到右側，檢視 [MyFirstRunbook-Python] 的窗格。
   在頂端的選項可讓我們啟動 Runbook、檢視 Runbook、加以排程來使其在未來的某個時間點啟動，或建立 [webhook](automation-webhooks.md)，以便能透過 HTTP 呼叫加以啟動。
1. 您想要啟動 runbook，因此請按**啟動**，然後按一下 **確定**啟動 Runbook 刀鋒視窗開啟時。
1. 工作窗格會開啟您建立 runbook 作業。 您可以關閉此窗格中，但在此情況下您維持開啟，因此您可以監看工作的進度。
1. 中會顯示工作狀態**工作摘要**而且符合您已看到當您測試 runbook 的狀態。
1. 一旦 Runbook 狀態顯示 [已完成]，請按一下 [輸出]。 [輸出] 窗格會開啟，而且您可以看到您*Hello World*。
1. 關閉 [輸出] 窗格。
1. 按一下 [所有記錄檔]  以開啟 Runbook 作業的 [資料流] 窗格。 您應該只會在輸出資料流中看到 Hello World ，但可能也會顯示 Runbook 作業的其他資料流，例如 Runbook 寫入時發生的詳細資訊和錯誤。
1. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 [MyFirstRunbook-Python] 窗格。
1. 按一下 [作業]  以開啟此 Runbook 的 [工作] 窗格。 這樣會列出此 Runbook 所建立的所有工作。 由於您只執行一次作業，因此應該只會看到列出一項作業。
1. 您可以按一下此作業，以開啟您啟動 Runbook 時所檢視的相同 [作業] 窗格。 這可讓您回到過去的時間並檢視針對特定 Runbook 所建立的任何工作的詳細資料。

## <a name="add-authentication-to-manage-azure-resources"></a>加入驗證來管理 Azure 資源

您已測試並發行您的 Runbook，但是到目前為止，它似乎並不實用。 您想要讓它管理 Azure 資源。
若要管理 Azure 資源，指令碼必須使用來自[自動化帳戶](automation-offering-get-started.md)的認證進行驗證。

> [!NOTE]
> 必須使用服務主體功能建立自動化帳戶，才會有 runas 憑證。
> 如果您的自動化帳戶不是透過服務主體建立，您可以使用[使用適用於 Python 的 Azure 管理程式庫](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate)所述的方法來進行驗證。

1. 按一下 [MyFirstRunbook-Python] 窗格上的 [編輯] 以開啟文字編輯器。
1. 加入下列程式碼，對 Azure 進行驗證：
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>加入程式碼以建立 Python 計算用戶端，並啟動虛擬機器

若要使用 Azure 虛擬機器，請建立 [適用於 Python 的 Azure 計算用戶端](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python)的執行個體。

您可以使用計算用戶端來啟動虛擬機器。 將下列程式碼加入 Runbook：

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

其中 _MyResourceGroup_ 是包含虛擬機器的資源群組名稱，而 _TestVM_ 是您要啟動的虛擬機器名稱。 

測試並再次執行 Runbook，看看它是否啟動虛擬機器。

## <a name="use-input-parameters"></a>使用輸入參數

Runbook 目前針對資源群組和虛擬機器名稱使用硬式編碼值。
現在讓我們來新增程式碼，以從輸入參數獲取這些值。

您使用`sys.argv`變數，以取得參數值。
在其他 `import` 陳述式之後，立即將下列程式碼加入 Runbook：

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

這樣會匯入 `sys` 模組，並建立兩個變數來保留資源群組和虛擬機器名稱。
請注意，引數清單的元素 `sys.argv[0]` 是指令碼名稱，而不是由使用者輸入。

您現在可以修改要使用的輸入的參數值，而不要使用硬式編碼值之 runbook 的最後兩行：

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

當您啟動 Python Runbook (在 [測試] 刀鋒視窗或做為已發佈 Runbook) 時，您可以在 [參數] 下的 [啟動 Runbook] 刀鋒視窗中輸入參數值。

![參數值方塊](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

在第一個方塊中輸入值之後，會出現第二個方塊，以此類推，以便您可以視需要輸入許多參數值。

有效值為指令碼中使用`sys.argv`如您剛才加入的程式碼所示的陣列。

輸入您的資源群組名稱做為第一個參數值，並輸入要啟動之虛擬機器的名稱做為第二個參數值。

![輸入參數值](media/automation-first-runbook-textual-python/runbook-python-params.png)

按一下 [確定] 啟動 Runbook。 隨即執行 Runbook，並開始您所指定的虛擬機器。

## <a name="next-steps"></a>後續步驟

* 若要開始使用 PowerShell Runbook，請參閱 [我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)
* 若要開始使用圖形化 Runbook，請參閱 [我的第一個圖形化 Runbook](automation-first-runbook-graphical.md)
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱 [我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)
* 若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)
* 若要深入了解 Azure with Python 的開發，請參閱 [Azure with Python 開發人員](https://docs.microsoft.com/python/azure/?view=azure-python)。
* 若要檢視範例 Python 2 Runbook，請參閱 [Azure 自動化 GitHub](https://docs.microsoft.com/python/azure/?view=azure-python)。
