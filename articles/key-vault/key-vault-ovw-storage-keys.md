---
ms.assetid: ''
title: Azure Key Vault 儲存體帳戶金鑰
description: 儲存體帳戶金鑰提供 Azure Key Vault 與 Azure 儲存體帳戶的金鑰型存取之間的完美整合。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 02fffe7c4a3acff6ce6d68046eee4286003b1766
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232217"
---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault 儲存體帳戶金鑰

> [!NOTE]
> [Azure 儲存體現在支援 AAD 授權](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。 我們建議使用 Azure Active Directory 進行儲存體的驗證和授權，因為使用者不必擔心其儲存體帳戶金鑰的輪替。

- Azure Key Vault 會管理 Azure 儲存體帳戶 (ASA) 的金鑰。
    - Azure Key Vault 可在內部列出 (同步) Azure 儲存體帳戶金鑰。    
    - Azure Key Vault 會定期重新產生 (輪替) 金鑰。
    - 永遠不會傳回金鑰值以回應呼叫者。
    - Azure Key Vault 會管理儲存體帳戶和傳統儲存體帳戶的金鑰。

<a name="prerequisites"></a>必要條件
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 安裝 Azure CLI   
2. [建立儲存體帳戶](https://azure.microsoft.com/services/storage/)
    - 請依照本[文件](https://docs.microsoft.com/azure/storage/)中的步驟來建立儲存體帳戶  
    - **命名指導方針：** 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。        
      
<a name="step-by-step-instructions"></a>逐步指示
-------------------------
在下面的指示中，我們會將 Key Vault 指派為服務，以便擁有儲存體帳戶的操作員權限

1. 建立儲存體帳戶之後，執行下列命令來取得您想要管理的儲存體帳戶的資源識別碼

    ```
    az storage account show -n storageaccountname (Copy ID out of the result of this command)
    ```
    
2. 取得 Azure Key Vault 服務主體的應用程式識別碼 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. 將儲存體金鑰操作員角色指派給 Azure Key Vault 身分識別

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id hhjkh --scope idofthestorageaccount
    ```
    
4. 建立 Key Vault 受控儲存體帳戶。     <br /><br />
   下面我們會設定 90 天的重新產生期間。 90 天之後，Key Vault 會重新產生 'key1'，並將作用中的金鑰從 'key2' 交換為 'key1'。
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    如果使用者未建立儲存體帳戶且沒有儲存體帳戶的權限，下列步驟會為您的帳戶設定權限，確保您可以管理 Key Vault 中的所有儲存體權限。
 > [!NOTE] 
    在使用者沒有儲存體帳戶權限的情況下，我們會先取得使用者的物件識別碼

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

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
