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
ms.date: 01/16/2019
ms.author: jeffgilb
ms.reviewer: brbartle
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: d9ab89afba2b83f99bfbf432d033cd0546a25a9d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247386"
---
# <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack

向 Azure 註冊 Azure Stack 可讓您從 Azure 下載市集項目，並設定向 Microsoft 回報商務資料的功能。 註冊 Azure Stack 之後，使用方式會回報給 Azure 商務報告，您可以在註冊所用的訂用帳戶中查看報告。

本文中的資訊說明如何向 Azure 註冊 Azure Stack 整合系統。 如需向 Azure 註冊 ASDK 的相關資訊，請參閱 ASDK 文件中的 [Azure Stack 註冊](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register)。

> [!IMPORTANT]  
> 必須註冊才能支援完整的 Azure Stack 功能，包括在 Marketplace 中提供項目。 此外，如果您在使用隨用隨付計費模型時未註冊，則會違反 Azure Stack 授權條款。 若要深入了解 Azure Stack 授權模型，請參閱[如何購買頁面](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)。

## <a name="prerequisites"></a>必要條件

在您進行註冊之前，必須先做好下列準備：

 - 確認認證
 - 設定 PowerShell 語言模式
 - 安裝適用於 Azure Stack 的 PowerShell
 - 下載 Azure Stack 工具
 - 判斷您的註冊案例

### <a name="verify-your-credentials"></a>確認認證

使用 Azure 註冊 Azure Stack 之前，您必須：

- Azure 訂用帳戶的訂用帳戶 ID。 只有 EA、CSP 或 CSP 共用服務訂用帳戶支援註冊。 CSP 必須決定要[使用 CSP 或 APSS 訂用帳戶](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)。<br><br>若要取得識別碼，請登入 Azure，按一下 [所有服務]。 然後在 [一般] 分類底下，選取 [訂用帳戶]，按一下您要使用的訂用帳戶，便可在 [基本資訊] 下找到 [訂用帳戶識別碼]。

  > [!Note]  
  > 目前不支援德國雲端訂用帳戶。

- 訂用帳戶擁有者的帳戶使用者名稱和密碼。

- 使用者帳戶必須具有 Azure 訂用帳戶的存取權，而且有權在與該訂用帳戶相關聯的目錄中建立身分識別應用程式和服務主體。 建議您[建立註冊用的服務帳戶](azure-stack-registration-role.md)以使用最低權限管理向 Azure 註冊 Azure Stack，而不要使用全域管理員認證。

- 註冊 Azure Stack 資源提供者 (請參閱後續的「註冊 Azure Stack 資源提供者」一節以取得詳細資料)。

註冊之後，就不需要 Azure Active Directory 全域管理員權限。 不過，某些作業可能需要全域管理員認證。 例如，需要獲派權限的資源提供者安裝程式指令碼或新功能。 您可以暫時恢復帳戶的全域管理員權限，或使用擁有「預設提供者訂用帳戶」的個別全域管理員帳戶。

