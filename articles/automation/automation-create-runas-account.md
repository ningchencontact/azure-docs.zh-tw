---
title: 建立 Azure 自動化執行身分帳戶
description: 本文說明如何更新您的自動化帳戶，並使用 PowerShell 或從入口網站建立執行身分帳戶。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: e7a339ae443bc61e1d2364afd8400fc436593b9f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>使用執行身分帳戶更新您的自動化帳戶驗證 
您可以從 Azure 入口網站或使用 PowerShell 來更新現有的自動化帳戶，前提是：

* 您已建立一個自動化帳戶，但拒絕建立執行身分帳戶。
* 您已使用自動化帳戶來管理 Resource Manager 資源，而且您想要更新此帳戶以包含可供 Runbook 驗證的執行身分帳戶。
* 您已使用自動化帳戶來管理傳統資源，而且您想要更新此帳戶以使用傳統執行身分，而不是建立新的帳戶並將 Runbook 和資產移轉至該帳戶。   

此程序會在自動化帳戶中建立下列項目。

**若為執行身分帳戶︰**

* 建立可使用自我簽署憑證或企業憑證的公開金鑰進行匯出的 Azure AD 應用程式、建立此應用程式在 Azure AD 中的服務主體帳戶，並在目前的訂用帳戶中為此帳戶指派參與者角色。 您可以將此設定變更為擁有者或任何其他角色。 如需詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
* 在指定的自動化帳戶中，建立名為 AzureRunAsCertificate 的自動化憑證資產。 憑證資產會保存 Azure AD 應用程式所使用的憑證私密金鑰。
* 在指定的自動化帳戶中，建立名為 AzureRunAsConnection 的自動化連線資產。 連線資產會保存 applicationId、tenantId、subscriptionId 和憑證指紋。

**若為傳統執行身分帳戶：**

* 在指定的自動化帳戶中，建立名為 AzureClassicRunAsCertificate 的自動化憑證資產。 憑證資產會保存管理憑證所使用的憑證私密金鑰。
* 在指定的自動化帳戶中，建立名為 AzureClassicRunAsConnection 的自動化連線資產。 連線資產會保存訂用帳戶名稱、subscriptionId 和憑證資產名稱。

## <a name="prerequisites"></a>先決條件
如果您選擇[使用 PowerShell 建立執行身分帳戶](#create-run-as-account-using-powershell)，此程序需要：

* 具有 Azure Resource Manager 模組 3.4.1 和更新版本的 Windows 10 與 Windows Server 2016。 PowerShell 指令碼不支援舊版 Windows。
* Azure PowerShell 1.0 和更新版本。 如需有關 PowerShell 1.0 版本的資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 自動化帳戶，系統會將其參照為 –AutomationAccountName 和 -ApplicationDisplayName 參數的值。

若要取得指令碼所需參數 SubscriptionID、ResourceGroup 和 AutomationAccountName 的值，請執行下列動作︰

1. 在 Azure 入口網站中，按一下 [所有服務]。 在資源清單中輸入**自動化**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [自動化帳戶] 。
2. 在自動化帳戶頁面上選取您的自動化帳戶，然後在 [帳戶設定] 下選取 [屬性]。  
3. 請記下 [屬性] 頁面上的值。<br><br> ![自動化帳戶的 [屬性] 刀鋒視窗](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>更新自動化帳戶所需的權限
若要更新自動化帳戶，您必須具有下列特定權限，才能完成本主題。   
 
* 您的 AD 使用者帳戶必須新增至具備與 Microsoft.Automation 資源的參與者角色同等權限的角色 (如 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md#contributor)一文所述)。  
* 如果 Azure AD 租用戶在 [使用者設定] 頁面中的 [使用者可以註冊應用程式] 選項設定為 [是]，Azure AD 租用戶中的非管理使用者就可以[註冊 AD 應用程式](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)。 如果應用程式註冊設定為 [否]，則執行此動作的使用者必須是 Azure AD 中的全域管理員。

若您在新增至訂用帳戶的全域管理員/共同管理員角色之前，並非訂用帳戶 Active Directory 執行個體的成員，系統會將您以來賓身分新增至 Active Directory。 在此情況下，您會在 [新增自動化帳戶] 刀鋒視窗中看到 「您沒有權限建立...」的警告。 新增至全域管理員/共同管理員角色的使用者可以先從訂用帳戶的 Active Directory 執行個體中移除並重新新增，使其成為 Active Directory 中的完整使用者。 若要確認這種情況，請從 Azure 入口網站的 [Azure Active Directory] 窗格，選取 [使用者和群組]、選取 [所有使用者]，然後在選取特定使用者之後，選取 [設定檔]。 使用者設定檔之下 [使用者類型] 屬性的值不得等於 [來賓]。

## <a name="create-run-as-account-from-the-portal"></a>從 Azure 入口網站建立執行身分帳戶
在本節中，執行下列步驟以在 Azure 入口網站更新 Azure 自動化帳戶。 您可以個別建立「執行身分帳戶」和「傳統執行身分帳戶」。 如果您不需要管理傳統資源，則可以只建立 Azure 執行身分帳戶。  

1. 以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
2. 在 Azure 入口網站中，按一下 [所有服務]。 在資源清單中輸入**自動化**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [自動化帳戶] 。
3. 在 [自動化帳戶] 頁面上，從自動化帳戶清單中選取您的自動化帳戶。
4. 在左側窗格中，選取 [帳戶設定] 區段下的 [執行身分帳戶]。  
5. 根據您所需的帳戶，選取 [Azure 執行身分帳戶] 或 [Azure 傳統執行身分帳戶]。 選取 [新增 Azure 執行身分] 之後或 [新增 Azure 傳統執行身分帳戶] 窗格出現之後，並檢閱概觀資訊之後，請按一下 [建立] 繼續建立執行身分帳戶。  
6. 在 Azure 建立執行身分帳戶時，您可以在功能表的 [通知]  底下追蹤進度。 橫幅也會顯示說明帳戶正在建立。 此程序需要數分鐘的時間完成。  

## <a name="create-run-as-account-using-powershell-script"></a>使用 PowerShell 指令碼建立執行身分帳戶
這個 PowerShell 指令碼包含下列組態的支援︰

* 使用自我簽署憑證建立執行身分帳戶。
* 使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。
* 使用企業憑證授權單位 (CA) 所核發的憑證，來建立執行身分帳戶和傳統執行身分帳戶。
* 在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。

>[!NOTE]
> 如果您選取任一選項來建立傳統執行方式帳戶，在指令碼執行之後，請將公開憑證 (.cer 副檔名) 上傳至自動化帳戶建立所在之訂用帳戶的管理存放區中。
> 

1. 將下列指令碼儲存到電腦。 在此範例中，請以檔案名稱 *New-RunAsAccount.ps1*進行儲存。

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,
        
        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,
        
        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,
        
        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,
        
        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,
        
        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",
        
        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )
        
    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256
        
        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }
        
    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid
        
        $startDate = Get-Date
        $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
        
        #Create an Azure AD application, AD App Credential, AD ServicePrincipal
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
        
        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }
        
    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }
        
    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }
        
    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources
        
    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }
        
    Login-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        
    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"
        
    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
        
    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName
        
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true
        
    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}
        
    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues
        
    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"
        
        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }
        
        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false
        
        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}
        
        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues
        
        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

