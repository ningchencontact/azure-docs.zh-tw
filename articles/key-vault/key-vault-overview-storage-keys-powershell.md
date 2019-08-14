---
title: Azure Key Vault 受控儲存體帳戶 - PowerShell 版本
description: 受控儲存體帳戶功能可在 Azure Key Vault 與 Azure 儲存體帳戶之間提供完美整合。
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 708c34347966eee7817ca04e0552dcba233765cb
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934505"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault 受控儲存體帳戶 - PowerShell

> [!NOTE]
> [Azure 儲存體與 Azure Active Directory (Azure AD) 之間的整合現已處於預覽狀態](../storage/common/storage-auth-aad.md)。 我們建議使用 Azure AD 進行驗證和授權，因為其和 Azure Key Vault 相同，能針對 Azure 儲存體提供以 OAuth2 權杖為基礎的存取。 這可讓您：
> - 使用應用程式或使用者身分識別來驗證用戶端應用程式，而非使用儲存體帳戶認證。 
> - 在 Azure 上執行時，使用 [Azure AD 受控識別](/azure/active-directory/managed-identities-azure-resources/)。 受控識別能直接移除用戶端驗證，以及使用應用程式儲存認證或將認證儲存於應用程式中的需求。
> - 使用同時也受 Key Vault 支援的角色型存取控制 (RBAC) 來管理授權。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure 儲存體帳戶](/azure/storage/storage-create-storage-account)會使用包含帳戶名稱和金鑰的認證。 該金鑰是由系統自動產生，且比單純作為「密碼」的密碼編譯金鑰還具有更多功能。 Key Vault 可透過將這些儲存體帳戶金鑰儲存為 [Key Vault 祕密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)來管理它們。 

## <a name="overview"></a>總覽

Key Vault 受控儲存體帳戶功能會代表您執行數個管理功能：

- 搭配 Azure 儲存體帳戶列出 (同步) 金鑰。
- 定期重新產生 (輪替) 金鑰。
- 同時管理儲存體帳戶和傳統儲存體帳戶的金鑰。
- 永遠不會傳回金鑰值以回應呼叫者。

當您使用受控儲存體帳戶金鑰功能時：

- **只允許 Key Vault 管理您的儲存體帳戶金鑰。** 請勿嘗試自行管理，因為您會干擾 Key Vault 的處理序。
- **不要允許多個 Key Vault 物件管理儲存體帳戶金鑰**。
- **不要手動重新產生儲存體帳戶金鑰**。 建議您透過 Key Vault 重新產生它們。

下列範例會示範如何允許 Key Vault 管理您的儲存體帳戶金鑰。

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>授與 Key Vault 儲存體帳戶的存取權

> [!IMPORTANT]
> Azure AD 租用戶會為每個已註冊的應用程式提供一個 **[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)** ，其能作為應用程式的識別碼。 透過角色型存取控制 (RBAC) 將授權授與服務主體以存取其他 Azure 資源時，會使用服務主體的應用程式識別碼。 由於 Key Vault 是 Microsoft 應用程式，因此它已經在 Azure 雲端內以相同的應用程式識別碼，預先註冊在所有 Azure AD 租用戶中：
> - Azure 政府雲端中的 Azure AD 租用戶會使用應用程式識別碼 `7e7c393b-45d0-48b1-a35e-2905ddf8183c`。
> - Azure 公用雲端中的 Azure AD 租用戶和所有其他用戶端都會使用應用程式識別碼 `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`。

在 Key Vault 可以存取並管理您的儲存體帳戶金鑰之前，您必須為它授權以存取您的儲存體帳戶。 Key Vault 應用程式需要「列出」及「重新產生」儲存體帳戶金鑰的使用權限。 這些使用權限是透過內建的 RBAC 角色[儲存體帳戶金鑰操作員服務角色](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)來啟用。 

使用下列步驟將此角色指派給 Key Vault 服務主體，並將範圍限制在您的儲存體帳戶。 在執行指令碼之前，請務必更新 `$resourceGroupName`、`$storageAccountName`、`$storageAccountKey`，以及 `$keyVaultName` 變數：

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

角色指派成功時，您應該會看到類似於以下範例的輸出：

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

如果 Key Vault 在您的儲存體帳戶上已經被新增至該角色，您將會收到「角色指派已存在。」 的錯誤。 您也可以使用 Azure 入口網站中該儲存體帳戶的 [存取控制 (IAM)] 頁面來驗證角色指派。  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>將使用者帳戶使用權限授與受控儲存體帳戶

>[!TIP] 
> 就像 Azure AD 會針對應用程式的識別碼提供**服務主體**一般，其也會針對使用者的身分識別提供**使用者主體**。 接著可以透過 Key Vault 存取原則使用權限，將存取 Key Vault 的授權授與使用者主體。

使用相同的 PowerShell 工作階段，針對受控儲存體帳戶更新 Key Vault 存取原則。 此步驟會將儲存體帳戶使用權限套用至您的使用者帳戶，以確保您可以存取受控儲存體帳戶功能： 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

請注意，適用於儲存體帳戶的使用權限並不會在該儲存體於 Azure 入口網站中的 [存取原則] 頁面上提供。

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>將受控儲存體帳戶新增到您的 Key Vault 執行個體

使用相同的 PowerShell 工作階段，在您的 Key Vault 執行個體中建立受控儲存體帳戶。 `-DisableAutoRegenerateKey` 參數會指定「不」重新產生儲存體帳戶金鑰。

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

在不搭配金鑰重新產生成功新增儲存體帳戶時，您應該會看到類似於以下範例的輸出：

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>啟用金鑰重新產生

如果您想要讓 Key Vault 定期重新產生儲存體帳戶金鑰，您可以設定重新產生期間。 在下列範例中，我們會設定為期三天的重新產生期間。 3 天之後，Key Vault 會重新產生 'key1'，並將作用中的金鑰從 'key2' 交換為 'key1'。

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

在搭配金鑰重新產生成功新增儲存體帳戶時，您應該會看到類似於以下範例的輸出：

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>後續步驟

- [受控儲存體帳戶金鑰範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) \(英文\)
- [關於金鑰、密碼與憑證](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell 參考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
