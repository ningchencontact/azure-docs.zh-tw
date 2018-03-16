---
title: "註冊 Azure Stack | Microsoft Docs"
description: "使用您的 Azure 訂用帳戶註冊 Azure Stack。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/27/2018
ms.author: jeffgilb
ms.openlocfilehash: 8d520e1917d63b56a2adf3823ddbb741721fe751
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>使用您的 Azure 訂用帳戶註冊 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以向 Azure 註冊 [Azure Stack](azure-stack-poc.md)，以便從 Azure 下載市集項目，以及設定向 Microsoft 回報商務資料的功能。

> [!NOTE]
>我們建議您註冊，因為它可讓您測試重要的 Azure Stack 功能，例如市集摘要整合和使用方式報告。 註冊 Azure Stack 之後，使用方式會回報給 Azure 商務。 您可以在註冊時所使用的訂用帳戶下看到這項資訊。 Azure Stack 開發套件使用者將不需針對回報的任何使用方式支付費用。


## <a name="get-azure-subscription"></a>取得 Azure 訂用帳戶

使用 Azure 註冊 Azure Stack 之前，您必須：

- Azure 訂用帳戶的訂用帳戶 ID。 若要取得 ID，請登入 Azure 並按一下 [更多服務]  >  [訂用帳戶]，然後按一下您要使用的訂用帳戶，便可以在 [基本資訊] 下找到 [訂用帳戶 ID]。 目前不支援中國、德國，和美國政府雲端訂用帳戶。
- 訂用帳戶擁有者的帳戶使用者名稱和密碼 (支援 MSA/2FA 帳戶)。
- 從 Azure Stack 1712 更新版本 (180106.1) 開始不再是必要項目：適用於 Azure 訂用帳戶的 Azure Active Directory。 您可以將游標暫留在 Azure 入口網站右上角的顯示圖片，即可在 Azure 中找到此目錄。

