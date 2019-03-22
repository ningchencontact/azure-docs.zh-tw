---
ms.assetid: ''
title: Azure Key Vault 受控儲存體帳戶 - CLI
description: 儲存體帳戶金鑰提供 Azure Key Vault 與 Azure 儲存體帳戶的金鑰型存取之間的完美整合。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: c2107e501affd5e3dd22e0fbc83d078b51d414a5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57841135"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Azure Key Vault 受控儲存體帳戶 - CLI

> [!NOTE]
> [Azure 儲存體與 Azure Active Directory (Azure AD) 之間的整合現已處於預覽狀態](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。 我們建議使用 Azure AD 進行驗證和授權，因為其和 Azure Key Vault 相同，能針對 Azure 儲存體提供以 OAuth2 權杖為基礎的存取。 這可讓您：
> - 使用應用程式或使用者身分識別來驗證用戶端應用程式，而非使用儲存體帳戶認證。 
> - 在 Azure 上執行時，使用 [Azure AD 受控識別](/azure/active-directory/managed-identities-azure-resources/)。 受控識別能直接移除用戶端驗證，以及使用應用程式儲存認證或將認證儲存於應用程式中的需求。
> - 使用同時也受 Key Vault 支援的角色型存取控制 (RBAC) 來管理授權。

[Azure 儲存體帳戶](/azure/storage/storage-create-storage-account)會使用包含帳戶名稱和金鑰的認證。 該金鑰是由系統自動產生，且比單純作為「密碼」的密碼編譯金鑰還具有更多功能。 Key Vault 可透過將這些儲存體帳戶金鑰儲存為 [Key Vault 祕密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)來管理它們。 

## <a name="overview"></a>概觀

Key Vault 受控儲存體帳戶功能會代表您執行數個管理功能：

- 搭配 Azure 儲存體帳戶列出 (同步) 金鑰。
- 定期重新產生 (輪替) 金鑰。
- 同時管理儲存體帳戶和傳統儲存體帳戶的金鑰。
- 永遠不會傳回金鑰值以回應呼叫者。

當您使用受控儲存體帳戶金鑰功能時：

- **只允許 Key Vault 管理您的儲存體帳戶金鑰。** 請勿嘗試自行管理，因為您會干擾 Key Vault 的處理序。
- **不要允許多個 Key Vault 物件管理儲存體帳戶金鑰**。
- **不要手動重新產生儲存體帳戶金鑰**。 建議您透過 Key Vault 重新產生它們。
- 要求 Key Vault 來管理儲存體帳戶可以由目前的使用者主體和服務主體

下列範例會示範如何允許 Key Vault 管理您的儲存體帳戶金鑰。

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
在概念上，遵循的步驟清單是
- 我們先取得 (現有的) 儲存體帳戶
- 我們再擷取 (現有的) 金鑰保存庫
- 然後，我們將 KeyVault 管理的儲存體帳戶新增到保存庫，將 Key1 設定為作用中金鑰，且重新產生週期為 180 天
- 最後，我們使用 Key1 為指定的儲存體帳戶設定儲存體內容

在下面的指示中，我們會將 Key Vault 指派為服務，以便擁有儲存體帳戶的操作員權限

> [!NOTE]
> 請注意，一旦您已經設定 Azure Key Vault 受控儲存體帳戶金鑰之後，除非透過 Key Vault，否則應該**無法**再變更。 受控儲存體帳戶金鑰意旨 Key Vault 可以管理輪替儲存體帳戶金鑰

> [!IMPORTANT]
> Azure AD 租用戶會為每個已註冊的應用程式提供一個**[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)**，其能作為應用程式的識別碼。 透過角色型存取控制 (RBAC) 將授權授與服務主體以存取其他 Azure 資源時，會使用服務主體的應用程式識別碼。 由於 Key Vault 是 Microsoft 應用程式，因此它已經在 Azure 雲端內以相同的應用程式識別碼，預先註冊在所有 Azure AD 租用戶中：
> - Azure 政府雲端中的 Azure AD 租用戶會使用應用程式識別碼 `7e7c393b-45d0-48b1-a35e-2905ddf8183c`。
> - Azure 公用雲端中的 Azure AD 租用戶和所有其他用戶端都會使用應用程式識別碼 `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`。


1. 建立儲存體帳戶之後，執行下列命令來取得您想要管理的儲存體帳戶的資源識別碼

    ```
    az storage account show -n storageaccountname 
    ```
    從上述命令如下所示的結果複製 [識別碼] 欄位
    ```
    /subscriptions/0xxxxxx-4310-48d9-b5ca-0xxxxxxxxxx/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    
2. 將 儲存體帳戶金鑰操作員服務角色 」 的 RBAC 角色指派至金鑰保存庫，限制儲存體帳戶的存取範圍。 對於傳統儲存體帳戶，使用 「 傳統儲存體帳戶金鑰操作員服務角色。 」
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    ' 93c27d83-f79b-4cb2-8dd4-4aa716542e74' 是公用雲端中的金鑰保存庫的物件識別碼。 若要取得國家/地區雲端中的 Key Vault 物件識別碼請參閱上述的重要區段
    
3. 建立 Key Vault 受控儲存體帳戶。     <br /><br />
   下面我們會設定 90 天的重新產生期間。 90 天之後，Key Vault 會重新產生 'key1'，並將作用中的金鑰從 'key2' 交換為 'key1'。 現在它會將 Key1 標示為作用中金鑰。 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>如何使用 Key Vault 來建立及產生 SAS 權杖的逐步指示
--------------------------------------------------------------------------------
您也可以要求 Key Vault 產生 SAS (共用存取簽章) 權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 透過 SAS，您可以對用戶端授與儲存體帳戶中資源的存取權，而不必共用帳戶金鑰。 這是在您應用程式中使用共用存取簽章的重點 - SAS 是共用儲存體資源的安全方式，而不會危害您的帳戶金鑰。

當您完成上面所列的步驟之後，可以執行下列命令，以要求 Key Vault 為您產生 SAS 權杖。 

會在以下步驟中完成的事項清單為
- 在您保存庫 '<VaultName>' 中由 KeyVault 管理的儲存體帳戶 '<YourStorageAccountName>' 上，設定名為 '<YourSASDefinitionName>' 的帳戶 SAS 定義。 
- 透過 Https 並使用指定的開始和結束日期，為資源型「服務」、「容器」和「物件」，建立服務 Blob、檔案、資料表和佇列的帳戶 SAS 權杖
- 使用如上面所建立 SAS 權杖的範本 URI，在保存庫中設定 KeyVault 管理的儲存體 SAS 定義，其 SAS 類型為 'account' 且有效期為 N 天
- 從與 SAS 定義相對應的 KeyVault 祕密擷取實際的存取權杖

1. 我們將在此步驟中建立 SAS 定義。 建立 SAS 定義之後，您可以要求 Key Vault 為您產生更多 SAS 權杖。 此作業需要 storage/setsas 權限。

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
您可以在[這裡](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)查看上述作業的更多說明

當此作業執行成功時，您應該會看到如下的輸出。 複製它

```console
   "se=2020-01-01&sp=***"
```

1. 在此步驟中，我們將使用上面產生的輸出 ($sasToken) 來建立 SAS 定義。 如需詳細文件，請參閱[這裡](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > 在使用者沒有儲存體帳戶權限的情況下，我們會先取得使用者的物件識別碼

 ```
 az ad user show --upn-or-object-id "developer@contoso.com"

 az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
 ```
    
## <a name="fetch-sas-tokens-in-code"></a>在程式碼中擷取 SAS 權杖

在這一節中，我們將討論如何藉由從 Key Vault 擷取 [SAS 權杖](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)，在您的儲存體帳戶上執行作業

在以下小節中，我們會示範當上述 SAS 權杖建立之後，如何擷取 SAS 權杖。

> [!NOTE]
>   如[基本概念](key-vault-whatis.md#basic-concepts)所述，向 Key Vault 驗證的方法有 3 種：
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

### <a name="relevant-azure-cli-commands"></a>相關的 Azure CLI 命令

[Azure CLI 儲存體命令](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>請參閱

- [關於金鑰、密碼與憑證](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 小組部落格](https://blogs.technet.microsoft.com/kv/)
