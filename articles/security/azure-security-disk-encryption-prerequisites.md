---
title: 先決條件 - IaaS VM 適用的 Azure 磁碟加密 | Microsoft Docs
description: 本文提供要對 IaaS VM 使用 Microsoft Azure 磁碟加密所需滿足的先決條件。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 551918373f8292d798980600d6e0d43add55bd18
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828275"
---
# <a name="azure-disk-encryption-prerequisites"></a>Azure 磁碟加密的先決條件

＜Azure 磁碟加密的先決條件＞這篇文章會說明要先備妥才能使用 Azure 磁碟加密的項目。 Azure 磁碟加密會與 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) 整合，以協助管理加密金鑰。 您可以使用 [Azure PowerShell](/powershell/azure/overview)、[Azure CLI](/cli/azure/)或 [Azure 入口網站](https://portal.azure.com)來設定 Azure 磁碟加密。

針對 [Azure 磁碟加密概觀](azure-security-disk-encryption-overview.md)一文中所討論的支援案例，當您在 Azure IaaS VM 上啟用 Azure 磁碟加密之前，請務必先備妥先決條件。 

> [!WARNING]
> - 如果您先前曾使用 [Azure 磁碟加密搭配 Azure AD 應用程式](azure-security-disk-encryption-prerequisites-aad.md)來加密此 VM，則必須繼續使用此選項來加密 VM。 您無法在此加密的 VM 上使用 [Azure 磁碟加密](azure-security-disk-encryption-prerequisites.md)，因為這不是支援的案例，表示尚未對此加密的 VM 支援從 AAD 應用程式離開。
> - 某些建議可能會增加資料、網路或計算資源的使用量，導致額外的授權或訂用帳戶成本。 您必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>支援的 VM 大小

[基本的 A 系列 vm](https://azure.microsoft.com/pricing/details/virtual-machines/series/)無法使用 Azure 磁碟加密。 Azure 磁碟加密可在符合下列最低記憶體需求的其他虛擬機器上使用：

| 虛擬機器 | 最小記憶體需求 |
|--|--|
| Windows VM | 2 GB |
| 僅加密資料磁片區時的 Linux Vm| 2 GB |
| 當加密資料和作業系統磁片區時，以及根（/）檔案系統使用量為4GB 或更少的位置時，Linux Vm | 8 GB |
| 當加密資料和作業系統磁片區，以及根（/）檔案系統使用量大於4GB 時，Linux Vm | 根檔案系統使用方式 * 2。 例如，16 GB 的根檔案系統使用量至少需要32GB 的 RAM |

一旦 Linux 虛擬機器上的 OS 磁片加密程式完成後，即可將 VM 設定成以較少的記憶體執行。 

> [!NOTE]
> [虛擬機器擴展集](../virtual-machine-scale-sets/index.yml)無法使用 Linux OS 磁片加密。

Azure 磁碟加密也適用于具有 premium 儲存體的 Vm。 

## <a name="supported-operating-systems"></a>受支援的作業系統

### <a name="windows"></a>Windows

- Windows 用戶端：Windows 8 和更新版本。
- Windows Server：Windows Server 2008 R2 和更新版本。  
 
> [!NOTE]
> Windows Server 2008 R2 需要安裝 .NET Framework 4.5 以進行加密;使用適用于 Windows Server 2008 R2 x64 型系統（[KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)）的選擇性更新 Microsoft .NET Framework 4.5.2，從 Windows Update 進行安裝。  
>  
> Windows Server 2012 R2 Core 和 Windows Server 2016 Core 需要在 VM 上安裝 bdehdcfg 元件，才能進行加密。


### <a name="linux"></a>Linux 

在經[Azure 背書的 linux](../virtual-machines/linux/endorsed-distros.md)散發套件的子集中，支援 Azure 磁碟加密，這本身是所有 Linux 伺服器可能散發套件的子集。

![支援 Azure 磁碟加密的 Linux 伺服器散發套件的卞氏圖表](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

未由 Azure 背書的 Linux 伺服器散發套件不支援 Azure 磁碟加密，而且對於背書的散發套件，只有下列散發套件和版本支援 Azure 磁碟加密：

| Linux 散發套件 | Version | 支援加密的磁碟區類型|
| --- | --- |--- |
| Ubuntu | 18.04| 作業系統和資料磁碟 |
| Ubuntu | 16.04| 作業系統和資料磁碟 |
| Ubuntu | 14.04.5</br>[搭配更新至 4.15 或更新版本的 Azure 調整核心](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | 作業系統和資料磁碟 |
| RHEL | 7.7 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.6 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.5 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.4 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.3 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 7.2 | 作業系統和資料磁片（請參閱下面的附注） |
| RHEL | 6.8 | 資料磁片（請參閱下面的附注） |
| RHEL | 6.7 | 資料磁片（請參閱下面的附注） |
| CentOS | 7.7 | 作業系統和資料磁碟 |
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
> 適用于 RHEL7 隨用隨付映射的 RHEL OS 和資料磁片支援新的 ADE 執行。 ADE 目前不支援 RHEL 自備訂用帳戶 (BYOS) 映像。 如需詳細資訊，請參閱[適用于 Linux 的 Azure 磁碟加密](azure-security-disk-encryption-linux.md)。

- Azure 磁碟加密需要您的金鑰保存庫和 VM 位於相同的 Azure 區域和訂用帳戶中。 若在不同的區域設定資源，會導致 Azure 磁碟加密功能啟用失敗。

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Linux IaaS Vm 的其他必要條件 

- Azure 磁碟加密需要在系統上出現 dm crypt 和 vfat 模組。 從預設映射移除或停用 vfat，將會導致系統無法讀取金鑰磁片區，並取得在後續重新開機時解除鎖定磁片所需的金鑰。 從系統中移除 vfat 模組的系統強化步驟，與 Azure 磁碟加密不相容。 
- 在啟用加密之前，所要加密的資料磁碟必須在 /etc/fstab 中正確列出。 請為此項目使用永續性區塊裝置名稱，原因是採用「/dev/sdX」格式的裝置名稱不一定會在重新開機時與相同的磁碟相關聯，在套用加密後尤其如此。 如需此行為的詳細資訊，請參閱：[針對 Linux VM 裝置名稱變更進行疑難排解](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- 確定 /etc/fstab 設定已正確設定而能夠掛接。 若要設定這些設定，請執行 mount -a 命令，或重新啟動 VM 再以該方式觸發重新掛接。 完成之後，檢查 lsblk 命令的輸出以確認所需的磁碟機仍有掛接。 
  - 如果 /etc/fstab 檔案還未正確掛接磁碟機就啟用加密，Azure 磁碟加密將無法正確地掛接磁碟機。
  - Azure 磁碟加密程序會在加密程序進行期間，將掛接資訊從 /etc/fstab 移出，並移入自己的設定檔中。 在資料磁碟機加密完成之後，若發現 /etc/fstab 中有項目遺失，請不要擔心。
  - 開始加密之前，請務必停止所有可能寫入已掛接之資料磁片的服務和處理常式，並將其停用，使其不會在重新開機後自動重新開機。 這些磁碟分割上的檔案可能會保持開啟狀態，防止加密程式重新掛接它們，因而導致加密失敗。 
  - 重新開機後，Azure 磁碟加密程序需要一點時間才能掛接新加密的磁碟。 這些磁碟無法在重新開機後立即可供使用。 此程序需要時間來將加密的磁碟機啟動、解除鎖定再加以掛接，然後這些磁碟機才可供其他程序來存取。 視系統特性而定，在重新開機後，此程序可能需要進行超過一分鐘。

如需可用來掛接資料磁碟並建立所需 /etc/fstab 項目的命令範例，請見[此指令檔的第 244 至 248 行](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248)。 

## <a name="bkmk_GPO"></a> 網路和群組原則

**若要啟用 Azure 磁碟加密功能，IaaS VM 必須符合下列網路端點組態需求：**
  - 若要取得用來連線至金鑰保存庫的權杖，IaaS VM 必須能連線至 Azure Active Directory 端點 \[login.microsoftonline.com\]。
  - 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  - IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../key-vault/key-vault-access-behind-firewall.md)。    


**群組原則：**
 - Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 對於加入網域的 VM，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如需關於「在不含相容 TPM 的情形下允許使用 BitLocker」的群組原則相關資訊，請參閱 [BitLocker 群組原則參考文件](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

-  具有自訂群組原則之已加入網域虛擬機器上的 BitLocker 原則必須包含下列設定：[設定 BitLocker 修復資訊的使用者儲存體-> 允許256位的修復金鑰](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密將會失敗。 在沒有正確原則設定的電腦上，您可能必須套用新的原則、強制新的原則進行更新 (gpupdate.exe /force)，然後重新啟動。

- 如果網域層級的群組原則封鎖 BitLocker 所使用的 AES CBC 演算法，Azure 磁碟加密將會失敗。


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 模型來管理 Azure 資源。 您可以在瀏覽器中將它與 [Azure Cloud Shell](../cloud-shell/overview.md) 搭配使用，也可以使用下列指示將它安裝在本機電腦上，以在任何 PowerShell 工作階段中使用它。 如果您已將它安裝在本機上，請確定您是使用最新版的 Azure PowerShell SDK 版本來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)。

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>安裝 Azure PowerShell 以便在本機電腦上使用 (選擇性)： 
1. 遵循作業系統所適用連結內的指示，然後繼續完成下列其餘步驟。      
   - [安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)。 
     - 安裝 PowerShellGet、Azure PowerShell，並載入 Az 模組。 

2. 確認 Az 模組的已安裝版本。 如有需要，[更新 Azure PowerShell 模組](/powershell/azure/install-az-ps#update-the-azure-powershell-module)。
    建議使用最新的 Az 模組版本。

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. 使用[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 登入 Azure。
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  如有需要，請檢閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。

## <a name="bkmk_CLI"></a>安裝 Azure CLI 以便在本機電腦上使用 (選擇性)

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


## <a name="prerequisite-workflow-for-key-vault"></a>Key Vault 的必要工作流程
如果您已經熟悉 Azure 磁碟加密的 Key Vault 和 Azure AD 必要條件，您可以使用 [Azure 磁碟加密必要條件 PowerShell 指令碼](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )。 如需有關使用必要指令碼的詳細資訊，請參閱 [VM 加密快速入門](../virtual-machines/linux/disk-encryption-powershell-quickstart.md)和 [Azure 磁碟加密附錄](azure-security-disk-encryption-appendix.md#bkmk_prereq-script)。 

1. 如有需要，請建立資源群組。
2. 建立金鑰保存庫。 
3. 設定金鑰保存庫進階存取原則。

>[!WARNING]
>在刪除金鑰保存庫之前，確保您並未使用該金鑰加密任何既有的虛擬機器。 若要防止意外刪除金鑰保存庫，可在保存庫啟用[虛刪除](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete)和[資源鎖定](../azure-resource-manager/resource-group-lock-resources.md)。 
 
## <a name="bkmk_KeyVault"></a> 建立金鑰保存庫 
Azure 磁碟加密會與 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 整合，協助您控制及管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 針對 Azure 磁碟加密，您可以建立金鑰保存庫，也可以使用現有保存庫。 如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../key-vault/key-vault-secure-your-key-vault.md)。 您可以使用 Resource Manager 範本、Azure PowerShell 或 Azure CLI 來建立金鑰保存庫。 


>[!WARNING]
>為了確保加密祕密不會跨出區域界限，Azure 磁碟加密需要讓 Key Vault 和 VM 共置於相同區域中。 請在和所要加密 VM 相同的區域中建立並使用 Key Vault。 


### <a name="bkmk_KVPSH"></a> 使用 PowerShell 建立金鑰保存庫

您可以使用[AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) Cmdlet，以 Azure PowerShell 建立金鑰保存庫。 如需 Key Vault 的其他 Cmdlet，請參閱[Az. KeyVault](/powershell/module/az.keyvault/)。 

1. 如有需要，請[連線至 Azure 訂用帳戶](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。 
2. 如有需要，請使用[remove-azresourcegroup](/powershell/module/az.Resources/New-azResourceGroup)建立新的資源群組。  若要列出資料中心位置，請使用[get-azlocation](/powershell/module/az.resources/get-azlocation)。 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. 使用[AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)建立新的金鑰保存庫
    
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

1. 在 Azure 快速入門範本中，按一下 [部署至 Azure]。
2. 選取訂用帳戶、資源群組、資源群組位置、Key Vault 名稱、物件識別碼、法律條款和協議，然後按一下 [購買]。 


## <a name="bkmk_KVper"></a> 設定金鑰保存庫進階存取原則
Azure 平台需要存取您金鑰保存庫中的加密金鑰或密碼，讓該資訊可供 VM 用來開機和解密磁碟區。 在金鑰保存庫或部署上啟用磁碟加密將會失敗。  

### <a name="bkmk_KVperPSH"></a> 使用 Azure PowerShell 設定金鑰保存庫進階存取原則
 使用金鑰保存庫 PowerShell Cmdlet[設定-set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)來啟用金鑰保存庫的磁片加密。

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

 - **視需要針對部署啟用 Key Vault**：可讓 Microsoft.Compute 資源提供者在資源建立期間 (例如，在建立虛擬機器時) 參考了這個金鑰保存庫的情況下，從這個金鑰保存庫擷取祕密。

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **視需要針對範本部署啟用 Key Vault**：允許 Resource Manager 從保存庫擷取秘密。
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> 透過 Azure 入口網站設定金鑰保存庫進階存取原則

1. 選取金鑰保存庫，移至 [存取原則]，然後**按一下以顯示進階存取原則**。
2. 選取標示為**為磁碟區加密啟用對 Azure 磁碟加密的存取**的方塊。
3. 視需要選取 [為部署啟用對 Azure 虛擬機器的存取] 及/或 [為範本部署啟用對 Azure Resource Manager 的存取]。 
4. 按一下 [儲存]。

    ![Azure 金鑰保存庫進階存取原則](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> 設定金鑰加密金鑰 (選擇性)
如果您想使用金鑰加密金鑰 (KEK) 來為加密金鑰額外添加一層安全性，請將 KEK 新增至金鑰保存庫。 使用[AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) Cmdlet，在金鑰保存庫中建立金鑰加密金鑰。 您也可以從內部部署金鑰管理 HSM 匯入 KEK。 如需詳細資訊，請參閱 [Key Vault 文件](../key-vault/key-vault-hsm-protected-keys.md)。 若指定了金鑰加密金鑰，Azure 磁碟加密會先使用該金鑰包裝加密祕密，再寫入 Key Vault。

* 產生金鑰時，請使用 RSA 金鑰類型。 Azure 磁碟加密還不支援使用橢圓曲線鍵。

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
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [啟用適用於 Windows 的 Azure 磁碟加密](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [啟用適用於 Linux 的 Azure 磁碟加密](azure-security-disk-encryption-linux.md)

