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
ms.date: 03/26/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 1dc3d9a96b9b27927cc8cc66b5e80987fba4f8ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack
向 Azure 註冊 [Azure Stack](azure-stack-poc.md)，即可從 Azure 下載市集項目，並設定向 Microsoft 回報商務資料的功能。 註冊 Azure Stack 之後，使用方式會回報給 Azure 商務報告，您可以在註冊所用的訂用帳戶中查看報告。 

> [!IMPORTANT]
> 如果您選擇使用時付費計費模型，則必須註冊。 否則，您將違反 Azure Stack 部署的授權條款，因為未報告使用量。

## <a name="prerequisites"></a>先決條件
使用 Azure 註冊 Azure Stack 之前，您必須：

- Azure 訂用帳戶的訂用帳戶 ID。 若要取得 ID，請登入 Azure 並按一下 [更多服務]  >  [訂用帳戶]，然後按一下您要使用的訂用帳戶，便可在 [基本資訊] 下找到 [訂用帳戶 ID]。 

  > [!NOTE]
  > 目前不支援中國、德國，和美國政府雲端訂用帳戶。 

- 訂用帳戶擁有者的帳戶使用者名稱和密碼 (支援 MSA/2FA 帳戶)
- 註冊 Azure Stack 資源提供者 (請參閱下面的「註冊 Azure Stack 資源提供者」一節以取得詳細資料)。

