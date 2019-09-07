---
title: 使用 Azure Key Vault 和 Azure CLI 管理儲存體帳戶金鑰
description: 儲存體帳戶金鑰可提供對 Azure 儲存體帳戶 Azure Key Vault 和金鑰型存取之間的緊密整合。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 251a7c21b671052a23f6ee18cb4278737464b25c
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744880"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>使用 Azure Key Vault 和 Azure CLI 管理儲存體帳戶金鑰 

Azure Key Vault 管理 Azure 儲存體帳戶和傳統儲存體帳戶的金鑰。 您可以使用 Key Vault 受控儲存體帳戶 功能來為您完成數個金鑰管理功能。

[Azure 儲存體帳戶](/azure/storage/storage-create-storage-account)會使用包含帳戶名稱和金鑰的認證。 金鑰會自動產生並作為密碼，而不是做為加密金鑰。 Key Vault 會將儲存體帳戶金鑰儲存為[Key Vault 秘密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)來加以管理。 金鑰會與 Azure 儲存體帳戶一起列出（已同步處理），並會定期重新產生或_輪替_。 

當您使用受控儲存體帳戶金鑰功能時，請考慮下列幾點：

- 回應呼叫端時，絕對不會傳回索引鍵值。
- 只有 Key Vault 應該管理您的儲存體帳戶金鑰。 請勿自行管理金鑰，並避免干擾 Key Vault 進程。
- 只有單一 Key Vault 物件應該管理儲存體帳戶金鑰。 不允許從多個物件進行金鑰管理。
- 您可以要求 Key Vault 來管理具有使用者主體的儲存體帳戶，但不能使用服務主體。
- 僅使用 Key Vault 重新產生金鑰。 請勿手動重新產生儲存體帳戶金鑰。 

> [!NOTE]
> 與 Azure Active Directory （Azure AD）的 Azure 儲存體整合是 Microsoft 的雲端式身分識別和存取管理服務。
> 適用于[Azure blob 和佇列](../storage/common/storage-auth-aad.md)的 Azure AD 整合。
> 使用 Azure AD 進行驗證和授權。
> Azure AD 提供以權杖為基礎的 OAuth2 存取，Azure 儲存體就像 Azure Key Vault。
>
> Azure AD 可讓您使用應用程式或使用者識別，而不是儲存體帳號憑證來驗證用戶端應用程式。
> 當您在 Azure 上執行時，可以使用[Azure AD 受控識別](/azure/active-directory/managed-identities-azure-resources/)。 受控識別會移除用戶端驗證的需求，並將認證儲存在或應用程式中。
> Azure AD 會使用角色型存取控制（RBAC）來管理授權，Key Vault 也支援這種方式。

### <a name="service-principal-application-id"></a>服務主體應用程式識別碼

