---
title: 管理 Azure Stack 的服務主體 | Microsoft Docs
description: 說明如何管理可與 Azure Resource Manager 中角色型存取控制搭配使用來管理資源存取權的新服務主體。
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 0f5a4dc76830740d69547a01ce40b5e10cf4a74b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499403"
---
# <a name="provide-applications-access-to-azure-stack"></a>為 Azure Stack 提供應用程式存取

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

當應用程式需要存取權透過 Azure Resource Manager 在 Azure Stack 中部署或設定資源時，您會建立服務主體，這是您的應用程式的認證。 然後，您可以只對該服務主體委派必要權限。  

舉例來說，您可能有使用 Azure Resource Manager 來清查 Azure 資源的組態管理工具。 在此案例中，您可以建立服務主體、為該服務主體授與讀取者角色，以及將組態管理工具限制在唯讀存取權。 

以您自己的認證執行應用程式最好是使用服務主體，因為：

 - 您可以對服務主體指派不同於您自己帳戶權限的權限。 一般而言，這些權限只會限制為 App 必須執行的確切權限。
 - 如果您的職責變更，就不需要變更 App 的認證。
 - 您可以使用憑證在執行自動指令碼時自動進行驗證。  

## <a name="getting-started"></a>開始使用

根據您部署 Azure Stack 的方式，您會從建立服務主體開始。 本文件說明如何為下列項目建立服務主體：

- Azure Active Directory (Azure AD)。 Azure AD 是一個多租用戶雲端式目錄和身分識別管理服務。 您可以將 Azure AD 與已連線的 Azure Stack 搭配使用。
- Active Directory 同盟服務 (AD FS)。 AD FS 提供簡化、安全的身分識別同盟和 Web 單一登入 (SSO) 功能。 您可以將 AD FS 與已連線和中斷連線的 Azure Stack 執行個體搭配使用。

一旦您已建立服務主體，會使用 AD FS 與 Azure Active Directory 的一組共通步驟來對角色委派權限。

## <a name="manage-service-principal-for-azure-ad"></a>管理 Azure AD 的服務主體