2. 在電腦上以提高的使用者權限從 [開始] 畫面啟動 **Windows PowerShell**。
3. 從提高權限的命令列殼層，移至包含您在步驟 1 中建立的指令碼的資料夾。  
4. 使用所需設定的參數值來執行指令碼。

    **使用自我簽署憑證建立執行身分帳戶**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **使用企業憑證建立執行身分帳戶和傳統執行身分帳戶**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > 指令碼執行之後，您會收到向 Azure 進行驗證的提示。 請以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。
    >
    >

指令碼執行成功之後，請注意下列事項︰
* 如果您使用自我簽署的公開憑證 (.cer 檔案) 建立了傳統執行身分帳戶，指令碼會建立該帳戶並將其儲存在電腦上的暫存檔案資料夾中，用來執行 PowerShell 工作階段的使用者設定檔 %USERPROFILE%\AppData\Local\Temp 底下。
* 如果您使用企業公開憑證 (.cer 檔案) 建立了傳統執行身分帳戶，請使用此憑證。 請遵循[將管理 API 憑證上傳至 Azure 入口網站](../azure-api-management-certs.md)的指示，然後使用[用來向服務 Azure 傳統部署資源進行驗證的範例程式碼](automation-verify-runas-authentication.md#classic-run-as-authentication)來驗證傳統部署資源的認證設定。 
* 如果您「並未」建立傳統執行身分帳戶，請使用[用來向服務管理資源進行驗證的程式碼範例](automation-verify-runas-authentication.md#automation-run-as-authentication)向 Resource Manager 資源進行驗證並驗證認證組態。

## <a name="limiting-run-as-account-permissions"></a>限制執行身分帳戶的權限

為了針對 Azure 自動化中的資源控制自動化目標，系統預設會將訂用帳戶中的參與者權限授予執行身分帳戶。 如果您需要限制執行身分服務主體可執行的動作，可以將帳戶從訂用帳戶的參與者角色中移除，並以參與者身分將它新增至您要指定的資源群組。

在 Azure 入口網站中，選取 [訂用帳戶]，並選擇您自動化帳戶的訂用帳戶。 選取 [存取控制 (IAM)]，並搜尋您自動化帳戶的服務主體 (它看起來像是 \<AutomationAccountName\>_unique identifier)。 選取帳戶，然後按一下 [移除] 將它從訂用帳戶中移除。

![訂用帳戶參與者](media/automation-create-runas-account/automation-account-remove-subscription.png)

若要將服務主體新增至資源群組，請在 Azure 入口網站中選取資源群組，然後選取 [存取控制 (IAM)]。 選取 [新增]，這會開啟 [新增權限] 頁面。 針對 [角色]，選取 [參與者]。 在 [選取] 文字方塊中，輸入您執行身分帳戶的服務主體名稱，並從清單中選取。 按一下 [儲存]  儲存變更。 對您想為 Azure 自動化執行身分服務主體提供存取權的資源群組，執行這項操作。

## <a name="next-steps"></a>後續步驟
* 如需服務主體的詳細資訊，請參閱[應用程式物件和服務主體物件](../active-directory/active-directory-application-objects.md)。
* 如需有關憑證和 Azure 服務的詳細資訊，請參閱 [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)。