如果您沒有符合這些需求的 Azure 訂用帳戶，則可以[在這裡建立免費的 Azure 帳戶](https://azure.microsoft.com/en-us/free/?b=17.06)。 註冊 Azure Stack 不會對您的 Azure 訂用帳戶收取任何費用。

## <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack  
> [!NOTE]
> 這些步驟全都必須從可存取具有特殊權限之端點的機器執行。 就 Azure Stack 開發套件而言，此機器將是主機電腦。 如果您要使用整合式系統，請連絡您的 Azure Stack 操作員。
>

1. 以系統管理員身分開啟 PowerShell 主控台，然後[安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。  

2. 新增您用來註冊 Azure Stack 的 Azure 帳戶。 若要新增帳戶，請執行 `Add-AzureRmAccount` Cmdlet，並將 EnvironmentName 參數設定為 **AzureCloud**。 當系統提示您輸入 Azure 帳戶認證時，您可能需要根據帳戶的組態使用雙因素驗證。

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. 如果您有多個訂用帳戶，請執行下列命令以選取您要使用的訂用帳戶：  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 如需在您的 Azure 訂用帳戶中註冊 Azure Stack 資源提供者，請執行下列命令：

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 刪除任何對應至註冊的現有 PowerShell 模組版本，然後[從 GitHub 下載其最新版本](azure-stack-powershell-download.md)。  

6. 從先前的步驟中建立的 "AzureStack-Tools-master" 目錄巡覽至「註冊」資料夾，然後匯入 ".\RegisterWithAzure.psm1" 模組：  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. 在相同的 PowerShell 工作階段中，執行下列指令碼：在出現認證的提示時，將 `azurestack\cloudadmin` 指定為使用者，密碼就與您在部署期間用於本機管理員的密碼相同。  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | 參數 | 說明 |  
   |--------|-------------|
   | CloudAdminCredential | 用來[存取具有特殊權限的端點](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的雲端網域認證。 使用者名稱的格式為 **\<Azure Stack 網域\>\cloudadmin**。 針對開發套件，使用者名稱會設為 **azurestack\cloudadmin**。 如果您要使用整合式系統，請向您的 Azure Stack 操作員索取此值。|  
   | PrivilegedEndpoint | 一個預先設定的遠端 PowerShell 主控台，可為您提供記錄收集和其他後續部署工作之類的功能。 針對開發套件，具有特殊權限的端點會裝載在 "AzS-ERCS01" 虛擬機器上。 如果您要使用整合式系統，請向您的 Azure Stack 操作員索取此值。 若要深入了解，請參閱[使用具有特殊權限的端點](azure-stack-privileged-endpoint.md)一文。|  
   | BillingModel | 您的訂用帳戶所使用的計費模型。 此參數允許的值為 - **Capacity**、**PayAsYouUse** 和 **Development**。 針對開發套件，此值會設為 **Development**。 如果您要使用整合式系統，請向您的 Azure Stack 操作員索取此值。 |

8. 指令碼完成時，您會看到「正在啟動 Azure Stack (此步驟可能需要 10 分鐘才能完成)」的訊息。




## <a name="verify-the-registration"></a>確認註冊  

1. 登入系統管理員入口網站 (https://adminportal.local.azurestack.external)。

2. 按一下 [更多服務]  >  [市集管理]  >  [從 Azure 新增]。

3. 如果您看到 Azure 提供的項目清單 (例如 WordPress)，則表示啟用已成功。

> [!NOTE]
> 註冊完成後，就不會再出現未註冊的作用中警告。


## <a name="modify-the-registration"></a>修改註冊

### <a name="change-the-subscription-you-use"></a>變更您使用的訂用帳戶
如果要變更您所使用的訂用帳戶，就必須先執行 Remove-AzsRegistration，確保您登入正確的 Azure PowerShell 內容，然後使用任何已變更的參數來執行 Set-AzsRegistration。

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>變更計費模式或摘要整合功能
如果您要變更安裝的計費模式或摘要整合功能，可以呼叫註冊函式來設定新的值。 您不需要先移除目前的註冊。  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>已中斷連線的註冊
本節中的資訊適用於 Azure Stack 1712 起的更新版本 (180106.1)，且不支援舊的版本。

如果您要在中斷連線的環境中註冊 Azure Stack，就需要從 Azure Stack 環境中取得註冊權杖，然後在可以連線到 Azure 進行註冊的電腦上使用該權杖。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>從 Azure Stack 環境中取得註冊權杖
  1. 若要取得註冊權杖，請執行下列項目：  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 註冊權杖會儲存在針對 $env:SystemDrive\RegistrationToken.txt 指定的檔案。

  2. 儲存此註冊權杖，以在連線 Azure 的電腦上使用。


### <a name="connect-to-azure-and-register"></a>連線至 Azure 並註冊
在可連線到 Azure 的電腦上執行此程序的步驟。

  1. [從 GitHub 下載對應至註冊的最新 PowerShell 模組](azure-stack-powershell-download.md)。  

  2. 從先前的步驟中建立的 "AzureStack-Tools-master" 目錄巡覽至「註冊」資料夾，然後匯入 ".\RegisterWithAzure.psm1" 模組：  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. 將 [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) 複製到資料夾，例如 C:\Temp。

  4. 以系統管理員身分啟動 PowerShell ISE，然後匯入 RegisterWithAzure 模組。  

  5. 請確定您登入的是要用來註冊 Azure Stack 環境的正確 Azure PowerShell 內容：  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. 指定要向 Azure 註冊的註冊權杖：

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    (選擇性) 您可以使用 Get-content cmdlet 來指向包含您註冊權杖的檔案：

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> 儲存註冊資源名稱或註冊權杖，供日後參考。

### <a name="remove-a-registered-resource"></a>將已註冊的資源移除
如果您要移除註冊資源，就必須使用 UnRegister-AzsEnvironment，並傳入註冊資源名稱，或您用於 Register-AzsEnvironment 的註冊權杖。
- **註冊資源名稱：**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **註冊權杖：**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>後續步驟
[連接至 Azure Stack](azure-stack-connect-azure-stack.md)