如果您已以 Azure Active Directory (Azure AD) 作為身分識別管理服務來部署 Azure Stack，便可以像對 Azure 那樣建立服務主體。 本節說明如何透過入口網站執行這些步驟。 開始之前，請確認您有[必要的 Azure AD 權限](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。

### <a name="create-service-principal"></a>建立服務主體

在本節中，您會在 Azure AD 中建立一個應用程式 (服務主體) 來代表您的應用程式。

1. 透過 [Azure 入口網站](https://portal.azure.com)登入您的 Azure 帳戶。
2. 選取 [Azure Active Directory] > [應用程式註冊] > [新應用程式註冊]
3. 提供應用程式的名稱和 URL。 針對您想要建立的應用程式類型，選取 [Web 應用程式/API] 或 [原生]。 設定值之後，選取 [建立]。

您已建立應用程式的服務主體。

### <a name="get-credentials"></a>取得認證

以程式設計方式登入時，使用您應用程式的識別碼，而針對 Web 應用程式 / API，則使用驗證金鑰。 若要取得這些值，請使用下列步驟︰

1. 在 Active Directory 中，從 [應用程式註冊]選取您的應用程式。

2. 複製 [應用程式識別碼] 並儲存在您的應用程式碼中。 範例應用程式區段中的應用程式會參考此值作為用戶端識別碼。

     ![用戶端識別碼](./media/azure-stack-create-service-principal/image12.png)
3. 若要產生 Web 應用程式 / API 的驗證金鑰，請選取 [設定] > [金鑰]。 

4. 提供金鑰的描述和金鑰的持續時間。 完成時，選取 [儲存]。

儲存金鑰之後會顯示金鑰的值。 由於之後就無法擷取此金鑰值，因此請將此值複製到記事本或其他暫存位置。 您提供金鑰值和應用程式識別碼，以應用程式身分簽署。 將金鑰值儲存在應用程式可擷取的地方。

![儲存的金鑰](./media/azure-stack-create-service-principal/image15.png)

完成之後，您便可以為應用程式指派角色。

## <a name="manage-service-principal-for-ad-fs"></a>管理 AD FS 的服務主體

如果您已以「Active Directory 同盟服務」(AD FS) 作為身分識別管理服務來部署 Azure Stack，請使用 PowerShell 來建立服務主體、指派用於存取的角色，然後使用該身分識別進行登入。

您可以使用兩種方法其中之一來搭配 AD FS 建立您的服務主體。 您可以：
 - [使用憑證來建立服務主體](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [使用用戶端祕密來建立服務主體](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

用於管理 AD FS 服務主體的工作。

| 類型 | 動作 |
| --- | --- |
| AD FS 憑證 | [建立](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| AD FS 憑證 | [更新](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| AD FS 憑證 | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| AD FS 用戶端祕密 | [建立](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| AD FS 用戶端祕密 | [更新](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| AD FS 用戶端祕密 | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>使用憑證來建立服務主體

在使用 AD FS 進行身分識別的情況下，若要建立服務主體，您可以使用憑證。

#### <a name="certificate"></a>憑證

需要憑證。

**憑證需求**

 - 密碼編譯服務提供者 (CSP) 必須是舊版金鑰提供者。
 - 憑證格式必須是 PFX 檔案，因為需要公用與私密金鑰。 Windows 伺服器會使用包含公用金鑰檔案 (SSL 憑證檔案) 及相關私密金鑰檔案的 .pfx 檔案。
 - 針對生產環境，憑證必須由內部憑證授權單位或公用憑證授權單位發出。 如果使用公用憑證授權單位，您必須將授權單位作為 Microsoft 受信任的根授權單位方案的一部分，包含在基礎作業系統映像中。 您可以在下列文章中找到完整清單：[Microsoft 受信任的根憑證計劃：參與者](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)。
 - 您的 Azure Stack 基礎結構必須能透過網路來存取憑證中所發佈憑證授權單位的憑證撤銷清單 (CRL) 位置。 這個 CRL 必須是 HTTP 端點。

#### <a name="parameters"></a>參數

需要下列資訊，作為自動化參數的輸入：

|參數|說明|範例|
|---------|---------|---------|
|Name|SPN 的帳戶名稱|MyAPP|
|ClientCertificates|憑證物件的陣列|X509 憑證|
|ClientRedirectUris<br>(選用)|應用程式重新導向 URI|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>使用 PowerShell 來建立服務主體

1. 開啟已提升權限的 Windows PowerShell 工作階段，然後執行下列 Cmdlet：

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > 為了進行驗證，可以使用以下範例來建立一個自我簽署憑證：

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. 自動化完成之後，它會顯示使用 SPN 的必要詳細資料。 建議您儲存輸出以供稍後使用。

   例如︰

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>更新 AD FS 服務主體的憑證

如果您已搭配 AD FS 來部署 Azure Stack，便可以使用 PowerShell 來更新服務主體的祕密。

指令碼是從 ERCS 虛擬機器上的特殊權限端點執行。

#### <a name="parameters"></a>參數

需要下列資訊，作為自動化參數的輸入：

|參數|說明|範例|
|---------|---------|---------|
|Name|SPN 的帳戶名稱|MyAPP|
|ApplicationIdentifier|唯一識別碼|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|憑證物件的陣列|X509 憑證|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>更新 AD FS 服務主體的範例

此範例會建立一個自我簽署憑證。 當您在生產環境部署中執行這些 Cmdlet 時，請使用 [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) 來擷取您想要使用之憑證的憑證物件。

1. 開啟已提升權限的 Windows PowerShell 工作階段，然後執行下列 Cmdlet：

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. 自動化完成之後，它會顯示 SPN 驗證所需的已更新指紋值。

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>使用用戶端祕密來建立服務主體

在使用 AD FS 進行身分識別的情況下，若要建立服務主體，您可以使用憑證。 您將使用具特殊權限的端點來執行 Cmdlet。

執行這些指令碼時，會從 ERCS 虛擬機器上的具特殊權限端點執行。 如需有關具特殊權限端點的詳細資訊，請參閱[使用 Azure Stack 中具有特殊權限的端點](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)。

#### <a name="parameters"></a>參數

需要下列資訊，作為自動化參數的輸入：

| 參數 | 說明 | 範例 |
|----------------------|--------------------------|---------|
| Name | SPN 的帳戶名稱 | MyAPP |
| GenerateClientSecret | 建立祕密 |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>使用 ERCS PrivilegedEndpoint 來建立服務主體

1. 開啟已提升權限的 Windows PowerShell 工作階段，然後執行下列 Cmdlet：

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. 在 Cmdlet 執行之後，殼層會顯示使用 SPN 所需的詳細資料。 請務必儲存用戶端祕密。

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>更新 AD FS 服務主體的用戶端祕密

PowerShell Cmdlet 會自動產生新的用戶端祕密。

指令碼是從 ERCS 虛擬機器上的特殊權限端點執行。

##### <a name="parameters"></a>參數

需要下列資訊，作為自動化參數的輸入：

| 參數 | 說明 | 範例 |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | 唯一識別碼。 | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | 在舊祕密仍然有效的情況下，搭配 2880 分鐘的變換期間來變更用戶端祕密。 |  |
| ResetClientSecret | 立即變更用戶端祕密 |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>更新 AD FS 用戶端祕密的範例

此範例使用可立即變更用戶端祕密的 **ResetClientSecret** 參數。

1. 開啟已提升權限的 Windows PowerShell 工作階段，然後執行下列 Cmdlet：

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. 自動化完成之後，它會顯示 SPN 驗證所需的新產生祕密。 請務必儲存新的用戶端祕密。

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>移除 AD FS 的服務主體

如果您已搭配 AD FS 來部署 Azure Stack，便可以使用 PowerShell 來刪除服務主體。

指令碼是從 ERCS 虛擬機器上的特殊權限端點執行。

#### <a name="parameters"></a>參數

需要下列資訊，作為自動化參數的輸入：

|參數|說明|範例|
|---------|---------|---------|
| 參數 | 說明 | 範例 |
| ApplicationIdentifier | 唯一識別碼 | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> 若要檢視現有服務主體及其「應用程式識別碼」的清單，可以使用 get-graphapplication 命令。

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>移除 AD FS 服務主體的範例

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>指派角色

若要存取您的訂用帳戶中的資源，您必須將應用程式指派給角色。 決定哪個角色代表應用程式的正確權限。 若要深入了解可用的角色，請參閱 [RBAC：內建角色](../role-based-access-control/built-in-roles.md)。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。 較低的範圍層級會繼承較高層級的權限。 例如，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源。

1. 在 Azure Stack 入口網站中，瀏覽至您想要指派應用程式的範圍層級。 例如，若要在訂用帳戶範圍指派角色，請選取 [訂用帳戶]。 您可以改為選取資源群組或資源。

2. 選取特定訂用帳戶作為指派應用程式的對象 (資源群組或資源)。

     ![選取要指派的訂用帳戶](./media/azure-stack-create-service-principal/image16.png)

3. 選取 [存取控制 (IAM)]。

     ![選取存取](./media/azure-stack-create-service-principal/image17.png)

4. 選取 [新增角色指派]。

5. 選取您想要將應用程式指派給哪個角色。

6. 搜尋並選取您的應用程式。

7. 選取 [確定] 以完成角色指派。 您在使用者清單中看到應用程式已指派給該範圍的角色。

既然您已建立服務主體並指派角色，您可以開始在應用程式內使用它來存取 Azure Stack 資源。  

## <a name="next-steps"></a>後續步驟

[為 AD FS 新增使用者](azure-stack-add-users-adfs.md)  
[管理使用者權限](azure-stack-manage-permissions.md)  
[Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory)  
[Active Directory 同盟服務](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services) \(英文\)
