---
title: 具有 Azure AD App 必要條件的 Azure 磁碟加密 (舊版)
description: 本文提供要對 IaaS VM 使用 Microsoft Azure 磁碟加密所需滿足的先決條件。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5fa8e54a6a665b1bad91a87ca8e58f873df1ae8a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672307"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Azure 磁碟加密必要條件 (舊版)

**新版的 Azure 磁碟加密不需要提供 Azure AD 應用程式參數，即可啟用 VM 磁碟加密。若使用新版本，您就不再需要在啟用加密步驟期間提供 Azure AD 認證。若使用新版本，所有新的 VM 必須經過加密，而不需要 Azure AD 應用程式參數。若要檢視使用新版本來啟用 VM 磁碟加密的指示，請參閱 [Azure 磁碟加密必要條件](azure-security-disk-encryption-prerequisites.md)。已經使用 Azure AD 應用程式參數進行加密的 VM 仍然受支援，應該繼續使用 AAD 語法進行維護。**

 ＜Azure 磁碟加密的先決條件＞這篇文章會說明要先備妥才能使用 Azure 磁碟加密的項目。 Azure 磁碟加密會連同一般先決條件與 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) 進行整合，並使用 Azure AD 應用程式來提供驗證，以管理金鑰保存庫中的加密金鑰。 您也可以使用 [Azure PowerShell](/powershell/azure/overview) 或 [Azure CLI](/cli/azure/) 來設定或配置金鑰保存庫與 Azure AD 應用程式。

針對 [Azure 磁碟加密概觀](azure-security-disk-encryption-overview.md)一文中所討論的支援案例，當您在 Azure IaaS VM 上啟用 Azure 磁碟加密之前，請務必先備妥先決條件。 

> [!WARNING]
> - 某些建議可能會增加資料、網路或計算資源的使用量，導致額外的授權或訂用帳戶成本。 您必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源。
> - 如果您先前曾使用 [Azure 磁碟加密搭配 Azure AD 應用程式](azure-security-disk-encryption-prerequisites-aad.md)來加密此 VM，則必須繼續使用此選項來加密 VM。 您無法在此加密的 VM 上使用 [Azure 磁碟加密](azure-security-disk-encryption-prerequisites.md)，因為這不是支援的案例，表示尚未對此加密的 VM 支援從 AAD 應用程式離開。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-operating-systems"></a>受支援的作業系統

### <a name="windows"></a>Windows

- Windows 用戶端：Windows 8 和更新版本。
- Windows Server：Windows Server 2008 R2 和更新版本。  
 
> [!NOTE] 
> Windows Server 2008 R2 需要.NET Framework 4.5 安裝加密;安裝從 Windows Update 以選用更新適用於 Windows Server 2008 R2 x64 型系統的 Microsoft.NET Framework 4.5.2 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983))。  
>  
> Windows Server 2012 R2 Core 和 Windows Server 2016 Core 需要加密的 VM 上安裝的 bdehdcfg 元件。

### <a name="linux"></a>Linux 

子集上支援 azure 磁碟加密[經 Azure 背書的 Linux 散發套件](../virtual-machines/linux/endorsed-distros.md)、 哪些是本身的所有 Linux 伺服器可能散發套件子集。

