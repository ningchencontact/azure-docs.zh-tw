---
title: 使用適用于 Azure 資源的 Azure Active Directory 和受控識別, 授權對 blob 和佇列的存取-Azure 儲存體
description: Azure Blob 和佇列儲存體支援使用 Azure 資源的 Azure Active Directory 和受控識別來授權資源的存取權。 您可以使用 Azure 資源的受控識別, 從 Azure 虛擬機器中執行的應用程式、函式應用程式、虛擬機器擴展集和其他專案, 授權存取 blob 和佇列。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2d0697567bfd2fcf95a1fe6ebf246646af5650c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564896"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>使用適用于 Azure 資源的 Azure Active Directory 和受控識別, 授權對 blob 和佇列的存取

Azure Blob 和佇列儲存體支援使用 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)來進行 Azure Active Directory (Azure AD) 驗證。 適用于 Azure 資源的受控識別可以使用 Azure 虛擬機器 (Vm) 中執行的應用程式、函式應用程式、虛擬機器擴展集和其他服務的 Azure AD 認證, 來授權 blob 和佇列資料的存取權。 藉由使用適用于 Azure 資源的受控識別搭配 Azure AD authentication, 您可以避免將認證儲存在雲端中執行的應用程式。  

本文說明如何使用 Azure VM 的受控識別來授權 blob 或佇列資料的存取權。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別

您必須先在 VM 上啟用 Azure 資源的受控識別, 才可以使用 Azure 資源的受控識別來授與 VM 的存取權。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 入口網站](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 範本](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 用戶端程式庫](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>將許可權授與 Azure AD 受控識別

若要從您的 Azure 儲存體應用程式中的受控識別授權對 Blob 或佇列服務的要求, 請先為該受控識別設定角色型存取控制 (RBAC) 設定。 Azure 儲存體會定義包含 blob 和佇列資料之許可權的 RBAC 角色。 當 RBAC 角色指派給受控識別時, 受控識別會被授與這些許可權給適當範圍的 blob 或佇列資料。

如需指派 RBAC 角色的詳細資訊, 請參閱下列其中一篇文章:

- [在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-portal.md)
- [透過 Azure CLI 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-cli.md)
- [透過 PowerShell 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Azure 儲存體資源識別碼

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

此程式碼範例示範如何從 Azure AD 取得 OAuth 2.0 權杖, 並使用它來授權建立區塊 blob 的要求。 若要讓此範例運作, 請先遵循前面各節中所述的步驟。

適用于 .NET 的[Microsoft Azure 應用程式驗證](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)用戶端程式庫 (預覽) 可簡化從程式碼取得和更新權杖的流程。 應用程式驗證用戶端程式庫會自動管理驗證。 程式庫會使用開發人員的認證, 在本機開發期間進行驗證。 在本機開發期間使用開發人員認證會比較安全，因為您不需要建立 Azure AD 認證，或在開發人員之間共用認證。 當解決方案稍後部署至 Azure 時, 程式庫會自動切換為使用應用程式認證。

### <a name="install-packages"></a>安裝套件

若要在 Azure 儲存體應用程式中使用應用程式驗證程式庫, 請從[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)安裝最新的預覽套件, 以及適用于[.net 的 Azure 儲存體常見用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)和[Azure Blob 儲存體用戶端程式庫的最新版本適用于 .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)。 將下列**using**語句新增至您的程式碼:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

### <a name="add-the-callback-method"></a>新增回呼方法

回呼方法會檢查權杖的到期時間, 並視需要進行更新:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>取得權杖並建立區塊 blob

應用程式驗證程式庫提供**azureservicetokenprovider 會**類別。 這個類別的實例可以傳遞至回呼, 以取得權杖, 然後在權杖到期之前更新它。

下列範例會取得權杖, 並使用它來建立新的 blob, 然後使用相同的 token 來讀取 blob。

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

如需有關應用程式驗證程式庫的詳細資訊, 請參閱[使用 .net Azure Key Vault 的服務對服務驗證](../../key-vault/service-to-service-authentication.md)。

若要深入瞭解如何取得存取權杖, 請參閱[如何在 AZURE VM 上使用適用于 azure 資源的受控識別來取得存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

> [!NOTE]
> 若要使用 Azure AD 來授權對 blob 或佇列資料的要求, 您必須針對這些要求使用 HTTPS。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 Azure 儲存體的 RBAC 角色, 請參閱[使用 Rbac 管理儲存體資料的存取權限](storage-auth-aad-rbac.md)。
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與儲存體應用程式](storage-auth-aad-app.md)。
- 若要瞭解如何使用 Azure AD 認證來執行 Azure CLI 和 PowerShell 命令, 請參閱[使用 Azure AD 認證來執行 Azure CLI 或 powershell 命令, 以存取 blob 或佇列資料](storage-auth-aad-script.md)。
