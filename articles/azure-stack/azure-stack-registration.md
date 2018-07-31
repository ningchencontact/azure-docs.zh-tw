---
title: 向 Azure 註冊 Azure Stack 整合式系統 | Microsoft Docs
description: 說明多節點 Azure Stack Azure 連線部署的 Azure 註冊程序。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: a5e31df435d5e9af8543301e7a4540faa3d6410f
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258060"
---
# <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack

向 Azure 註冊 [Azure Stack](azure-stack-poc.md)，即可從 Azure 下載市集項目，並設定向 Microsoft 回報商務資料的功能。 註冊 Azure Stack 之後，使用方式會回報給 Azure 商務報告，您可以在註冊所用的訂用帳戶中查看報告。

> [!IMPORTANT]  
> 必須註冊才能支援完整的 Azure Stack 功能，包括 Marketplace 摘要整合。 此外，如果您在使用隨用隨付計費模型時未註冊，則會違反 Azure Stack 授權條款。 若要深入了解 Azure Stack 授權模型，請參閱[如何購買頁面](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)。

## <a name="prerequisites"></a>必要條件

使用 Azure 註冊 Azure Stack 之前，您必須：

- Azure 訂用帳戶的訂用帳戶 ID。 若要取得 ID，請登入 Azure 並按一下 [更多服務]  >  [訂用帳戶]，然後按一下您要使用的訂用帳戶，便可在 [基本資訊] 下找到 [訂用帳戶 ID]。

  > [!NOTE]
  > 目前不支援德國和美國政府雲端訂用帳戶。

- 訂用帳戶擁有者的帳戶使用者名稱和密碼 (支援 MSA/2FA 帳戶)。
- 註冊 Azure Stack 資源提供者 (請參閱下面的「註冊 Azure Stack 資源提供者」一節以取得詳細資料)。