如果您沒有符合這些需求的 Azure 訂用帳戶，則可以[在這裡建立免費的 Azure 帳戶](https://azure.microsoft.com/free/?b=17.06)。 註冊 Azure Stack 不會對您的 Azure 訂用帳戶收取任何費用。

### <a name="powershell-language-mode"></a>PowerShell 語言模式

若要成功註冊 Azure Stack，PowerShell 語言模式必須設定為 **FullLanguageMode**。  若要確認目前的語言模式是否已設定為完整，請開啟已提升權限的 PowerShell 視窗，然後執行下列 PowerShell Cmdlet：

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

確定輸出有傳回 **FullLanguageMode**。 如果傳回任何其他語言模式，則必須在另一部機器上執行註冊，或必須將語言模式設定為 **FullLanguageMode**，才能繼續。

### <a name="install-powershell-for-azure-stack"></a>安裝適用於 Azure Stack 的 PowerShell

請使用適用於 Azure Stack 的最新 PowerShell 向 Azure 註冊。

如果尚未安裝最新版本，請參閱[安裝適用於 Azure Stack 的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)。

### <a name="download-the-azure-stack-tools"></a>下載 Azure Stack 工具

Azure Stack 工具 GitHub 存放庫包含可支援 Azure Stack 功能 (包括註冊功能) 的 PowerShell 模組。 在註冊過程中，您必須匯入並使用 **RegisterWithAzure.psm1** PowerShell 模組 (位於 Azure Stack 工具存放庫中) 來向 Azure 註冊您的 Azure Stack 執行個體。

為確保使用的是最新版本，請刪除任何現有的 Azure Stack 工具版本，並[從 GitHub 下載最新版本](azure-stack-powershell-download.md)，再向 Azure 註冊。

### <a name="determine-your-registration-scenario"></a>判斷您的註冊案例

您的 Azure Stack 部署可能為「已連線」或「已中斷連線」。

 - **已連接**  
 「已連線」意謂著您已部署 Azure Stack，讓它可以連線至網際網路和 Azure。 您可以使用 Azure Active Directory (Azure AD) 或「Active Directory 同盟服務」(AD FS) 作為您的身分識別存放區。 使用已連線的部署時，有兩種計費模型可供您選擇：使用時付費或以容量為基礎。
    - [使用「使用時付費」計費模型向 Azure 註冊已連線的 Azure Stack](#register-connected-with-pay-as-you-go-billing)
    - [使用「容量」計費模型向 Azure 註冊已連線的 Azure Stack](#register-connected-with-capacity-billing)

 - **已中斷連線**  
 使用與 Azure 中斷連線的部署選項，您可以在沒有網際網路連線的情況下，部署和使用 Azure Stack。 不過，若使用已中斷連線的部署，您會受限於 AD FS 身分識別儲存和以容量為基礎的計費模型。
    - [使用「容量」計費模型來註冊已中斷連線的 Azure Stack](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>決定要使用的唯一註冊名稱 
當您向 Azure 註冊 Azure Stack 時，您必須提供唯一的註冊名稱。 讓 Azure Stack 訂用帳戶與 Azure 註冊產生關聯的簡單方法，是使用您的 Azure Stack **雲端識別碼**。 

> [!NOTE]
> 若 Azure Stack 註冊使用容量型計費模型，在這些年度訂用帳戶到期之後，須於重新註冊時變更唯一名稱，除非您[刪除過期的註冊](azure-stack-registration.md#change-the-subscription-you-use)並重新註冊 Azure。

若要確認 Azure Stack 部署的雲端識別碼，請以電腦上可以存取特殊權限端點的系統管理員身分開啟 PowerShell，並執行下列命令，然後記錄 **CloudID** 值： 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: get-azurestackstampinformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>使用隨用隨付計費來註冊已連線的環境

使用這些步驟，向 Azure 註冊「使用時付費」計費模型的 Azure Stack。

> [!Note]  
> 這些步驟全都必須從可存取具特殊權限端點 (PEP) 的電腦執行。 如需有關 PEP 的詳細資料，請參閱[使用 Azure Stack 中具有特殊權限的端點](azure-stack-privileged-endpoint.md)。

已連線的環境可以存取網際網路和 Azure。 在這類環境中，您必須向 Azure 註冊 Azure Stack 資源提供者，然後設定計費模型。

1. 若要向 Azure 註冊 Azure Stack 資源提供者，請以系統管理員身分啟動 PowerShell ISE，然後使用下列 PowerShell Cmdlet，其中將 **EnvironmentName** 參數設定為適當的 Azure 訂用帳戶類型 (請參閱下面的參數)。

2. 新增您用來註冊 Azure Stack 的 Azure 帳戶。 若要新增帳戶，請執行 **Add-AzureRmAccount** Cmdlet。 當系統提示您輸入 Azure 帳戶認證時，您可能需要根據帳戶的組態使用雙因素驗證。

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 參數 | 說明 |  
   |-----|-----|
   | EnvironmentName | Azure 雲端訂用帳戶環境名稱。 支援的環境名稱為 **AzureCloud**、**AzureUSGovernment**，或如果使用中國 Azure 訂用帳戶，則為 **AzureChinaCloud**。  |

3. 如果您有多個訂用帳戶，請執行下列命令以選取您要使用的訂用帳戶：  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 如需在您的 Azure 訂用帳戶中註冊 Azure Stack 資源提供者，請執行下列命令：

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 以系統管理員身分啟動 PowerShell ISE，並瀏覽至您[下載 Azure Stack 工具](#bkmk_tools)時所建 **AzureStack-Tools-master** 目錄中的 **Registration** 資料夾。 使用 PowerShell 匯入 **RegisterWithAzure.psm1** 模組：

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. 接下來，在相同的 PowerShell 工作階段中，確定您已登入正確的 Azure PowerShell 環境。 這是先前用來註冊 Azure Stack 資源提供者的 Azure 帳戶。 要執行的 Powershell：

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 參數 | 說明 |  
   |-----|-----|
   | EnvironmentName | Azure 雲端訂用帳戶環境名稱。 支援的環境名稱為 **AzureCloud**、**AzureUSGovernment**，或如果使用中國 Azure 訂用帳戶，則為 **AzureChinaCloud**。  |

7. 在相同的 PowerShell 工作階段中，執行 **Set-AzsRegistration** Cmdlet。 要執行的 PowerShell：  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   如需有關 Set-AzsRegistration Cmdlet 的詳細資訊，請參閱[註冊參考](#registration-reference)。

  此程序需要 10 到 15 分鐘的時間。 命令完成時，您會看到 **「您的環境現在已使用提供的參數註冊並啟動」** 的訊息。

## <a name="register-connected-with-capacity-billing"></a>使用容量計費來註冊已連線的環境

使用這些步驟，向 Azure 註冊「使用時付費」計費模型的 Azure Stack。

> [!Note]  
> 這些步驟全都必須從可存取具特殊權限端點 (PEP) 的電腦執行。 如需有關 PEP 的詳細資料，請參閱[使用 Azure Stack 中具有特殊權限的端點](azure-stack-privileged-endpoint.md)。

已連線的環境可以存取網際網路和 Azure。 在這類環境中，您必須向 Azure 註冊 Azure Stack 資源提供者，然後設定計費模型。

1. 若要向 Azure 註冊 Azure Stack 資源提供者，請以系統管理員身分啟動 PowerShell ISE，然後使用下列 PowerShell Cmdlet，其中將 **EnvironmentName** 參數設定為適當的 Azure 訂用帳戶類型 (請參閱下面的參數)。

2. 新增您用來註冊 Azure Stack 的 Azure 帳戶。 若要新增帳戶，請執行 **Add-AzureRmAccount** Cmdlet。 當系統提示您輸入 Azure 帳戶認證時，您可能需要根據帳戶的組態使用雙因素驗證。

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 參數 | 說明 |  
   |-----|-----|
   | EnvironmentName | Azure 雲端訂用帳戶環境名稱。 支援的環境名稱為 **AzureCloud**、**AzureUSGovernment**，或如果使用中國 Azure 訂用帳戶，則為 **AzureChinaCloud**。  |

3. 如果您有多個訂用帳戶，請執行下列命令以選取您要使用的訂用帳戶：  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 如需在您的 Azure 訂用帳戶中註冊 Azure Stack 資源提供者，請執行下列命令：

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 以系統管理員身分啟動 PowerShell ISE，並瀏覽至您[下載 Azure Stack 工具](#bkmk_tools)時所建 **AzureStack-Tools-master** 目錄中的 **Registration** 資料夾。 使用 PowerShell 匯入 **RegisterWithAzure.psm1** 模組：

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > 您可以將參數設為 false，以此方式使用 **Set-AzsRegistration** Cmdlet 的 UsageReportingEnabled 參數來停用使用量報告功能。 
   
  如需有關 Set-AzsRegistration Cmdlet 的詳細資訊，請參閱[註冊參考](#registration-reference)。

## <a name="register-disconnected-with-capacity-billing"></a>使用容量計費來註冊已中斷連線的環境

如果您要在已中斷連線的環境 (沒有網際網路連線能力) 中註冊 Azure Stack，就需要從 Azure Stack 環境中取得註冊權杖，然後在可以連線到 Azure 並[已安裝適用於 Azure Stack 之 PowerShell](#bkmk_powershell) 的電腦上使用該權杖。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>從 Azure Stack 環境中取得註冊權杖

1. 以系統管理員身分啟動 PowerShell ISE，並瀏覽至您[下載 Azure Stack 工具](#bkmk_tools)時所建 **AzureStack-Tools-master** 目錄中的 **Registration** 資料夾。 匯入 **RegisterWithAzure.psm1** 模組：  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 若要取得註冊權杖，請執行下列 PowerShell Cmdlet：  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   如需有關 Get-AzsRegistrationToken Cmdlet 的詳細資訊，請參閱[註冊參考](#registration-reference)。

   > [!Tip]  
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

  > [!Note]  
  > 儲存註冊資源名稱和註冊權杖，供日後參考。

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>從 Azure 註冊資源擷取啟用金鑰

接著，進行 Register-AzsEnvironment 時，您必須從 Azure 中建立的註冊資源中擷取啟用金鑰。

若要取得啟用金鑰，請執行下列 PowerShell Cmdlet：  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > 啟用金鑰會儲存在為 *$KeyOutputFilePath* 指定的檔案中。 您可以自行變更檔案路徑或檔名。

### <a name="create-an-activation-resource-in-azure-stack"></a>在 Azure Stack 中建立啟用資源

從 Get-AzsActivationKey 建立的啟用金鑰中取得檔案或文字後，返回 Azure Stack 環境。 接下來，您將使用該啟用金鑰在 Azure Stack 中建立啟用資源。 若要建立啟用資源，請執行下列 PowerShell Cmdlet：  

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

您可以使用 [Region management] \(區域管理\) 圖格來確認 Azure Stack 註冊是否已成功。 此圖格位於系統管理員入口網站中的預設儀表板上。 狀態可以是已註冊或未註冊。 如果是已註冊，則會一併顯示您用來註冊 Azure Stack 的 Azure 訂用帳戶 ID，以及註冊資源群組和名稱。

1. 登入 [Azure Stack 系統管理入口網站](https://adminportal.local.azurestack.external)。

2. 從儀表板中，選取 [Region management] \(區域管理\)。

3. 選取 [屬性] 。 此刀鋒視窗會顯示您環境的狀態和詳細資料。 狀態可以是 [已註冊] 或 [未註冊]。

    [ ![[Region management] \(區域管理\) 圖格](media/azure-stack-registration/admin1sm.png "[Region management] \(區域管理\) 圖格") ](media/azure-stack-registration/admin1.png#lightbox)

    若已註冊，屬性會包括：
    
    - **註冊訂用帳戶識別碼**：已註冊並且與 Azure Stack 相關聯的 Azure 訂用帳戶識別碼
    - **註冊資源群組：** 相關聯的訂用帳戶中包含 Azure Stack 資源的 Azure 資源群組。

4. 使用 Azure 入口網站檢視 Azure Stack 應用程式註冊。 使用與您用來註冊 Azure Stack 的訂用帳戶相關聯的帳戶，登入 Azure 入口網站。 切換至與 Azure Stack 相關聯的租用戶。
5. 瀏覽至 [Azure Active Directory > 應用程式註冊 > 檢視所有應用程式]。

    ![應用程式註冊](media/azure-stack-registration/app-registrations.png)

    Azure Stack 應用程式註冊前面會加上 **Azure Stack**。

或者，您也可以使用 Marketplace 管理功能來確認註冊是否已成功。 如果您在 [Marketplace Management] \(Marketplace 管理\) 刀鋒視窗中看到 Marketplace 項目清單，即表示您的註冊已成功。 不過，在已中斷連線的環境中，您則無法在 Marketplace 管理中看到 Marketplace 項目。 不過，您可以使用離線工具來確認註冊情況。

> [!NOTE]
> 註冊完成後，就不會再出現未註冊的作用中警告。 在已中斷連線的情況下，即使您已成功註冊，在 Marketplace 管理中，仍然會看到要求您註冊並啟用 Azure Stack 的訊息。

## <a name="renew-or-change-registration"></a>續訂或變更註冊

### <a name="renew-or-change-registration-in-connected-environments"></a>在已連線的環境中更新或變更註冊

在下列情況下，您需要更新或續訂您的註冊：

- 在您續訂以容量為基礎的年度訂用帳戶之後。
- 當您變更計費模型時。
- 當您調整以容量為基礎的計費變更 (新增/移除節點) 時。

#### <a name="change-the-subscription-you-use"></a>變更您使用的訂用帳戶

如果要變更您所使用的訂用帳戶，就必須先執行 **Remove-AzsRegistration** Cmdlet，然後確保您已登入正確的 Azure PowerShell 內容，最後使用任何已變更的參數來執行 **Set-AzsRegistration** (包括\<計費模型\>)：

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>變更計費模型或提供功能的方式

如果您想要為安裝變更計費模型或提供功能的方式，您可以呼叫註冊函式來設定新的值。 您不需要先移除目前的註冊：

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>在已中斷連線的環境中更新或變更註冊

在下列情況下，您需要更新或續訂您的註冊：

- 在您續訂以容量為基礎的年度訂用帳戶之後。
- 當您變更計費模型時。
- 當您調整以容量為基礎的計費變更 (新增/移除節點) 時。

#### <a name="remove-the-activation-resource-from-azure-stack"></a>從 Azure Stack 中移除啟用資源

您必須先從 Azure Stack 中移除啟用資源，然後再移除 Azure 中的註冊資源。  

若要移除 Azure Stack 中的啟用資源，請在您的 Azure Stack 環境中執行下列 PowerShell Cmdlet：  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

接下來，若要移除 Azure 中的註冊資源，請確定您的電腦已與 Azure 連線，然後登入正確的 Azure PowerShell 環境，並執行適當的 PowerShell Cmdlet，如下所述。

您可以使用用來建立資源的註冊權杖：  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

或者，您可以使用註冊名稱：

  ```Powershell
  $registrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>使用未連線的步驟重新註冊

您現在已在未連線的狀況下完成取消註冊，而您必須在未連線的狀況下，執行重複的步驟來註冊 Azure Stack 環境。

### <a name="disable-or-enable-usage-reporting"></a>停用或啟用使用量報告功能

針對使用容量計費模型的 Azure Stack 環境，請使用 **Set-AzsRegistration** 或 **Get-AzsRegistrationToken** Cmdlet 搭配 **UsageReportingEnabled** 參數來關閉使用量報告功能。 Azure Stack 預設會回報使用計量。 操作員若使用容量或支援已中斷連線的環境，將必須關閉使用量報告功能。

#### <a name="with-a-connected-azure-stack"></a>使用已連線的 Azure Stack

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>使用已中斷連線的 Azure Stack

1. 若要變更註冊權杖，請執行下列 PowerShell Cmdlet：  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > 註冊權杖會儲存在針對 $FilePathForRegistrationToken 指定的檔案。 您可以自行變更檔案路徑或檔名。

2. 儲存此註冊權杖，以在連線 Azure 的電腦上使用。 您可以從 $FilePathForRegistrationToken 複製檔案或文字。

## <a name="move-a-registration-resource"></a>移動註冊資源
對於所有環境均**支援**在相同的訂用帳戶下的資源群組之間移動註冊資源。 不過，如果兩個訂用帳戶解析為相同的合作夥伴識別碼，則僅針對 CSP 支援在訂用帳戶之間移動註冊資源。 如需將資源移到新資源群組的詳細資訊，請參閱[將資源移到新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。

## <a name="registration-reference"></a>註冊參考

### <a name="set-azsregistration"></a>Set-AzsRegistration

您可以使用 Set-AzsRegistration 向 Azure 註冊 Azure Stack，以及啟用或停用在市集中提供項目的功能和使用量報告功能。

若要執行此 Cmdlet，您需要：
- 一個任何類型的全域 Azure 訂用帳戶。
- 您也必須使用身為該訂用帳戶之擁有者或參與者的帳戶來登入 Azure PowerShell。

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| 參數 | 類型 | 說明 |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | 用來[存取具有特殊權限的端點](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的認證。 使用者名稱的格式為 **AzureStackDomain\CloudAdmin**。 |
| PrivilegedEndpoint | 字串 | 一個預先設定的遠端 PowerShell 主控台，可為您提供記錄收集和其他後續部署工作之類的功能。 若要深入了解，請參閱[使用具有特殊權限的端點](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。 |
| AzureContext | PSObject |  |
| resourceGroupName | 字串 |  |
| ResourceGroupLocation | 字串 |  |
| BillingModel | 字串 | 您的訂用帳戶所使用的計費模型。 此參數允許的值：Capacity、PayAsYouUse 和 Development。 |
| MarketplaceSyndicationEnabled | True/False | 判斷是否可在入口網站中使用 Marketplace 管理功能。 如果在有網際網路連線的情況下註冊，則設定為 True。 如果在已中斷連線的環境中註冊，則設定為 False。 若為中斷連線的註冊，[離線摘要整合工具](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)可用於下載 Marketplace 項目。 |
| UsageReportingEnabled | True/False | Azure Stack 預設會回報使用計量。 操作員若使用容量或支援已中斷連線的環境，將必須關閉使用量報告功能。 此參數允許的值：True、False。 |
| AgreementNumber | 字串 |  |
| RegistrationName | 字串 | 如果您要在多個 Azure Stack 執行個體上，使用相同的 Azure 訂用帳戶識別碼執行註冊指令碼，請為註冊設定唯一的名稱。 此參數的預設值是 **AzureStackRegistration**。 不過，如果您在多個 Azure Stack 執行個體上使用相同的名稱，指令碼將會失敗。 |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken 會從輸入參數產生註冊權杖。

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| 參數 | 類型 | 說明 |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | 用來[存取具有特殊權限的端點](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的認證。 使用者名稱的格式為 **AzureStackDomain\CloudAdmin**。 |
| PrivilegedEndpoint | 字串 |  一個預先設定的遠端 PowerShell 主控台，可為您提供記錄收集和其他後續部署工作之類的功能。 若要深入了解，請參閱[使用具有特殊權限的端點](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。 |
| AzureContext | PSObject |  |
| resourceGroupName | 字串 |  |
| ResourceGroupLocation | 字串 |  |
| BillingModel | 字串 | 您的訂用帳戶所使用的計費模型。 此參數允許的值：Capacity、PayAsYouUse 和 Development。 |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | Azure Stack 預設會回報使用計量。 操作員若使用容量或支援已中斷連線的環境，將必須關閉使用量報告功能。 此參數允許的值：True、False。 |
| AgreementNumber | 字串 |  |


## <a name="next-steps"></a>後續步驟

[從 Azure 下載市集項目](azure-stack-download-azure-marketplace-item.md)。