如果您沒有符合這些需求的 Azure 訂用帳戶，則可以[在這裡建立免費的 Azure 帳戶](https://azure.microsoft.com/free/?b=17.06)。 註冊 Azure Stack 不會對您的 Azure 訂用帳戶收取任何費用。

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
若要向 Azure 註冊 Azure Stack 資源提供者，請以系統管理員身分啟動 Powershell ISE 並使用下列 PowerShell 命令。 這些命令會：
- 提示您以所要使用之 Azure 訂用帳戶的擁有者身分登入，並且將 **EnvironmentName** 參數設定為 [AzureCloud]。
- 註冊 Azure 資源提供者 **Microsoft.AzureStack**。

1. 新增您用來註冊 Azure Stack 的 Azure 帳戶。 若要新增帳戶，請執行 **Add-AzureRmAccount** Cmdlet。 當系統提示您輸入 Azure 全域系統管理員帳戶認證時，您可能需要根據帳戶的組態使用雙因素驗證。

   ```Powershell
      Add-AzureRmAccount -EnvironmentName AzureCloud
   ```

2. 如果您有多個訂用帳戶，請執行下列命令以選取您要使用的訂用帳戶：  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. 如需在您的 Azure 訂用帳戶中註冊 Azure Stack 資源提供者，請執行下列命令：

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>向 Azure 註冊「使用時付費」計費模型的 Azure Stack
使用這些步驟，向 Azure 註冊「使用時付費」計費模型的 Azure Stack。

以系統管理員身分啟動 PowerShell ISE，並瀏覽至您[下載 Azure Stack 工具](#bkmk_tools)時所建 **AzureStack-Tools-master** 目錄中的 **Registration** 資料夾。 使用 PowerShell 匯入 **RegisterWithAzure.psm1** 模組： 

要執行的 PowerShell：

```powershell
Import-Module .\RegisterWithAzure.psm1
```
接著，在相同的 PowerShell 工作階段中，執行 **Set-AzsRegistration** Cmdlet。 當系統提示您輸入認證時，請指定 Azure 訂用帳戶的擁有者。  

要執行的 PowerShell：

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials>  -Message "Enter the credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|參數|說明|
|-----|-----|
|CloudAdminCredential|PowerShell 物件，其中包含 存取特殊權限端點所用的認證資訊 (使用者名稱和密碼)。|
|PrivilegedEndpoint|一個預先設定的遠端 PowerShell 主控台，可為您提供記錄收集和其他後續部署工作之類的功能。 若要深入了解，請參閱[使用具有特殊權限的端點](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint)一文。|
|BillingModel|您的訂用帳戶所使用的計費模型。 此參數允許的值：Capacity、PayAsYouUse 和 Development。|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>向 Azure 註冊使用容量計費模型的 Azure Stack
遵循用於註冊「使用時付費」計費模型的相同指示，但新增購買容量時所依據的合約編號並將 `BillingModel` 參數變更為 [Capacity]。 所有其他參數皆不變。

要執行的 PowerShell：
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials>  -Message "Enter the credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>在已中斷連線的環境中註冊 Azure Stack 
本節中的資訊適用於 Azure Stack 1712 起的更新版本 (180106.1)，且不支援舊的版本。

如果您要在已中斷連線的環境 (沒有網際網路連線能力) 中註冊 Azure Stack，就需要從 Azure Stack 環境中取得註冊權杖，然後在可以連線到 Azure 並[已安裝適用於 Azure Stack 之 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install) 的電腦上使用該權杖。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>從 Azure Stack 環境中取得註冊權杖
  1. 若要取得註冊權杖，請執行下列 PowerShell 命令：  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > 註冊權杖會儲存在針對 $env:SystemDrive\RegistrationToken.txt 指定的檔案。

  2. 儲存此註冊權杖，以在連線 Azure 的電腦上使用。


### <a name="connect-to-azure-and-register"></a>連線至 Azure 並註冊
以系統管理員身分啟動 PowerShell ISE，並瀏覽至您[下載 Azure Stack 工具](#bkmk_tools)時所建 **AzureStack-Tools-master** 目錄中的 **Registration** 資料夾。 匯入 **RegisterWithAzure.psm1** 模組： 

要執行的 PowerShell：
```powershell
Import-Module .\RegisterWithAzure.psm1
```
接著，在相同的 PowerShell 工作階段中，指定要向 Azure 註冊的註冊權杖：

```Powershell  
$registrationToken = "<Your Registration Token>"
Register-AzsEnvironment -RegistrationToken $registrationToken  
```
(選擇性) 您可以使用 Get-content cmdlet 來指向包含您註冊權杖的檔案：

 ```Powershell  
 $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
 Register-AzsEnvironment -RegistrationToken $registrationToken  
 ```
> [!NOTE]  
> 儲存註冊資源名稱或註冊權杖，供日後參考。

## <a name="verify-azure-stack-registration"></a>驗證 Azure Stack 註冊
使用下列步驟確認 Azure Stack 已成功向 Azure 註冊。
1. 登入 Azure Stack [系統管理員入口網站](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal)：https&#58;//adminportal.*&lt;region>.&lt;fqdn>*。
2. 按一下 [更多服務]  >  [市集管理]  >  [從 Azure 新增]。

如果您看到 Azure 提供的項目清單 (例如 WordPress)，則表示啟用已成功。

> [!NOTE]
> 註冊完成後，就不會再出現未註冊的作用中警告。

## <a name="renew-or-change-registration"></a>續訂或變更註冊
在下列情況下，您需要更新或續訂您的註冊：
- 在您續訂以容量為基礎的年度訂用帳戶之後。
- 當您變更計費模型時。
- 當您調整以容量為基礎的計費變更 (新增/移除節點) 時。

### <a name="change-the-subscription-you-use"></a>變更您使用的訂用帳戶
如果要變更您所使用的訂用帳戶，就必須先執行 **Remove-AzsRegistration** Cmdlet，然後確保您已登入正確的 Azure PowerShell 內容，最後使用任何已變更的參數來執行 **Set-AzsRegistration**：

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>變更計費模式或摘要整合功能
如果您要變更安裝的計費模式或摘要整合功能，可以呼叫註冊函式來設定新的值。 您不需要先移除目前的註冊： 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```
## <a name="next-steps"></a>後續步驟

[從 Azure 下載市集項目](azure-stack-download-azure-marketplace-item.md)。