如果您沒有符合這些需求的 Azure 訂用帳戶，則可以[在這裡建立免費的 Azure 帳戶](https://azure.microsoft.com/free/?b=17.06)。 註冊 Azure Stack 不會對您的 Azure 訂用帳戶收取任何費用。

### <a name="powershell-language-mode"></a>PowerShell 語言模式

若要成功註冊 Azure Stack，PowerShell 語言模式必須設定為 **FullLanguageMode**。  若要確認目前的語言模式設定為完整，請開啟已提升權限的 PowerShell 視窗，並執行下列 PowerShell 命令：

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

確定輸出有傳回 **FullLanguageMode**。 如果傳回任何其他語言模式，則必須在另一部機器上執行註冊，或是必須將語言模式設定為 **FullLanguageMode**，才能繼續。

### <a name="bkmk_powershell"></a>安裝適用於 Azure Stack 的 PowerShell

您必須使用適用於 Azure Stack 的最新 PowerShell 向 Azure 註冊。

如果尚未安裝，[安裝適用於 Azure Stack 的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)。

### <a name="bkmk_tools"></a>下載 Azure Stack 工具

Azure Stack 工具 GitHub 存放庫包含可支援 Azure Stack 功能 (包括註冊功能) 的 PowerShell 模組。 在登錄程序期間，您必須匯入並使用 RegisterWithAzure.psm1 PowerShell 模組 (位於 Azure Stack 工具存放庫) 向 Stack 註冊您的 Azure Stack 執行個體。

為確保使用的是最新版本，請刪除任何現有的 Azure Stack 工具版本，並[從 GitHub 下載最新版本](azure-stack-powershell-download.md)，再向 Azure 註冊。

## <a name="register-azure-stack-in-connected-environments"></a>在已連線的環境中註冊 Azure Stack

已連線的環境可以存取網際網路和 Azure。 在這類環境中，您必須向 Azure 註冊 Azure Stack 資源提供者，然後設定計費模型。

> [!NOTE]
> 這些步驟全都必須從可存取具有特殊權限之端點的電腦執行。

### <a name="register-the-azure-stack-resource-provider"></a>註冊 Azure Stack 資源提供者

若要向 Azure 註冊 Azure Stack 資源提供者，請以系統管理員身分啟動 PowerShell ISE，然後使用下列 PowerShell 命令，並將 **EnvironmentName** 參數設定為適當的 Azure 訂用帳戶類型 (請參閱以下參數)。

1. 新增您用來註冊 Azure Stack 的 Azure 帳戶。 若要新增帳戶，請執行 **Add-AzureRmAccount** Cmdlet。 當系統提示您輸入 Azure 全域系統管理員帳戶認證時，您可能需要根據帳戶的組態使用雙因素驗證。

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | 參數 | 說明 |  
   |-----|-----|
   | EnvironmentName | Azure 雲端訂用帳戶環境名稱。 支援的環境名稱為 **AzureCloud**，或者，如果使用中國 Azure 訂用帳戶，則可使用 **AzureChinaCloud**。  |
   |  |  |

2. 如果您有多個訂用帳戶，請執行下列命令以選取您要使用的訂用帳戶：  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. 如需在您的 Azure 訂用帳戶中註冊 Azure Stack 資源提供者，請執行下列命令：

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>向 Azure 註冊「使用時付費」計費模型的 Azure Stack

使用這些步驟，向 Azure 註冊「使用時付費」計費模型的 Azure Stack。

1. 以系統管理員身分啟動 PowerShell ISE，並瀏覽至您[下載 Azure Stack 工具](#bkmk_tools)時所建 **AzureStack-Tools-master** 目錄中的 **Registration** 資料夾。 使用 PowerShell 匯入 **RegisterWithAzure.psm1** 模組：

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 接下來，在相同的 PowerShell 工作階段中，確定您已登入正確的 Azure PowerShell 環境。 這是用來註冊上述 Azure Stack 資源提供者的 Azure 帳戶。 要執行的 Powershell：

   ```PowerShell  
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. 在相同的 PowerShell 工作階段中，執行 **Set-AzsRegistration** Cmdlet。 要執行的 PowerShell：  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
      -RegistrationName $RegistrationName
   ```

  |參數|說明|
  |-----|-----|
  |PrivilegedEndpointCredential|用來[存取具有特殊權限的端點](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的認證。 使用者名稱的格式為 **AzureStackDomain\CloudAdmin**。|
  |PrivilegedEndpoint|一個預先設定的遠端 PowerShell 主控台，可為您提供記錄收集和其他後續部署工作之類的功能。 若要深入了解，請參閱[使用具有特殊權限的端點](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。|
  |BillingModel|您的訂用帳戶所使用的計費模型。 此參數允許的值：Capacity、PayAsYouUse 和 Development。|
  | RegistrationName | 如果您要在多個 Azure Stack 執行個體上，使用相同的 Azure 訂用帳戶識別碼執行註冊指令碼，請為註冊設定唯一的名稱。 此參數的預設值是 **AzureStackRegistration**。 不過，如果您在多個 Azure Stack 執行個體上使用相同的名稱，指令碼將會失敗。 |

  處理程序將需要 10 到 15 分鐘的時間。 當命令完成時，您會看到此訊息：**「您的環境現在已使用提供的參數註冊並啟動」**。

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>向 Azure 註冊使用容量計費模型的 Azure Stack

遵循用於註冊「使用時付費」計費模型的相同指示，但新增購買容量時所依據的合約編號並將 **BillingModel** 參數變更為 [Capacity (容量)\]。 所有其他參數皆不變。

要執行的 PowerShell：

```PowerShell  
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
$RegistrationName = "<unique-registration-name>"
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
    -RegistrationName $RegistrationName
```

## <a name="register-azure-stack-in-disconnected-environments"></a>在已中斷連線的環境中註冊 Azure Stack
如果您要在已中斷連線的環境 (沒有網際網路連線能力) 中註冊 Azure Stack，就需要從 Azure Stack 環境中取得註冊權杖，然後在可以連線到 Azure 並[已安裝適用於 Azure Stack 之 PowerShell](#bkmk_powershell) 的電腦上使用該權杖。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>從 Azure Stack 環境中取得註冊權杖

1. 以系統管理員身分啟動 PowerShell ISE，並瀏覽至您[下載 Azure Stack 工具](#bkmk_tools)時所建 **AzureStack-Tools-master** 目錄中的 **Registration** 資料夾。 匯入 **RegisterWithAzure.psm1** 模組：  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 若要取得註冊權杖，請執行下列 PowerShell 命令：  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > 註冊權杖會儲存在針對 $FilePathForRegistrationToken 指定的檔案。 您可以自行變更檔案路徑或檔名。

3. 儲存此註冊權杖，以在連線 Azure 的電腦上使用。 您可以從 $FilePathForRegistrationToken 複製檔案或文字。

### <a name="connect-to-azure-and-register"></a>連線至 Azure 並註冊

在連線至網際網路的電腦上，執行相同的步驟，以匯入 RegisterWithAzure.psm1 模組，並登入正確的 Azure Powershell 環境。 接著，呼叫 Register-AzsEnvironment。 指定要向 Azure 註冊的註冊權杖。 如果您要使用相同的 Azure 訂用帳戶識別碼註冊多個 Azure Stack 執行個體，請指定一個唯一的註冊名稱。 執行下列 Cmdlet：

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

(選擇性) 您可以使用 Get-content cmdlet 來指向包含您註冊權杖的檔案：

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!NOTE]  
  > 儲存註冊資源名稱和註冊權杖，供日後參考。

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>從 Azure 註冊資源擷取啟用金鑰

接著，進行 Register-AzsEnvironment 時，您必須從 Azure 中建立的註冊資源中擷取啟用金鑰。

若要取得啟用金鑰，請執行下列 PowerShell 命令：  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > 啟用金鑰會儲存在為 *$KeyOutputFilePath* 指定的檔案中。 您可以自行變更檔案路徑或檔名。

### <a name="create-an-activation-resource-in-azure-stack"></a>在 Azure Stack 中建立啟用資源

從 Get-AzsActivationKey 建立的啟用金鑰中取得檔案或文字後，返回 Azure Stack 環境。 接下來，您將使用該啟動金鑰在 Azure Stack 中建立啟用資源。 若要建立啟用資源，請執行下列 PowerShell 命令：  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

(選擇性) 您可以使用 Get-content cmdlet 來指向包含您註冊權杖的檔案：

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>驗證 Azure Stack 註冊

使用下列步驟確認 Azure Stack 已成功向 Azure 註冊。

1. 登入 Azure Stack [系統管理員入口網站](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal)：https&#58;//adminportal.*&lt;region>.&lt;fqdn>*。
2. 按一下 [更多服務] > [Marketplace 管理] > [從 Azure 新增]。

如果您看到 Azure 提供的項目清單 (例如 WordPress)，則表示啟用已成功。 不過，在已中斷連線的環境中，您不會在Azure Stack 市集中看到 Azure 市集項目。

> [!NOTE]
> 註冊完成後，就不會再出現未註冊的作用中警告。

## <a name="renew-or-change-registration"></a>續訂或變更註冊

### <a name="renew-or-change-registration-in-connected-environments"></a>在已連線的環境中更新或變更註冊

在下列情況下，您需要更新或續訂您的註冊：

- 在您續訂以容量為基礎的年度訂用帳戶之後。
- 當您變更計費模型時。
- 當您調整以容量為基礎的計費變更 (新增/移除節點) 時。

#### <a name="change-the-subscription-you-use"></a>變更您使用的訂用帳戶

如果要變更您所使用的訂用帳戶，就必須先執行 **Remove-AzsRegistration** Cmdlet，然後確保您已登入正確的 Azure PowerShell 內容，最後使用任何已變更的參數來執行 **Set-AzsRegistration**：

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>變更計費模式或摘要整合功能

如果您要變更安裝的計費模式或摘要整合功能，可以呼叫註冊函式來設定新的值。 您不需要先移除目前的註冊：

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>在已中斷連線的環境中更新或變更註冊

在下列情況下，您需要更新或續訂您的註冊：

- 在您續訂以容量為基礎的年度訂用帳戶之後。
- 當您變更計費模型時。
- 當您調整以容量為基礎的計費變更 (新增/移除節點) 時。

#### <a name="remove-the-activation-resource-from-azure-stack"></a>從 Azure Stack 中移除啟用資源

您必須先從 Azure Stack 中移除啟用資源，然後再移除 Azure 中的註冊資源。  

若要移除 Azure Stack 中的啟用資源，請在您的 Azure Stack 環境中執行下列 PowerShell 命令：  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

接下來，若要移除 Azure 中的註冊資源，請確保您的電腦已與 Azure 連線，然後登入正確的 Azure PowerShell 環境，並執行適當的 PowerShell 命令，如下所述。

您可以使用用來建立資源的註冊權杖：  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

或者，您可以使用註冊名稱：

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>使用未連線的步驟重新註冊

您現在已在未連線的狀況下完成取消註冊，而您必須在未連線的狀況下，執行重複的步驟來註冊 Azure Stack 環境。

## <a name="next-steps"></a>後續步驟

[從 Azure 下載市集項目](azure-stack-download-azure-marketplace-item.md)。
