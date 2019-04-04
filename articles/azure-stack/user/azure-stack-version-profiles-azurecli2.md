---
title: 使用 CLI 連線至 Azure Stack | Microsoft 文件
description: 了解如何使用跨平台命令列介面 (CLI) 在 Azure Stack 上管理及部署資源
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 21167366ff3af2bb360c33eaae9d591020bf11a5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487578"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>在 Azure Stack 中搭配 Azure CLI 使用 API 版本設定檔

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

您可以遵循本文中的步驟從 Linux、Mac 和 Windows 用戶端平台設定 Azure 命令列介面 (CLI)，來管理 Azure Stack 開發套件 (ASDK) 資源。

## <a name="prepare-for-azure-cli"></a>針對 Azure CLI 進行準備

您需要有 CA 根憑證，Azure Stack 才能在開發電腦上使用 Azure CLI。 您可以使用該憑證來透過 CLI 管理資源。

 - 如果您要從位於 ASDK 外的工作站使用 CLI，就必須要有 **Azure Stack CA 根憑證**。  

 - **虛擬機器別名端點** - 此端點提供一個在部署 VM 時，以單一參數參考映像發行者、供應項目、SKU 及版本的別名 (例如 "UbuntuLTS" 或 "Win2012Datacenter")。  

下列各節會說明如何取得這些值。

### <a name="export-the-azure-stack-ca-root-certificate"></a>匯出 Azure Stack CA 根憑證

如果您使用整合式系統，則不需要匯出 CA 根憑證。 您需要在 ASDK 上匯出 CA 根憑證。

若要以 PEM 格式匯出 ASDK 根憑證：

1. [在 Azure Stack 上建立 Windows VM](azure-stack-quick-windows-portal.md)。

2. 登入該電腦，開啟提升權限的 PowerShell 提示字元，然後執行下列指令碼：

    ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

3. 將憑證複製到本機電腦。


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>設定虛擬機器別名端點

您可以設定裝載虛擬機器別名檔案的可公開存取端點。 虛擬機器別名檔案是為映像提供通用名稱的 JSON 檔案。 您會在部署 VM 作為 Azure CLI 參數時使用此名稱。

1. 如果您發行自訂映像，請記下您在發行時所指定的發行者、供應項目、SKU 及版本資訊。 如果映像來自 Marketplace，則可以使用 ```Get-AzureVMImage``` Cmdlet 來檢視資訊。  

