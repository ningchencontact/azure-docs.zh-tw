---
title: 管理 Azure 金鑰保存庫和 Azure CLI 與儲存體帳戶金鑰
description: 儲存體帳戶金鑰提供 Azure Key Vault 和金鑰為基礎的存取，Azure 儲存體帳戶之間的緊密整合。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476161"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>管理 Azure 金鑰保存庫和 Azure CLI 與儲存體帳戶金鑰 

Azure Key Vault 會管理 Azure 儲存體帳戶和傳統儲存體帳戶的金鑰。 您可以使用 Key Vault 受控儲存體帳戶功能，為您完成幾個重要的管理功能。

[Azure 儲存體帳戶](/azure/storage/storage-create-storage-account)會使用包含帳戶名稱和金鑰的認證。 索引鍵會自動產生，並做為密碼，而不是做為密碼編譯金鑰。 Key Vault 會管理儲存體帳戶金鑰，藉以儲存這些做[Key Vault 祕密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)。 列出 （同步處理） 的 Azure 儲存體帳戶金鑰，並定期重新產生或_旋轉_。 

當您使用受控儲存體帳戶金鑰功能時，請考慮下列幾點：

- 索引鍵的值永遠不會傳回給呼叫者的回應。
- 只為金鑰保存庫應管理儲存體帳戶金鑰。 不會自行管理金鑰，並避免干擾 Key Vault 的處理程序。
- 只有單一的 Key Vault 物件應該管理儲存體帳戶金鑰。 不允許從多個物件的金鑰管理。
- 您可以要求 Key Vault 來管理儲存體帳戶，使用者主體，但不是使用服務主體。
- 重新產生金鑰，只會使用金鑰保存庫。 不以手動方式重新產生儲存體帳戶金鑰。 

