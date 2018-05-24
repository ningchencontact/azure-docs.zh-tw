---
title: 在 Azure Stack 中使用 API 版本設定檔與 Python | Microsoft Docs
description: 了解如何在 Azure Stack 中使用 API 版本設定檔與 Python。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2018
ms.locfileid: "32310296"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>在 Azure Stack 中使用 API 版本設定檔與 Python

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

## <a name="python-samples-for-azure-stack"></a>適用於 Azure Stack 的 Python 範例 

您可以使用下列程式碼範例，在 Azure Stack 中執行虛擬機器的一般管理工作。

程式碼範例會顯示您可以：

- 建立虛擬機器：
    - 建立 Linux 虛擬機器
    - 建立 Windows 虛擬機器
- 更新虛擬機器：
    - 展開磁碟機
    - 標記虛擬機器
    - 連接資料磁碟
    - 卸離資料磁碟
- 操作虛擬機器：
    - 啟動虛擬機器
    - 停止虛擬機器
    - 重新啟動虛擬機器
- 列出虛擬機器
- 刪除虛擬機器

若要檢閱程式碼來執行這些作業，請在 GitHub Repo [virtual-machines-python-manage](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88) 中查看 Python 指令碼 **Hybrid/unmanaged-disks/example.py** 中的 **run_example()** 函式。

每個作業都會清楚地加上註解，並列印函式。
這些範例不一定依照上述清單中顯示的順序排列。


## <a name="run-the-python-sample"></a>執行 Python 範例

1.  如果還沒有 Python，請[安裝](https://www.python.org/downloads/)。

    此範例 (和 SDK) 與 Python 2.7、3.4、3.5 和 3.6 相容。

2.  Python 開發的一般建議是使用虛擬環境。 
    如需詳細資訊，請參閱 https://docs.python.org/3/tutorial/venv.html
    
    在 Python 3 上使用 "venv" 模組安裝和初始化虛擬環境 (您必須為 Python 2.7 安裝 [virtualenv](https://pypi.python.org/pypi/virtualenv))：

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  複製儲存機制。

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  安裝使用 pip 的相依性。

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  建立[服務主體](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals)以搭配 Azure Stack 使用。 確保您的服務主體在訂用帳戶中擁有[參與者/擁有者角色](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)。

6.  設定下列變數，並將這些環境變數匯出到目前的殼層。 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  請注意，若要執行此範例，Azure Stack 市場中必須存在 Ubuntu 16.04-LTS 和 WindowsServer 2012-R2-Datacenter 映像。 這些可以是[從 Azure 下載](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item)或[加入平台映像儲存機制](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image)。


8. 執行範例。

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>注意

您可能會想要再試一次使用 `virtual_machine.storage_profile.os_disk` 擷取 VM 的 OS 磁碟。
在某些情況下，如此可能會達成您的目的。但請注意，它會產生一個 `OSDisk` 物件。
為了更新 OS 磁碟的大小，與 `example.py` 一樣，您不需要 `OSDisk` 物件，而是 `Disk` 物件。
`example.py` 取得包含下列項目的 `Disk` 物件：

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>後續步驟

- [Azure Python 開發中心](https://azure.microsoft.com/develop/python/)
- [Azure 虛擬機器文件](https://azure.microsoft.com/services/virtual-machines/)
- [虛擬機器的學習路徑](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- 如果您沒有 Microsoft Azure 訂用帳戶，則可以在[此處](http://go.microsoft.com/fwlink/?LinkId=330212)建立免費的試用帳戶。