![支援 Azure 磁碟加密的文氏圖的 Linux 伺服器散發套件](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

不由 Azure 背書的 Linux 伺服器散發套件不支援 Azure 磁碟加密，而且這些所背書的只有下列散發套件和版本支援 Azure 磁碟加密：

| Linux 散發套件 | Version | 支援加密的磁碟區類型|
| --- | --- |--- |
| Ubuntu | 18.04| 作業系統和資料磁碟 |
| Ubuntu | 16.04| 作業系統和資料磁碟 |
| Ubuntu | 14.04.5</br>[搭配更新至 4.15 或更新版本的 Azure 調整核心](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | 作業系統和資料磁碟 |
| RHEL | 7.6 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 7.5 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 7.4 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 7.3 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 7.2 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 6.8 | 資料磁碟 （請參閱下列附註） |
| RHEL | 6.7 | 資料磁碟 （請參閱下列附註） |
| CentOS | 7.6 | 作業系統和資料磁碟 |
| CentOS | 7.5 | 作業系統和資料磁碟 |
| CentOS | 7.4 | 作業系統和資料磁碟 |
| CentOS | 7.3 | 作業系統和資料磁碟 |
| CentOS | 7.2n | 作業系統和資料磁碟 |
| CentOS | 6.8 | 資料磁碟 |
| openSUSE | 42.3 | 資料磁碟 |
| SLES | 12-SP4 | 資料磁碟 |
| SLES | 12-SP3 | 資料磁碟 |

> [!NOTE]
> 新的 ADE 實作適用於 RHEL OS 和資料磁碟 RHEL7 隨用隨付映像的支援。 ADE 目前不支援 RHEL 自備訂用帳戶 (BYOS) 映像。 請參閱[適用於 Linux 的 Azure 磁碟加密](azure-security-disk-encryption-linux.md)如需詳細資訊。

- Azure 磁碟加密需要您的金鑰保存庫和 VM 位於相同的 Azure 區域和訂用帳戶中。 若在不同的區域設定資源，會導致 Azure 磁碟加密功能啟用失敗。

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Linux IaaS Vm 適用的其他必要條件 

- Azure 磁碟加密需要 dm crypt 和 vfat 模組是在系統上。 移除或停用的預設映像的 vfat 會造成系統無法讀取金鑰的磁碟區和取得解除鎖定上後續的重新開機的磁碟所需的金鑰。 從系統移除 vfat 模組的系統強化步驟不使用 Azure 磁碟加密相容。 
- 在啟用加密之前，所要加密的資料磁碟必須在 /etc/fstab 中正確列出。 請為此項目使用永續性區塊裝置名稱，原因是採用「/dev/sdX」格式的裝置名稱不一定會在重新開機時與相同的磁碟相關聯，在套用加密後尤其如此。 如需此行為的詳細資訊，請參閱：[針對 Linux VM 裝置名稱變更進行疑難排解](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- 確定 /etc/fstab 設定已正確設定而能夠掛接。 若要設定這些設定，請執行 mount -a 命令，或重新啟動 VM 再以該方式觸發重新掛接。 完成之後，檢查 lsblk 命令的輸出以確認所需的磁碟機仍有掛接。 
  - 如果 /etc/fstab 檔案還未正確掛接磁碟機就啟用加密，Azure 磁碟加密將無法正確地掛接磁碟機。
  - Azure 磁碟加密程序會在加密程序進行期間，將掛接資訊從 /etc/fstab 移出，並移入自己的設定檔中。 在資料磁碟機加密完成之後，若發現 /etc/fstab 中有項目遺失，請不要擔心。
  - 開始加密，請務必停止之前所有的服務和程序，可能會寫入掛接資料磁碟，並停用它們，以便他們不要自動重新啟動之後重新開機。 這些可以讓檔案保持為開啟這些分割區，導致重新掛接它們，造成失敗之加密的加密程序上。 
  - 重新開機後，Azure 磁碟加密程序需要一點時間才能掛接新加密的磁碟。 這些磁碟無法在重新開機後立即可供使用。 此程序需要時間來將加密的磁碟機啟動、解除鎖定再加以掛接，然後這些磁碟機才可供其他程序來存取。 視系統特性而定，在重新開機後，此程序可能需要進行超過一分鐘。

如需可用來掛接資料磁碟並建立所需 /etc/fstab 項目的命令範例，請見[此指令檔的第 244 至 248 行](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248)。 

## <a name="bkmk_GPO"></a> 網路和群組原則

**若要使用較舊的 AAD 參數語法啟用 Azure 磁碟加密功能，IaaS VM 必須符合下列網路端點組態需求：** 
  - 若要取得用來連線至金鑰保存庫的權杖，IaaS VM 必須能連線至 Azure Active Directory 端點 \[login.microsoftonline.com\]。
  - 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  - IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../key-vault/key-vault-access-behind-firewall.md)。
  - 在 Windows 中，如果 TLS 1.0 已明確停用，且 .NET 版本尚未更新到 4.6 版或更新版本，則下列登錄變更會啟用 ADE 來選取較新的 TLS 版本：
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**群組原則：**
 - Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 對於加入網域的 VM，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如需關於「在不含相容 TPM 的情形下允許使用 BitLocker」的群組原則相關資訊，請參閱 [BitLocker 群組原則參考文件](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

-  具有自訂群組原則之已加入網域虛擬機器上的 BitLocker 原則必須包含下列設定：[設定使用者存放裝置的 BitLocker 修復資訊-> 允許 256 位元修復金鑰](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密將會失敗。 在沒有正確原則設定的電腦上，您可能必須套用新的原則、強制新的原則進行更新 (gpupdate.exe /force)，然後重新啟動。  


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 模型來管理 Azure 資源。 您可以在瀏覽器中將它與 [Azure Cloud Shell](../cloud-shell/overview.md) 搭配使用，也可以使用下列指示將它安裝在本機電腦上，以在任何 PowerShell 工作階段中使用它。 如果您已經有在本機安裝，請確定您使用最新版的 Azure PowerShell 來設定 Azure 磁碟加密。

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>安裝 Azure PowerShell 以便在本機電腦上使用 (選擇性)：  
1. [安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)。 

2. 安裝 [Azure Active Directory PowerShell 模組](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module)。 

     ```powershell
     Install-Module AzureAD
     ```

3. 確認已安裝的模組版本。
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. 若要設定使用的 Azure 登入[Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet。
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  連線至 Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread)。
     
     ```powershell
     Connect-AzureAD
     ```

6. 如有需要，檢閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps) 和 [AzureAD](/powershell/module/azuread)。

## <a name="bkmk_CLI"></a> Azure CLI

[Azure CLI 2.0](/cli/azure) 是命令列工具，可用於管理 Azure 資源。 CLI 的設計是要讓您能夠彈性地查詢資料、以非封鎖處理序的形式支援長時間執行作業，並輕鬆地撰寫指令碼。 您可以在瀏覽器中將它與 [Azure Cloud Shell](../cloud-shell/overview.md) 搭配使用，或可將它安裝在本機電腦上，並在任何 PowerShell 工作階段中使用它。

1. [安裝 Azure CLI](/cli/azure/install-azure-cli) 以便在本機電腦上使用 (選擇性)：

2. 確認已安裝的版本。
     
     ```azurecli-interactive
     az --version
     ``` 

3. 使用 [az login](/cli/azure/authenticate-azure-cli) 登入 Azure。
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. 如有需要，檢閱[開始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)。 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Key Vault 和 Azure AD 應用程式的先決條件工作流程

如果您已經熟悉 Azure 磁碟加密的 Key Vault 和 Azure AD 必要條件，您可以使用 [Azure 磁碟加密必要條件 PowerShell 指令碼](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )。 如需有關使用必要指令碼的詳細資訊，請參閱 [VM 加密快速入門](quick-encrypt-vm-powershell.md)和 [Azure 磁碟加密附錄](azure-security-disk-encryption-appendix.md#bkmk_prereq-script)。 

1. 建立金鑰保存庫。 
2. 設定 Azure AD 應用程式和服務主體。
3. 設定 Azure AD 應用程式的金鑰保存庫存取原則。
4. 設定金鑰保存庫進階存取原則。
 
## <a name="bkmk_KeyVault"></a> 建立金鑰保存庫 
Azure 磁碟加密會與 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 整合，協助您控制及管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 針對 Azure 磁碟加密，您可以建立金鑰保存庫，也可以使用現有保存庫。 如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../key-vault/key-vault-secure-your-key-vault.md)。 您可以使用 Resource Manager 範本、Azure PowerShell 或 Azure CLI 來建立金鑰保存庫。 


>[!WARNING]
>為了確保加密祕密不會跨出區域界限，Azure 磁碟加密需要讓 Key Vault 和 VM 共置於相同區域中。 請在和所要加密 VM 相同的區域中建立並使用 Key Vault。 


### <a name="bkmk_KVPSH"></a> 使用 PowerShell 建立金鑰保存庫

您可以使用 Azure PowerShell 建立 key vault[新增 AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet。 金鑰保存庫的其他 cmdlet，請參閱[Az.KeyVault](/powershell/module/az.keyvault/)。 

1. 如有需要，請[連線至 Azure 訂用帳戶](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。 
2. 如有需要建立新的資源群組，具有[新增 AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)。  若要列出的資料中心位置，請使用[Get AzLocation](/powershell/module/az.resources/get-azlocation)。 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. 建立新的金鑰保存庫使用[新增 AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. 記下傳回的**保存庫名稱**、**資源群組名稱**、**資源識別碼**、**保存庫 URI** 和**物件識別碼**，以供稍後在加密磁碟時使用。 


### <a name="bkmk_KVCLI"></a> 使用 Azure CLI 建立金鑰保存庫
您可以搭配使用 Azure CLI 與 [az keyvault](/cli/azure/keyvault#commands) 命令來管理金鑰保存庫。 若要建立金鑰保存庫，請使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)。

1. 如有需要，請[連線至 Azure 訂用帳戶](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 如有需要，請使用 [az group create](/cli/azure/group#az-group-create) 建立新的資源群組。 若要列出位置，請使用 [az account list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. 使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create) 建立新的金鑰保存庫。
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. 記下傳回的**保存庫名稱** (名稱)、**資源群組名稱**、**資源識別碼** (識別碼)、**保存庫 URI** 和**物件識別碼**，以供稍後使用。 

### <a name="bkmk_KVRM"></a> 使用 Resource Manager 範本建立金鑰保存庫

您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)來建立金鑰保存庫。

1. 在 Azure 快速入門範本中，按一下 [部署至 Azure]  。
2. 選取訂用帳戶、資源群組、資源群組位置、Key Vault 名稱、物件識別碼、法律條款和協議，然後按一下 [購買]  。 


## <a name="bkmk_ADapp"></a> 設定 Azure AD 應用程式和服務主體 
當您需要在 Azure 中執行中的 VM 上啟用加密時，Azure 磁碟加密會產生並將加密金鑰寫入金鑰保存庫。 在金鑰保存庫中管理加密金鑰需要 Azure AD 驗證。 基於此目的，請建立 Azure AD 應用程式。 針對驗證目的，您可以使用用戶端密碼式驗證或[用戶端憑證式 Azure AD 驗證](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)。


### <a name="bkmk_ADappPSH"></a> 使用 Azure PowerShell 設定 Azure AD 應用程式和服務主體 
若要執行下列命令，請取得並使用 [Azure AD PowerShell 模組](/powershell/azure/active-directory/install-adv2)。 

1. 如有需要，請[連線至 Azure 訂用帳戶](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。
2. 使用[新增 AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell cmdlet 來建立 Azure AD 應用程式。 MyApplicationHomePage 和 MyApplicationUri 可以是任何您想要的值。

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId 是 Azure AD ClientID，而 $aadClientSecret 是用戶端密碼，您稍後會用此資訊來啟用 Azure 磁碟加密。 適當地保護 Azure AD 用戶端密碼。 執行 `$azureAdApplication.ApplicationId` 會顯示 ApplicationID。


### <a name="bkmk_ADappCLI"></a> 使用 Azure CLI 設定 Azure AD 應用程式和服務主體

您可以搭配使用 Azure CLI 和 [az ad sp](/cli/azure/ad/sp) 命令來管理服務主體。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

1. 如有需要，請[連線至 Azure 訂用帳戶](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 建立新的服務主體。
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  所傳回的 appId 是其他命令中所使用的 Azure AD ClientID。 它也是您會用於 az keyvault set-policy 的 SPN。 此密碼是您稍後應該會用來啟用 Azure 磁碟加密的用戶端密碼。 適當地保護 Azure AD 用戶端密碼。
 
### <a name="bkmk_ADappRM"></a> 透過 Azure 入口網站設定 Azure AD 應用程式和服務主體
請使用[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)一文中的步驟來建立 Azure AD 應用程式。 下面所列的每個步驟各會帶您直接前往要完成的文章章節。 

1. [確認所需權限](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - 在建立應用程式時，您可以使用任何您想要的名稱和登入 URL。
3. [取得應用程式識別碼和驗證金鑰](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)。 
     - 驗證金鑰是用戶端密碼，並作為組 AzVMDiskEncryptionExtension AadClientSecret。 
        - 應用程式會使用驗證金鑰作為認證來登入 Azure AD。 在 Azure 入口網站中，此密碼稱為金鑰，但實際上與金鑰保存庫並無任何關聯。 請適當地保護這個祕密。 
     - 應用程式識別碼將用於稍後的組 AzVMDiskEncryptionExtension AadClientId 以及如組 AzKeyVaultAccessPolicy ServicePrincipalName。 

## <a name="bkmk_KVAP"></a> 設定 Azure AD 應用程式的金鑰保存庫存取原則
為了將加密祕密寫入指定的 Key Vault，Azure 磁碟加密需要有權將祕密寫入 Key Vault 的 Azure Active Directory 應用程式用戶端識別碼和用戶端密碼。 

> [!NOTE]
> 若要使用 Azure 磁碟加密，您必須對 Azure AD 用戶端應用程式設定下列存取原則：_WrapKey_ 和 _Set_ 權限。

### <a name="bkmk_KVAPPSH"></a> 使用 Azure PowerShell 設定 Azure AD 應用程式的金鑰保存庫存取原則
您的 Azure AD 應用程式需要權限，才能存取保存庫中的金鑰或密碼。 使用[組 AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 可授與權限的應用程式中，然後再使用用戶端識別碼 （登錄應用程式時所產生） 做為 _-ServicePrincipalName_參數值。 若要深入了解，請參閱部落格文章 [Azure Key Vault - 逐步解說](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)。 

1. 如有需要，請[連線至 Azure 訂用帳戶](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。
2. 使用 PowerShell 設定 AD 應用程式的金鑰保存庫存取原則。

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a> 使用 Azure CLI 設定 Azure AD 應用程式的金鑰保存庫存取原則
使用 [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) 來設定存取原則。 如需詳細資訊，請參閱[使用 CLI 2.0 管理 Key Vault](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)。

1. 如有需要，請[連線至 Azure 訂用帳戶](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 使用下列命令，提供您透過 Azure CLI 存取權所建立的服務主體，來取得祕密並包裝金鑰：
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> 使用入口網站設定 Azure AD 應用程式的金鑰保存庫存取原則

1. 使用金鑰保存庫開啟資源群組。
2. 選取金鑰保存庫，移至 [存取原則]  ，然後按一下 [新增]  。
3. 在 [選取主體]  底下，搜尋您所建立的 Azure AD 應用程式，並加以選取。 
4. 針對 [金鑰權限]  ，勾選 [密碼編譯作業]  底下的 [包裝金鑰]  。
5. 針對 [祕密權限]  ，勾選 [祕密管理作業]  底下的 [設定]  。
6. 按一下 [確定]  以儲存存取原則。 

![Azure Key Vault 密碼編譯作業 - 包裝金鑰](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault 密碼權限 - 設定](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> 設定金鑰保存庫進階存取原則
Azure 平台需要存取您金鑰保存庫中的加密金鑰或密碼，讓該資訊可供 VM 用來開機和解密磁碟區。 在金鑰保存庫或部署上啟用磁碟加密將會失敗。  

### <a name="bkmk_KVperPSH"></a> 使用 Azure PowerShell 設定金鑰保存庫進階存取原則
 使用金鑰保存庫 PowerShell cmdlet[組 AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)啟用磁碟加密金鑰保存庫。

  - **針對磁碟加密啟用 Key Vault**：Azure 磁碟加密需要 EnabledForDiskEncryption。
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **視需要針對部署啟用 Key Vault**：可讓 Microsoft.Compute 資源提供者在資源建立期間 (例如，在建立虛擬機器時) 參考了這個金鑰保存庫的情況下，從這個金鑰保存庫擷取祕密。

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **視需要針對範本部署啟用 Key Vault**：可讓 Azure Resource Manager 在範本部署參考了這個金鑰保存庫的情況下，從這個金鑰保存庫取得祕密。

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> 使用 Azure CLI 設定金鑰保存庫進階存取原則
使用 [az keyvault update](/cli/azure/keyvault#az-keyvault-update) 啟用金鑰保存庫的磁碟加密。 

 - **針對磁碟加密啟用 Key Vault**：需要 Enabled-for-disk-encryption。 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **視需要針對部署啟用 Key Vault**：允許虛擬機器從保存庫擷取儲存為祕密的憑證。
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **視需要針對範本部署啟用 Key Vault**：允許 Resource Manager 從保存庫擷取秘密。
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> 透過 Azure 入口網站設定金鑰保存庫進階存取原則

1. 選取金鑰保存庫，移至 [存取原則]  ，然後**按一下以顯示進階存取原則**。
2. 選取標示為**為磁碟區加密啟用對 Azure 磁碟加密的存取**的方塊。
3. 視需要選取 [為部署啟用對 Azure 虛擬機器的存取]  及/或 [為範本部署啟用對 Azure Resource Manager 的存取]  。 
4. 按一下 [儲存]  。

![Azure 金鑰保存庫進階存取原則](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> 設定金鑰加密金鑰 (選擇性)
如果您想使用金鑰加密金鑰 (KEK) 來為加密金鑰額外添加一層安全性，請將 KEK 新增至金鑰保存庫。 使用[新增 AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 來建立金鑰保存庫中的金鑰加密金鑰。 您也可以從內部部署金鑰管理 HSM 匯入 KEK。 如需詳細資訊，請參閱 [Key Vault 文件](../key-vault/key-vault-hsm-protected-keys.md)。 若指定了金鑰加密金鑰，Azure 磁碟加密會先使用該金鑰包裝加密祕密，再寫入 Key Vault。 

* 當產生金鑰，使用 RSA 金鑰類型。 Azure 磁碟加密還不支援使用橢圓曲線金鑰。

* 您的金鑰保存庫密碼和 KEK URL 必須已設定版本。 Azure 會強制執行設定版本的這項限制。 針對有效的密碼和 KEK URL，請參閱下列範例︰

  * 有效祕密 URL 的範例： *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 有效 KEK URL 的範例： *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure 磁碟加密不支援將連接埠號碼指定為金鑰保存庫密碼和 KEK URL 的一部分。 如需不支援和支援的金鑰保存庫 URL 範例，請參閱下列範例：

  * 無法接受的金鑰保存庫 URL *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 可接受的金鑰保存庫 URL： *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> 使用 Azure PowerShell 設定金鑰加密金鑰 
在使用 PowerShell 指令碼之前，請先熟悉 Azure 磁碟加密的先決條件，以了解指令碼中的步驟。 範例指令碼可能需要隨環境加以變更。 此指令碼會建立所有 Azure 磁碟加密先決條件，並加密現有 IaaS VM 以使用金鑰加密金鑰包裝磁碟加密金鑰。 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> 憑證式驗證 (選擇性)
如果您想要使用憑證驗證，您可以在金鑰保存庫上傳一個憑證驗證，並將它部署到用戶端。 在使用 PowerShell 指令碼之前，請先熟悉 Azure 磁碟加密的先決條件，以了解指令碼中的步驟。 範例指令碼可能需要隨環境加以變更。

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> 憑證式驗證和 KEK (選擇性)

如果您想要使用憑證驗證，並以 KEK 包裝加密金鑰，您可以使用下列指令碼作為範例。 在使用 PowerShell 指令碼之前，請先熟悉所有先前的 Azure 磁碟加密先決條件，以了解指令碼中的步驟。 範例指令碼可能需要隨環境加以變更。

> [!IMPORTANT]
> Linux VM 目前不支援 Azure AD 憑證式驗證。



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [啟用適用於 Windows 的 Azure 磁碟加密](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [啟用適用於 Linux 的 Azure 磁碟加密](azure-security-disk-encryption-linux-aad.md)