2. 從 GitHub 下載[範例檔案](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。

3. 在 Azure Stack 中建立儲存體帳戶。 完成時，建立 Blob 容器。 將存取原則設定為「公用」。  

4. 將 JSON 檔案上傳到新的容器。 完成時，您可以檢視 Blob 的 URL。 選取 Blob 名稱，然後選取 Blob 屬性中的 URL。

### <a name="install-or-upgrade-cli"></a>安裝或升級 CLI

登入您的開發工作站並安裝 CLI。 Azure Stack 需要有 Azure CLI 2.0 版或更新版本。 API 設定檔的最新版本需要目前的 CLI 版本。  您可以使用[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 一文中所述的步驟來安裝 CLI。 若要確認安裝是否成功，請開啟終端機或命令提示字元視窗，並執行下列命令：

```shell
az --version
```

您應該會看到 Azure CLI 的號碼和您電腦上安裝的其他相依程式庫。

### <a name="install-python-on-windows"></a>在 Windows 上安裝 Python

1. 安裝 [Python 3 到您的系統上](https://www.python.org/downloads/)。

2. 升級 PIP。 PIP 是適用於 Python 的套件管理員。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```powershell  
    python -m pip install --upgrade pip
    ```

3. 安裝 **certifi** 模組。 [Certifi](https://pypi.org/project/certifi/) 是一種模組，會收集各種根憑證以便在確認 TLS 主機的身分識別時驗證 SSL 憑證的可信度。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```powershell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>在 Linux 上安裝 Python

1. Ubuntu 16.04 映像在推出時已預設安裝 Python 2.7 和 Python 3.5。 您可以執行下列命令來確認 Python 3 的版本：

    ```bash  
    python3 --version
    ```

2. 升級 PIP。 PIP 是適用於 Python 的套件管理員。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. 安裝 **certifi** 模組。 [Certifi](https://pypi.org/project/certifi/) 會收集各種根憑證，以便在確認 TLS 主機的身分識別時驗證 SSL 憑證的可信度。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>在 macOS 上安裝 Python

1. 安裝 [Python 3 到您的系統上](https://www.python.org/downloads/)。 針對 Python 3.7 版本，Python.org 提供了兩個二進位安裝程式選項供您下載。 預設的變異版本只有 64 位元，適用於 macOS 10.9 (Mavericks) 和更新版本的系統。 請開啟終端機並輸入下列命令，來檢查您的 Python 版本：

    ```bash  
    python3 --version
    ```

2. 升級 PIP。 PIP 是適用於 Python 的套件管理員。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. 安裝 **certifi** 模組。 [Certifi](https://pypi.org/project/certifi/) 是一種模組，會收集各種根憑證以便在確認 TLS 主機的身分識別時驗證 SSL 憑證的可信度。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

在您要使用 Azure AD 作為身分識別管理服務，並在 Windows 電腦上使用 CLI 的前提下，本節可逐步引導您設定 CLI。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根憑證

如果您使用 ASDK，您將必須信任遠端機器上的 CA 根憑證。 您不需要透過整合系統執行此動作。

若要信任 Azure Stack CA 根憑證，請將它附加到現有的 Python 憑證。

1. 尋找您機器上的憑證位置。 此位置可能會根據您安裝 Python 的位置不同而有所差異。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    記下憑證位置。 例如： `~/lib/python3.5/site-packages/certifi/cacert.pem`。 您的特定路徑取決於安裝的 OS 和 Python 版本。

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
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>連線至 Azure Stack

1. 執行 `az cloud register` 命令來註冊 Azure Stack 環境。

    在某些情況下，直接輸出的網際網路連線是透過 Proxy 或防火牆進行路由傳送，這會強制執行 SSL 攔截。 在這些情況下，`az cloud register` 命令可能會失敗並發生錯誤，例如「無法從雲端取得端點」。 若要解決這個錯誤，您可以設定下列環境變數：

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. 註冊您的環境。 在執行 `az cloud register` 時使用下列參數。

    | 值 | 範例 | 說明 |
    | --- | --- | --- |
    | 環境名稱 | AzureStackUser | 若為使用者環境，請使用 `AzureStackUser`。 如果您是操作員，則請指定 `AzureStackAdmin`。 |
    | Resource Manager 端點 | https://management.local.azurestack.external | Azure Stack 開發套件 (ASDK) 中的 **ResourceManagerUrl** 為：`https://management.local.azurestack.external/` 整合系統中的 **ResourceManagerUrl** 為：`https://management.<region>.<fqdn>/` 若要擷取所需的中繼資料：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` 如果您有關於整合系統端點的問題，請連絡您的雲端操作員。 |
    | 儲存體端點 | local.azurestack.external | `local.azurestack.external` 適用於 ASDK。 若為整合系統，請使用系統的端點。  |
    | Keyvalut 後置詞 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 適用於 ASDK。 若為整合系統，請使用系統的端點。  |
    | VM 映像別名文件端點- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 虛擬機器映像別名所在文件的 URI。 如需詳細資訊，請參閱 [### 設定虛擬機器別名端點](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. 使用下列命令來設定作用中環境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. 將您的環境組態更新成使用 Azure Stack 特定的 API 版本設定檔。 若要更新組態，請執行下列命令：

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果您正在執行 Azure Stack 1808 組建之前的版本，則必須使用 API 版本設定檔 **2017-03-09-profile**，而非 API 版本設定檔 **2018-03-01-hybrid**。 您必須使用目前的 Azure CLI 版本。
 
1. 使用 `az login` 命令來登入 Azure Stack 環境。 您可以以使用者身分或以[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)形式登入 Azure Stack 環境。 

   - 以「使用者」身分登入： 

     您可以直接在 `az login` 命令內指定使用者名稱和密碼，或使用瀏覽器進行驗證。 如果您的帳戶已啟用多重要素驗證，則必須採用後者方式：

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > 如果您的使用者帳戶已啟用多重要素驗證，則可以使用 `az login` 命令，而不需提供 `-u` 參數。 執行此命令可提供您一個 URL 以及必須用來進行驗證的代碼。

   - 使用*服務主體*來登入： 
    
     在登入之前，請透過 [Azure 入口網站](azure-stack-create-service-principals.md)或 CLI 建立服務主體，並為它指派角色。 現在，請使用下列命令登入：

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>測試連線

一切都已準備就緒後，請使用 CLI 在 Azure Stack 中建立資源。 例如，您可以建立應用程式的資源群組，並新增虛擬機器。 若要建立名為 "MyResourceGroup" 的資源群組，請使用下列命令：

```azurecli
az group create -n MyResourceGroup -l local
```

如果資源群組成功建立，先前的命令會輸出新建立資源的下列內容：

![資源群組建立輸出](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

在您要使用 Active Directory Federated Services (AD FS) 作為身分識別管理服務，並在 Windows 電腦上使用 CLI 的前提下，本節可逐步引導您設定 CLI。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根憑證

如果您使用 ASDK，您將必須信任遠端機器上的 CA 根憑證。 您不需要透過整合系統執行此動作。

1. 尋找您機器上的憑證位置。 此位置可能會根據您安裝 Python 的位置不同而有所差異。 開啟 CMD 命令提示字元或已提升權限的 PowerShell 命令提示字元，並輸入下列命令：

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    記下憑證位置。 例如： `~/lib/python3.5/site-packages/certifi/cacert.pem`。 您的特定路徑取決於安裝的 OS 和 Python 版本。

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
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>連線至 Azure Stack

1. 執行 `az cloud register` 命令來註冊 Azure Stack 環境。

    在某些情況下，直接輸出的網際網路連線是透過 Proxy 或防火牆進行路由傳送，這會強制執行 SSL 攔截。 在這些情況下，`az cloud register` 命令可能會失敗並發生錯誤，例如「無法從雲端取得端點」。 若要解決這個錯誤，您可以設定下列環境變數：

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. 註冊您的環境。 在執行 `az cloud register` 時使用下列參數。

    | 值 | 範例 | 說明 |
    | --- | --- | --- |
    | 環境名稱 | AzureStackUser | 若為使用者環境，請使用 `AzureStackUser`。 如果您是操作員，則請指定 `AzureStackAdmin`。 |
    | Resource Manager 端點 | https://management.local.azurestack.external | Azure Stack 開發套件 (ASDK) 中的 **ResourceManagerUrl** 為：`https://management.local.azurestack.external/` 整合系統中的 **ResourceManagerUrl** 為：`https://management.<region>.<fqdn>/` 若要擷取所需的中繼資料：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` 如果您有關於整合系統端點的問題，請連絡您的雲端操作員。 |
    | 儲存體端點 | local.azurestack.external | `local.azurestack.external` 適用於 ASDK。 若為整合系統，請使用系統的端點。  |
    | Keyvalut 後置詞 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 適用於 ASDK。 若為整合系統，請使用系統的端點。  |
    | VM 映像別名文件端點- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 虛擬機器映像別名所在文件的 URI。 如需詳細資訊，請參閱 [### 設定虛擬機器別名端點](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. 使用下列命令來設定作用中環境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. 將您的環境組態更新成使用 Azure Stack 特定的 API 版本設定檔。 若要更新組態，請執行下列命令：

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果您正在執行 Azure Stack 1808 組建之前的版本，則必須使用 API 版本設定檔 **2017-03-09-profile**，而非 API 版本設定檔 **2018-03-01-hybrid**。 您必須使用目前的 Azure CLI 版本。

1. 使用 `az login` 命令來登入 Azure Stack 環境。 您可以以使用者身分或以[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)形式登入 Azure Stack 環境。 

   - 以「使用者」身分登入：

     您可以直接在 `az login` 命令內指定使用者名稱和密碼，或使用瀏覽器進行驗證。 如果您的帳戶已啟用多重要素驗證，則必須採用後者方式：

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
     ```

     > [!NOTE]
     > 如果您的使用者帳戶已啟用多重要素驗證，則可以使用 `az login` 命令，而不需提供 `-u` 參數。 執行此命令可提供您一個 URL 以及必須用來進行驗證的代碼。

   - 使用*服務主體*來登入： 
    
     準備要用於服務主體登入的.pem 檔案。

     在建立主體的用戶端電腦上，使用私密金鑰 (位於 `cert:\CurrentUser\My`；憑證名稱與主體名稱相同) 將服務主體憑證匯出為 pfx。

     將 pfx 轉換為 pem (使用 OpenSSL 公用程式)。

     登入 CLI：
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>測試連線

一切都已準備就緒後，請使用 CLI 在 Azure Stack 中建立資源。 例如，您可以建立應用程式的資源群組，並新增虛擬機器。 若要建立名為 "MyResourceGroup" 的資源群組，請使用下列命令：

```azurecli
az group create -n MyResourceGroup -l local
```

如果資源群組成功建立，先前的命令會輸出新建立資源的下列內容：

![資源群組建立輸出](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

在您要使用 Azure AD 作為身分識別管理服務，並在 Linux 電腦上使用 CLI 的前提下，本節可逐步引導您設定 CLI。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根憑證

如果您使用 ASDK，您將必須信任遠端機器上的 CA 根憑證。 您不需要透過整合系統執行此動作。

將 Azure Stack CA 根憑證附加到現有的 Python 憑證，以信任該根憑證。

1. 尋找您機器上的憑證位置。 此位置可能會根據您安裝 Python 的位置不同而有所差異。 您必須將 pip 和 certifi [模組安裝好](#install-python-on-linux)。 您可以從 Bash 提示中使用下列 Python 命令：

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    記下憑證位置，例如 `~/lib/python3.5/site-packages/certifi/cacert.pem`。 您的特定路徑取決於您的作業系統及所安裝 Python 的版本。

2. 使用憑證的路徑來執行下列 bash 命令。

   - 若為遠端 Linux 電腦：

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - 若為 Azure Stack 環境內的 Linux 電腦：

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>連線至 Azure Stack

使用下列步驟連線至 Azure Stack：

1. 執行 `az cloud register` 命令來註冊 Azure Stack 環境。 在某些情況下，直接輸出的網際網路連線是透過 Proxy 或防火牆進行路由傳送，這會強制執行 SSL 攔截。 在這些情況下，`az cloud register` 命令可能會失敗並發生錯誤，例如「無法從雲端取得端點」。 若要解決這個錯誤，您可以設定下列環境變數：

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. 註冊您的環境。 在執行 `az cloud register` 時使用下列參數。

    | 值 | 範例 | 說明 |
    | --- | --- | --- |
    | 環境名稱 | AzureStackUser | 若為使用者環境，請使用 `AzureStackUser`。 如果您是操作員，則請指定 `AzureStackAdmin`。 |
    | Resource Manager 端點 | https://management.local.azurestack.external | Azure Stack 開發套件 (ASDK) 中的 **ResourceManagerUrl** 為：`https://management.local.azurestack.external/` 整合系統中的 **ResourceManagerUrl** 為：`https://management.<region>.<fqdn>/` 若要擷取所需的中繼資料：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` 如果您有關於整合系統端點的問題，請連絡您的雲端操作員。 |
    | 儲存體端點 | local.azurestack.external | `local.azurestack.external` 適用於 ASDK。 若為整合系統，請使用系統的端點。  |
    | Keyvalut 後置詞 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 適用於 ASDK。 若為整合系統，請使用系統的端點。  |
    | VM 映像別名文件端點- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 虛擬機器映像別名所在文件的 URI。 如需詳細資訊，請參閱 [### 設定虛擬機器別名端點](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. 設定使用中的環境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. 將您的環境組態更新成使用 Azure Stack 特定的 API 版本設定檔。 若要更新組態，請執行下列命令：

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果您正在執行 Azure Stack 1808 組建之前的版本，則必須使用 API 版本設定檔 **2017-03-09-profile**，而非 API 版本設定檔 **2018-03-01-hybrid**。 您必須使用目前的 Azure CLI 版本。

5. 使用 `az login` 命令來登入 Azure Stack 環境。 您可以以使用者身分或以[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)形式登入 Azure Stack 環境。 

   * 以「使用者」身分登入：

     您可以直接在 `az login` 命令內指定使用者名稱和密碼，或使用瀏覽器進行驗證。 如果您的帳戶已啟用多重要素驗證，則必須採用後者方式：

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > 如果您的使用者帳戶已啟用多重要素驗證，則可以使用 `az login` 命令，而不需提供 `-u` 參數。 執行此命令可提供您一個 URL 以及必須用來進行驗證的代碼。
   
   * 以*服務主體*身分登入
    
     在登入之前，請透過 [Azure 入口網站](azure-stack-create-service-principals.md)或 CLI 建立服務主體，並為它指派角色。 現在，請使用下列命令登入：

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>測試連線

一切都已準備就緒後，請使用 CLI 在 Azure Stack 中建立資源。 例如，您可以建立應用程式的資源群組，並新增虛擬機器。 若要建立名為 "MyResourceGroup" 的資源群組，請使用下列命令：

```azurecli
    az group create -n MyResourceGroup -l local
```

如果資源群組成功建立，先前的命令會輸出新建立資源的下列內容：

![資源群組建立輸出](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

在您要使用 Active Directory Federated Services (AD FS) 作為管理服務，並在 Linux 電腦上使用 CLI 的前提下，本節可逐步引導您設定 CLI。

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根憑證

如果您使用 ASDK，您將必須信任遠端機器上的 CA 根憑證。 您不需要透過整合系統執行此動作。

將 Azure Stack CA 根憑證附加到現有的 Python 憑證，以信任該根憑證。

1. 尋找您機器上的憑證位置。 此位置可能會根據您安裝 Python 的位置不同而有所差異。 您必須將 pip 和 certifi [模組安裝好](#install-python-on-linux)。 您可以從 Bash 提示中使用下列 Python 命令：

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    記下憑證位置，例如 `~/lib/python3.5/site-packages/certifi/cacert.pem`。 您的特定路徑取決於您的作業系統及所安裝 Python 的版本。

2. 使用憑證的路徑來執行下列 bash 命令。

   - 若為遠端 Linux 電腦：

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - 若為 Azure Stack 環境內的 Linux 電腦：

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>連線至 Azure Stack

使用下列步驟連線至 Azure Stack：

1. 執行 `az cloud register` 命令來註冊 Azure Stack 環境。 在某些情況下，直接輸出的網際網路連線是透過 Proxy 或防火牆進行路由傳送，這會強制執行 SSL 攔截。 在這些情況下，`az cloud register` 命令可能會失敗並發生錯誤，例如「無法從雲端取得端點」。 若要解決這個錯誤，您可以設定下列環境變數：

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. 註冊您的環境。 在執行 `az cloud register` 時使用下列參數。

    | 值 | 範例 | 說明 |
    | --- | --- | --- |
    | 環境名稱 | AzureStackUser | 若為使用者環境，請使用 `AzureStackUser`。 如果您是操作員，則請指定 `AzureStackAdmin`。 |
    | Resource Manager 端點 | https://management.local.azurestack.external | Azure Stack 開發套件 (ASDK) 中的 **ResourceManagerUrl** 為：`https://management.local.azurestack.external/` 整合系統中的 **ResourceManagerUrl** 為：`https://management.<region>.<fqdn>/` 若要擷取所需的中繼資料：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` 如果您有關於整合系統端點的問題，請連絡您的雲端操作員。 |
    | 儲存體端點 | local.azurestack.external | `local.azurestack.external` 適用於 ASDK。 若為整合系統，請使用系統的端點。  |
    | Keyvalut 後置詞 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 適用於 ASDK。 若為整合系統，請使用系統的端點。  |
    | VM 映像別名文件端點- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 虛擬機器映像別名所在文件的 URI。 如需詳細資訊，請參閱 [### 設定虛擬機器別名端點](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. 設定使用中的環境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. 將您的環境組態更新成使用 Azure Stack 特定的 API 版本設定檔。 若要更新組態，請執行下列命令：

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >如果您正在執行 Azure Stack 1808 組建之前的版本，則必須使用 API 版本設定檔 **2017-03-09-profile**，而非 API 版本設定檔 **2018-03-01-hybrid**。 您必須使用目前的 Azure CLI 版本。

5. 使用 `az login` 命令來登入 Azure Stack 環境。 您可以以使用者身分或以[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)形式登入 Azure Stack 環境。 

6. 登入： 

   *  使用網頁瀏覽器搭配裝置代碼以**使用者**身分登入：  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >執行此命令可提供您一個 URL 以及必須用來進行驗證的代碼。

   * 以服務主體身分登入：
        
     準備要用於服務主體登入的.pem 檔案。

      * 在建立主體的用戶端電腦上，使用私密金鑰 (位於 `cert:\CurrentUser\My`；憑證名稱與主體名稱相同) 將服務主體憑證匯出為 pfx。
  
      * 將 pfx 轉換為 pem (使用 OpenSSL 公用程式)。

     登入 CLI：

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>測試連線

一切都已準備就緒後，請使用 CLI 在 Azure Stack 中建立資源。 例如，您可以建立應用程式的資源群組，並新增虛擬機器。 若要建立名為 "MyResourceGroup" 的資源群組，請使用下列命令：

```azurecli
  az group create -n MyResourceGroup -l local
```

如果資源群組成功建立，先前的命令會輸出新建立資源的下列內容：

![資源群組建立輸出](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>已知問題

在 Azure Stack 中使用 CLI 有一些已知問題：

 - Azure Stack 尚未支援 CLI 互動模式 (例如 `az interactive` 命令)。
 - 若要取得 Azure Stack 中的可用虛擬機器映像清單，請使用 `az vm image list --all` 命令，而非 `az vm image list` 命令。 指定 `--all` 選項可確保回應只會傳回您 Azure Stack 環境中可用的映像。
 - Azure 中可用的虛擬機器映像別名可能不適用於 Azure Stack。 使用虛擬機器映像時，您必須使用整個 URN 參數 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0)，而非映像別名。 此 URN 必須符合從 `az vm images list` 命令衍生的映像規格。

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 部署範本](azure-stack-deploy-template-command-line.md)
- [為 Azure Stack 使用者 (操作員) 啟用 Azure CLI](../azure-stack-cli-admin.md)
- [管理使用者權限](azure-stack-manage-permissions.md) 
