---
title: 驗證適用於 Azure 資源-Azure 儲存體 blob 和佇列管理的身分識別與存取 |Microsoft Docs
description: Azure Blob 和佇列儲存體支援 Azure 資源管理的身分識別與 Azure Active Directory 驗證。 您可以使用 Azure 資源的受控識別，從執行於 Azure 虛擬機器、函式應用程式和虛擬機器擴展集等項目中的應用程式，驗證 Blob 和佇列的存取權。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4245c44ceaf907512187d7db4a9d6f087a855f70
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507882"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>驗證適用於 Azure 資源的存取權的 blob 和佇列與 Azure Active Directory 與受管理的身分識別

Azure Blob 和佇列儲存體支援使用 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)來進行 Azure Active Directory (Azure AD) 驗證。 管理身分識別，用於 Azure 資源可以授權存取 blob 和佇列資料，使用從 Azure 虛擬機器 (Vm)、 函式應用程式、 虛擬機器擴展集和其他服務中執行的應用程式的 Azure AD 認證。 藉由使用 Azure AD 驗證搭配 Azure 資源管理的身分識別，您可以避免儲存認證與您在雲端中執行的應用程式。  

本文說明如何從 Azure VM 授權管理的身分識別的 blob 或佇列資料的存取。 

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別

您可以使用適用於 Azure 資源管理的身分識別來授與對 blob 和佇列的存取權，從您的 VM 之前，您必須先啟用受管理的身分識別，在 VM 上的 Azure 資源。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 入口網站](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 範本](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>授與權限的受管理的 Azure AD 身分識別

若要授權給 Blob 或佇列服務的要求，從受管理的身分識別，在您的 Azure 儲存體應用程式中，先設定受管理身分識別的角色型存取控制 (RBAC) 設定。 Azure 儲存體定義 RBAC 角色，其中包含 blob 和佇列資料的權限。 RBAC 角色指派給受控身分識別，當受管理的身分識別授與適當的範圍內的 blob 或佇列資料的這些權限。 

如需指派 RBAC 角色的詳細資訊，請參閱下列文章：

- [Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中授與存取權](storage-auth-aad-rbac-portal.md)
- [授與存取權與使用 Azure CLI 的 RBAC 的 Azure blob 和佇列資料](storage-auth-aad-rbac-cli.md)
- [使用 RBAC，使用 PowerShell 的 Azure blob 和佇列資料的授與存取權](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>使用受控身分識別的存取權杖授權

若要授權對 Blob 和佇列儲存體與受控身分識別的要求，您的應用程式或指令碼必須取得 OAuth 權杖。 [Microsoft Azure 應用程式驗證](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)適用於.NET （預覽） 的用戶端程式庫會簡化取得和更新您的程式碼的語彙基元的程序。

應用程式驗證用戶端程式庫會自動管理驗證。 程式庫會使用開發人員的認證，在本機開發期間進行驗證。 在本機開發期間使用開發人員認證會比較安全，因為您不需要建立 Azure AD 認證，或在開發人員之間共用認證。 當解決方案在稍後部署至 Azure 中時，程式庫會自動切換成使用應用程式認證。

若要使用 Azure 儲存體應用程式中的應用程式驗證程式庫，安裝最新的預覽套件，從[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)，以及最新版[適用於.NET 的 Azure 儲存體用戶端程式庫](https://www.nuget.org/packages/WindowsAzure.Storage/)。 新增下列**使用**陳述式，以您的程式碼：

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

應用程式驗證程式庫會提供**azureservicetokenprovider 會**類別。 此類別的執行個體可以傳遞至回呼取得的權杖，然後在到期前更新權杖。

下列範例會取得語彙基元並使用它來建立新的 blob，然後會使用相同的權杖來讀取 blob。

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

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
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

回呼方法會檢查權杖的到期時間，並視需要更新它：

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
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

如需詳細的應用程式驗證程式庫的詳細資訊，請參閱[服務對服務驗證，以使用.NET 的 Azure 金鑰保存庫](../../key-vault/service-to-service-authentication.md)。 

若要深入了解如何取得存取權杖，請參閱[如何使用適用於 Azure VM 上的 Azure 資源的受管理的身分識別取得存取權杖](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

> [!NOTE]
> 若要授權對與 Azure AD 的 blob 或佇列資料的要求，您必須使用 HTTPS，這些要求。

## <a name="next-steps"></a>後續步驟

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[使用 RBAC 管理存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與儲存體應用程式](storage-auth-aad-app.md)。
- 若要了解如何以 Azure AD 認證來執行 Azure CLI 和 PowerShell 命令，請參閱[執行 Azure CLI 或 PowerShell 命令與 Azure AD 認證來存取 blob 或佇列資料](storage-auth-aad-script.md)。