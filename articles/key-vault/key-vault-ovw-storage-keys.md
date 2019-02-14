---
ms.assetid: ''
title: Azure Key Vault 受控儲存體帳戶 - CLI
description: 儲存體帳戶金鑰提供 Azure Key Vault 與 Azure 儲存體帳戶的金鑰型存取之間的完美整合。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 152e1e5892e3a72286205c2f5bf4e18b2a2bcbf7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814838"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Azure Key Vault 受控儲存體帳戶 - CLI

> [!NOTE]
> [Azure 儲存體與 Azure Active Directory (Azure AD) 之間的整合現已處於預覽狀態](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。 我們建議使用 Azure AD 進行驗證和授權，因為其和 Azure Key Vault 相同，能針對 Azure 儲存體提供以 OAuth2 權杖為基礎的存取。 這可讓您：
> - 使用應用程式或使用者身分識別來驗證用戶端應用程式，而非使用儲存體帳戶認證。 
> - 在 Azure 上執行時，使用 [Azure AD 受控識別](/azure/active-directory/managed-identities-azure-resources/)。 受控識別能直接移除用戶端驗證，以及使用應用程式儲存認證或將認證儲存於應用程式中的需求。
> - 使用同時也受 Key Vault 支援的角色型存取控制 (RBAC) 來管理授權。

- Azure Key Vault 會管理 Azure 儲存體帳戶 (ASA) 的金鑰。
    - Azure Key Vault 可在內部列出 (同步) Azure 儲存體帳戶金鑰。    
    - Azure Key Vault 會定期重新產生 (輪替) 金鑰。
    - 永遠不會傳回金鑰值以回應呼叫者。
    - Azure Key Vault 會管理儲存體帳戶和傳統儲存體帳戶的金鑰。
    
> [!IMPORTANT]
> Azure AD 租用戶會為每個已註冊的應用程式提供一個**[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)**，其能作為應用程式的識別碼。 透過角色型存取控制 (RBAC) 將授權授與服務主體以存取其他 Azure 資源時，會使用服務主體的應用程式識別碼。 由於 Key Vault 是 Microsoft 應用程式，因此它已經在 Azure 雲端內以相同的應用程式識別碼，預先註冊在所有 Azure AD 租用戶中：
> - Azure 政府雲端中的 Azure AD 租用戶會使用應用程式識別碼 `7e7c393b-45d0-48b1-a35e-2905ddf8183c`。
> - Azure 公用雲端中的 Azure AD 租用戶和所有其他用戶端都會使用應用程式識別碼 `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`。

<a name="prerequisites"></a>必要條件
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 安裝 Azure CLI   
2. [建立儲存體帳戶](https://azure.microsoft.com/services/storage/)
    - 請依照本[文件](https://docs.microsoft.com/azure/storage/)中的步驟來建立儲存體帳戶  
    - **命名指導方針：** 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>如何使用 Key Vault 管理儲存體帳戶金鑰的逐步指示
--------------------------------------------------------------------------------
在下面的指示中，我們會將 Key Vault 指派為服務，以便擁有儲存體帳戶的操作員權限

> [!NOTE]
> 請注意，一旦您已經設定 Azure Key Vault 受控儲存體帳戶金鑰之後，除非透過 Key Vault，否則應該**無法**再變更。 受控儲存體帳戶金鑰意旨 Key Vault 可以管理輪替儲存體帳戶金鑰

1. 建立儲存體帳戶之後，執行下列命令來取得您想要管理的儲存體帳戶的資源識別碼

    ```
    az storage account show -n storageaccountname 
    ```
    從以上命令的結果複製識別碼欄位
    
2. 藉由執行下列命令來取得 Azure Key Vault 服務主體的物件識別碼

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    此命令成功完成時在結果中找到物件識別碼
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. 將儲存體金鑰操作員角色指派給 Azure Key Vault 身分識別

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. 建立 Key Vault 受控儲存體帳戶。     <br /><br />
   下面我們會設定 90 天的重新產生期間。 90 天之後，Key Vault 會重新產生 'key1'，並將作用中的金鑰從 'key2' 交換為 'key1'。 現在它會將 Key1 標示為作用中金鑰。 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    如果使用者未建立儲存體帳戶且沒有儲存體帳戶的權限，下列步驟會為您的帳戶設定權限，確保您可以管理 Key Vault 中的所有儲存體權限。
    
 > [!NOTE] 
 > 在使用者沒有儲存體帳戶權限的情況下，我們會先取得使用者的物件識別碼


    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="how-to-access-your-storage-account-with-sas-tokens"></a>如何使用 SAS 權杖存取您的儲存體帳戶

在這一節中，我們將討論如何藉由從 Key Vault 擷取 [SAS 權杖](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)，在您的儲存體帳戶上執行作業

在下一節中，我們會示範如何擷取 Key Vault 中儲存的儲存體帳戶金鑰，並使用該金鑰來建立儲存體帳戶的 SAS (共用存取簽章) 定義。

> [!NOTE] 
  如[基本概念](key-vault-whatis.md#basic-concepts)所述，向 Key Vault 驗證的方法有 3 種：
> - 使用受控服務識別 (不建議)
> - 使用服務主體和憑證 
> - 使用服務主體和密碼 (不建議)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

如果您的 SAS 權杖即將到期，您會從 Key Vault 再次擷取 SAS 權杖並更新程式碼

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


### <a name="relavant-azure-cli-cmdlets"></a>相關的 Azure CLI Cmdlet
[Azure CLI 儲存體 Cmdlet](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>相關的 Powershell Cmdlet

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>另請參閱

- [關於金鑰、密碼與憑證](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 小組部落格](https://blogs.technet.microsoft.com/kv/)
