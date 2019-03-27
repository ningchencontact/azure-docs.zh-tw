---
title: 在 Azure Stack 中使用 API 版本設定檔與 Python | Microsoft Docs
description: 了解如何在 Azure Stack 中使用 API 版本設定檔與 Python。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
<!-- dev: viananth -->
ms.openlocfilehash: b3bfc3072f819a92bdceb1721bb7737a3dc04cf8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078851"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>在 Azure Stack 中使用 API 版本設定檔與 Python

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

## <a name="python-and-api-version-profiles"></a>Python 和 API 版本設定檔

Python SDK 支援 API 版本設定檔以不同雲端平台 (例如，Azure Stack 和全域 Azure) 作為目標。 您可以使用 API 設定檔來建立混合式雲端的解決方案。 Python SDK 支援下列 API 設定檔：

- **最新**  
    設定檔會以 Azure 平台中所有服務提供者的最新 API 版本作為目標。
- **2018-03-01-hybrid**     
    此設定檔針對 Azure Stack 平台中所有資源提供者，以最新的 API 版本作為目標。
- **2017-03-09-profile**  
    設定檔會以 Azure Stack 所支援的資源提供者最相容的 API 版本作為目標。

   如需 API 設定檔和 Azure Stack 的詳細資訊，請參閱[管理 Azure Stack 中的 API 版本設定檔](azure-stack-version-profiles.md)。

## <a name="install-the-azure-python-sdk"></a>安裝 Azure Python SDK

1. 從[官方網站](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)安裝 Git。
2. 如需如何安裝 Python SDK 的指示，請參閱[適用於 Python 開發人員的 Azure](/python/azure/python-sdk-azure-install?view=azure-python)。
3. 如果無法使用，請建立訂用帳戶，並儲存訂用帳戶識別碼以供後續使用。 如需建立訂用帳戶的指示，請參閱[在 Azure Stack 中建立供應項目的訂用帳戶](../azure-stack-subscribe-plan-provision-vm.md)。
4. 建立服務主體，並儲存其識別碼和祕密。 如需如何建立 Azure Stack 服務主體的指示，請參閱[為 Azure Stack 提供應用程式存取](../azure-stack-create-service-principals.md)。
5. 確保服務主體在訂用帳戶中擁有參與者/擁有者角色。 如需如何對服務主體指派角色的指示，請參閱[為 Azure Stack 提供應用程式存取](../azure-stack-create-service-principals.md)。

## <a name="prerequisites"></a>必要條件

若要搭配 Azure Stack 使用 Python Azure SDK，您必須提供下列值，然後以環境變數設定值。 針對您的作業系統設定環境變數時，請參閱下表的指示。

| 值 | 環境變數 | 說明 |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| 租用戶識別碼 | AZURE_TENANT_ID | 您的 Azure Stack [租用戶識別碼](../azure-stack-identity-overview.md)的值。 |
| 用戶端識別碼 | AZURE_CLIENT_ID | 在本文上一節建立服務主體時所儲存的服務主體應用程式識別碼。 |
| 訂用帳戶識別碼 | AZURE_SUBSCRIPTION_ID | [訂用帳戶識別碼](../azure-stack-plan-offer-quota-overview.md#subscriptions)是您存取 Azure Stack 中供應項目的方式。 |
| 用戶端密碼 | AZURE_CLIENT_SECRET | 建立服務主體時所儲存的服務主體應用程式祕密。 |
| Resource Manager 端點 | ARM_ENDPOINT | 請參閱 [Azure Stack Resource Manager 端點](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)。 |
| 資源位置 | AZURE_RESOURCE_LOCATION | Azure Stack 環境的資源位置。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根憑證

如果您使用 ASDK，您將必須信任遠端機器上的 CA 根憑證。 您不需要透過整合系統執行此動作。

#### <a name="windows"></a> Windows

1. 尋找您機器上的 Python 憑證存放區位置。 此位置可能會根據您安裝 Python 的位置不同而有所差異。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    記下憑證存放區位置。 例如 *~/lib/python3.5/site-packages/certifi/cacert.pem*。 您的特定路徑取決於安裝的 OS 和 Python 版本。

2. 將 Azure Stack CA 根憑證附加到現有的 Python 憑證，以信任該根憑證。

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"

    ```

> [!NOTE]  
> 如果您以下列方式使用 virtualenv 進行 Python SDK 的開發，則您也必須將上述憑證新增至虛擬環境的憑證存放區。 其路徑可能顯示如下："..\mytestenv\Lib\site-packages\certifi\cacert.pem"



## <a name="python-samples-for-azure-stack"></a>適用於 Azure Stack 的 Python 範例

使用 Python SDK 為 Azure Stack 提供一些程式碼範例，包括：

- [管理資源和資源群組](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)。
- [管理儲存體帳戶](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)。
- [管理虛擬機器](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/)。

## <a name="python-manage-virtual-machine-sample"></a>Python 管理虛擬機器範例

您可以使用下列程式碼範例，在 Azure Stack 中執行虛擬機器的一般管理工作。 該程式碼範例示範如何：

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

若要檢閱執行這些作業的程式碼，請在 GitHub Repo [Hybrid-Compute-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM) 中查看 Python 指令碼 **example.py** 中的 **run_example()** 函式。

每個作業都會清楚地加上註解，並列印函式。 這些範例不一定要依照清單中顯示的順序排列。

## <a name="run-the-python-sample"></a>執行 Python 範例

1. 請[安裝 Python](https://www.python.org/downloads/)，如果您尚未安裝的話。 此範例 (和 SDK) 與 Python 2.7、3.4、3.5 和 3.6 相容。

2. Python 開發的一般建議是使用虛擬環境。 如需詳細資訊請，請參閱 [Python 文件](https://docs.python.org/3/tutorial/venv.html) \(英文\)。

3. 在 Python 3 上使用 "venv" 模組安裝和初始化虛擬環境 (您必須為 Python 2.7 安裝 [virtualenv](https://pypi.python.org/pypi/virtualenv))：

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. 複製存放庫：

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. 使用 pip 安裝相依性：

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. 建立[服務主體](../azure-stack-create-service-principals.md)以搭配 Azure Stack 使用。 確保您的服務主體在訂用帳戶中擁有[參與者/擁有者角色](../azure-stack-create-service-principals.md#assign-a-role)。

7. 設定下列變數，並將這些環境變數匯出到目前的殼層：

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. 若要執行此範例，Azure Stack 市集中必須有 Ubuntu 16.04-LTS 和 WindowsServer 2012-R2-DataCenter 映像存在。 這些可以是[從 Azure 下載](../azure-stack-download-azure-marketplace-item.md)或加入[平台映像存放庫](../azure-stack-add-vm-image.md)。

9. 執行範例：

    ```python
    python example.py
    ```


## <a name="next-steps"></a>後續步驟

- [Azure Python 開發中心](https://azure.microsoft.com/develop/python/)
- [Azure 虛擬機器文件](https://azure.microsoft.com/services/virtual-machines/)
- [虛擬機器的學習路徑](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- 如果您沒有 Microsoft Azure 訂用帳戶，則可以在[這裡](https://go.microsoft.com/fwlink/?LinkId=330212)建立免費的試用帳戶。