> [!NOTE]
> Azure 儲存體整合與 Azure Active Directory (Azure AD) 是 Microsoft 的雲端式身分識別和存取管理服務。
> Azure AD 整合可供[Azure blob 和佇列](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。
> 使用 Azure AD 進行驗證和授權。
> Azure AD 提供 OAuth2 權杖為基礎的存取，就像 Azure Key Vault 的 Azure 儲存體。
>
> Azure AD 可讓您使用的應用程式或使用者的身分識別，而不儲存體帳戶認證來驗證用戶端應用程式。
> 您可以使用[受管理的 Azure AD 識別](/azure/active-directory/managed-identities-azure-resources/)您在 Azure 上執行的時機。 受管理的身分識別會移除用戶端驗證，並儲存認證，或您的應用程式的需求。
> Azure AD 會使用角色型存取控制 (RBAC) 來管理授權，這也會受到金鑰保存庫。

### <a name="service-principal-application-id"></a>服務主體的應用程式識別碼

Azure AD 租用戶提供每個已註冊的應用程式與[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服務主體做為應用程式識別碼 (ID)。 應用程式識別碼用於授權安裝期間透過 RBAC 其他 Azure 資源的存取權。

Key Vault 是 Microsoft 應用程式在所有 Azure AD 租用戶中預先註冊。 在相同的應用程式識別碼，並在每個 Azure 雲端內，會註冊金鑰保存庫。

| 租用戶 | 雲端 | 應用程式識別碼 |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>必要條件

管理儲存體帳戶金鑰的情況下，您在使用金鑰保存庫之前，檢閱必要條件：

- 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。
- 建立[Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)。 請遵循[這些步驟](https://docs.microsoft.com/azure/storage/)。
- 儲存體帳戶名稱必須使用小寫字母和數字。 名稱的長度必須介於 3 到 24 個字元之間。        
      
## <a name="manage-storage-account-keys"></a>管理儲存體帳戶金鑰

有四個基本步驟，以使用 Key Vault 來管理儲存體帳戶金鑰：

1. 取得現有的儲存體帳戶。
1. 擷取現有的 key vault。
1. 將受管理的金鑰保存庫儲存體帳戶新增至保存庫。 設定`key1`為作用中金鑰重新產生期間為 180 天。
1. 使用`key1`設定指定的儲存體帳戶的儲存體內容。

> [!NOTE]
> 以服務的金鑰保存庫指派操作員權限，您的儲存體帳戶。
> 
> 您設定受管理的 Azure Key Vault 儲存體帳戶金鑰之後，只使用金鑰保存庫之後變更金鑰。
> 對於受管理的儲存體帳戶金鑰，Key Vault 會管理儲存體帳戶金鑰的輪替。

1. 建立儲存體帳戶之後，執行下列命令來取得儲存體帳戶的資源識別碼，來管理：
    ```
    az storage account show -n storageaccountname
    ```

    複製命令輸出中的資源 ID 值：
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    範例輸出︰
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. 儲存體帳戶金鑰操作員服務角色 」 將 RBAC 角色指派至金鑰保存庫。 此角色會限制您的儲存體帳戶的存取範圍。 對於傳統儲存體帳戶，使用 傳統儲存體帳戶金鑰操作員服務角色 」 角色。

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` 為 Azure 公用雲端中的金鑰保存庫中的物件識別碼。 若要在 Azure Government 雲端中取得金鑰保存庫的物件識別碼，請參閱[服務主體應用程式識別碼](#service-principal-application-id)。
    
1. 建立金鑰保存庫管理儲存體帳戶：

    設定重新產生期間的 90 天。 90 天之後，Key Vault 會重新產生`key1`及交換的作用中的索引鍵，從`key2`至`key1`。 `key1` 然後標示為作用中金鑰。 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>建立並產生權杖

您也可以要求 Key Vault 來產生共用的存取簽章權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以授與用戶端存取資源在儲存體帳戶而不必共用帳戶金鑰。 共用的存取簽章為您提供安全的方式來共用儲存體資源，而不會危害您的帳戶金鑰。

在本節中的命令會完成下列動作：

- 設定帳戶共用存取簽章定義`<YourSASDefinitionName>`。 定義受管理的金鑰保存庫儲存體帳戶上設定`<YourStorageAccountName>`金鑰保存庫`<VaultName>`。
- 建立 Blob、 檔案、 資料表和佇列服務的帳戶共用的存取簽章權杖。 服務、 容器和物件，會建立資源類型的語彙基元。 所有的權限，透過 https，與指定的開始和結束日期，會建立權杖。
- 設定保存庫的受管理的金鑰保存庫儲存體共用存取簽章定義。 定義具有範本所建立的共用的存取簽章權杖的 URI。 定義具有共用的存取簽章類型`account`，有效期為 N 天前發生。
- 擷取對應至共用的存取簽章定義金鑰保存庫密碼的實際存取權杖。

完成上一節中的步驟之後，執行下列命令，以要求 Key Vault 來產生共用的存取簽章權杖。 

1. 建立共用的存取簽章定義。 建立共用的存取簽章定義之後，要求以產生更多共用的存取簽章權杖的金鑰保存庫。 這項作業需要`storage`和`setsas`權限。
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    如需有關作業的說明，請參閱[az 儲存體帳戶產生 sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)參考文件。

    作業成功執行之後，複製的輸出。
    ```console
       "se=2020-01-01&sp=***"
    ```

1. 使用`$sasToken`先前命令所產生，並建立共用的存取簽章定義。 如需命令參數的詳細資訊，請參閱[az keyvault 儲存體 sas 定義建立](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)參考文件。
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    當使用者不需要儲存體帳戶的權限時，第一次收到使用者的物件識別碼：
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>擷取程式碼中的權杖

執行您的儲存體帳戶上的作業擷取[共用存取簽章權杖](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)從金鑰保存庫。

有三種方式可向 Key Vault:

- 使用受控的服務識別。 強烈建議這種方法。
- 使用服務主體和憑證。 
- 使用服務主體和密碼。 不建議這種方法。

如需詳細資訊，請參閱[Azure 金鑰保存庫：基本概念](key-vault-whatis.md#basic-concepts)。

下列範例示範如何擷取共用的存取簽章權杖。 建立共用的存取簽章定義之後，您會擷取權杖。 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

如果您的共用的存取簽章權杖即將到期，再次從 Key Vault 擷取共用的存取簽章權杖，並更新程式碼。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI 命令

與受管理的儲存體帳戶的 Azure CLI 命令的相關資訊，請參閱[az keyvault 儲存體](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)參考文件。

## <a name="next-steps"></a>後續步驟

- 深入了解[金鑰、 祕密和憑證](https://docs.microsoft.com/rest/api/keyvault/)。
- 檢閱文件上[Azure Key Vault 小組部落格](https://blogs.technet.microsoft.com/kv/)。