Azure AD 租使用者會提供每個已註冊的應用程式與[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服務主體會作為應用程式識別碼（ID）。 在授權設定期間，會使用應用程式識別碼，以透過 RBAC 存取其他 Azure 資源。

Key Vault 是在所有 Azure AD 租使用者中預先註冊的 Microsoft 應用程式。 Key Vault 會在相同的應用程式識別碼和每個 Azure 雲端內註冊。

| 租用戶 | 雲端 | 應用程式識別碼 |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>必要條件

使用 Key Vault 來管理儲存體帳戶金鑰之前，請先參閱必要條件：

- 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。
- 建立[Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)。 請遵循下列[步驟](../storage/index.yml)。
- 儲存體帳戶名稱必須僅使用小寫字母和數位。 名稱的長度必須介於3到24個字元之間。        
      
## <a name="manage-storage-account-keys"></a>管理儲存體帳戶金鑰

使用 Key Vault 來管理儲存體帳戶金鑰有四個基本步驟：

1. 取得現有的儲存體帳戶。
1. 提取現有的金鑰保存庫。
1. 將 Key Vault 受控儲存體帳戶新增至保存庫。 設定`key1`為使用中金鑰，且重新產生期間為90天。
1. 用`key1`來設定指定之儲存體帳戶的儲存體內容。

> [!NOTE]
> Key Vault 即服務指派給儲存體帳戶的操作員許可權。
> 
> 在您設定 Azure Key Vault 受控儲存體帳戶金鑰之後，請只使用 Key Vault 變更金鑰。
> 針對受控儲存體帳戶金鑰，Key Vault 會管理儲存體帳戶金鑰的輪替。

1. 建立儲存體帳戶之後，請執行下列命令來取得要管理之儲存體帳戶的資源識別碼：
    ```
    az storage account show -n storageaccountname
    ```

    從命令輸出複製資源識別碼值：
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    範例輸出︰
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. 將「儲存體帳戶金鑰操作員服務角色」 RBAC 角色指派給 Key Vault。 此角色會將存取範圍限制為您的儲存體帳戶。 若為傳統儲存體帳戶，請使用「傳統儲存體帳戶金鑰操作員服務角色」角色。

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`是 Azure 公用雲端中 Key Vault 的物件識別碼。 若要取得 Azure Government 雲端中 Key Vault 的物件識別碼，請參閱[服務主體應用程式識別碼](#service-principal-application-id)。
    
1. 建立 Key Vault 受控儲存體帳戶：

    設定90天的重新產生期間。 90天后，Key Vault `key1`重新產生，並將作用中的金鑰從`key2`交換到`key1`。 `key1`接著會標示為使用中的索引鍵。 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>建立並產生權杖

您也可以要求 Key Vault 產生共用存取簽章權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以對用戶端授與儲存體帳戶中資源的存取權，而不需要共用您的帳戶金鑰。 共用存取簽章可讓您以安全的方式共用儲存體資源，而不會危害您的帳戶金鑰。

本節中的命令會完成下列動作：

- 設定帳戶共用存取簽章定義`<YourSASDefinitionName>`。 定義會在金鑰保存庫`<YourStorageAccountName>` `<VaultName>`中的 Key Vault 受控儲存體帳戶上設定。
- 為 Blob、檔案、資料表和佇列服務建立帳戶共用存取簽章權杖。 權杖是針對資源類型服務、容器和物件所建立。 權杖是以擁有權限（透過 HTTPs）和指定的開始和結束日期來建立。
- 在保存庫中設定 Key Vault 管理的儲存體共用存取簽章定義。 定義具有已建立的共用存取簽章權杖的範本 URI。 定義具有共用存取簽章類型`account` ，且有效期為 N 天。
- 從對應至共用存取簽章定義的 Key Vault 密碼中，取出實際的存取權杖。

完成上一節中的步驟之後，請執行下列命令來要求 Key Vault 產生共用存取簽章權杖。 

1. 建立共用存取簽章定義。 建立共用存取簽章定義之後，請 Key Vault，以產生更多的共用存取簽章權杖。 這種作業需要`storage`和`setsas`許可權。
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    如需作業的說明，請參閱[az storage account 產生-sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)參考檔。

    作業執行成功之後，請複製輸出。
    ```console
       "se=2020-01-01&sp=***"
    ```

1. 使用上`$sasToken`一個命令所產生的，並建立共用存取簽章定義。 如需命令參數的詳細資訊，請參閱[az keyvault storage sas-definition create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) reference 檔。
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    當使用者沒有儲存體帳戶的許可權時，請先取得使用者的物件識別碼：
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>在程式碼中提取權杖

藉由從 Key Vault 提取[共用存取](../storage/common/storage-dotnet-shared-access-signature-part-1.md)簽章權杖，在您的儲存體帳戶上執行作業。

有三種方式可向 Key Vault 進行驗證：

- 使用受控服務識別。 強烈建議使用此方法。
- 使用服務主體和憑證。 
- 使用服務主體和密碼。 不建議使用此方法。

如需詳細資訊， [請參閱 Azure Key Vault：基本概念](key-vault-whatis.md#basic-concepts)。

下列範例示範如何提取共用存取簽章權杖。 建立共用存取簽章定義之後，您會提取權杖。 

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

如果您的共用存取簽章權杖即將到期，請從 Key Vault 中再次提取共用存取簽章權杖，並更新程式碼。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI 命令

如需與受管理的儲存體帳戶相關之 Azure CLI 命令的詳細資訊，請參閱[az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) reference 檔 \ （英文 \）。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[金鑰、秘密和憑證](https://docs.microsoft.com/rest/api/keyvault/)。
- 請參閱[Azure Key Vault 小組 blog](https://blogs.technet.microsoft.com/kv/)的文章。
