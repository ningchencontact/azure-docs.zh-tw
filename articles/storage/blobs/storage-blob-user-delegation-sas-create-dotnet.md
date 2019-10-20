---
title: 使用 .NET （預覽）建立容器或 blob 的使用者委派 SAS-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫在 Azure 儲存體中 Azure Active Directory 認證來建立使用者委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595220"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>使用 .NET 建立容器或 blob 的使用者委派 SAS （預覽）

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文說明如何使用 Azure Active Directory （Azure AD）認證，利用適用于 .NET 的 Azure 儲存體用戶端程式庫來建立容器或 blob 的使用者委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>使用 Azure 身分識別程式庫（預覽）進行驗證

適用于 .NET 的 Azure 身分識別用戶端程式庫（預覽）會驗證安全性主體。 當您的程式碼在 Azure 中執行時，安全性主體是適用于 Azure 資源的受控識別。

當您的程式碼在開發環境中執行時，可能會自動處理驗證，或視您使用的工具而定，可能需要瀏覽器登入。 Microsoft Visual Studio 支援單一登入（SSO），讓作用中的 Azure AD 使用者帳戶自動用於驗證。 如需 SSO 的詳細資訊，請參閱[單一登入應用程式](../../active-directory/manage-apps/what-is-single-sign-on.md)。

其他開發工具可能會提示您透過網頁瀏覽器登入。 您也可以使用服務主體，從開發環境進行驗證。 如需詳細資訊，請參閱[在入口網站中建立 Azure 應用程式](../../active-directory/develop/howto-create-service-principal-portal.md)的身分識別。

驗證之後，Azure 身分識別用戶端程式庫會取得權杖認證。 然後，此權杖認證會封裝在您建立來對 Azure 儲存體執行作業的服務用戶端物件中。 程式庫會藉由取得適當的權杖認證，順暢地為您處理這項工作。

如需 Azure 身分識別用戶端程式庫的詳細資訊，請參閱[適用于 .net 的 azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。

## <a name="assign-rbac-roles-for-access-to-data"></a>指派 RBAC 角色以存取資料

當 Azure AD 安全性主體嘗試存取 blob 資料時，該安全性主體必須擁有該資源的許可權。 無論安全性主體是 Azure 中的受控識別，或是在開發環境中執行程式碼的 Azure AD 使用者帳戶，都必須將 RBAC 角色指派給安全性主體，以授與 Azure 儲存體中 blob 資料的存取權。 如需透過 RBAC 指派許可權的相關資訊，請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)中的

## <a name="install-the-preview-packages"></a>安裝預覽套件

本文中的範例會使用[適用于 Blob 儲存體的 Azure 儲存體用戶端程式庫](https://www.nuget.org/packages/Azure.Storage.Blobs)的最新預覽版本。 若要安裝預覽套件，請從 NuGet 套件管理員主控台執行下列命令：

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

本文中的範例也會使用適用于 .NET 的 Azure 身分[識別用戶端程式庫](https://www.nuget.org/packages/Azure.Identity/)的最新預覽版本，以 Azure AD 認證進行驗證。 若要安裝預覽套件，請從 NuGet 套件管理員主控台執行下列命令：

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>新增 using 指示詞

將下列 `using` 指示詞新增至您的程式碼，以使用 Azure 身分識別的預覽版本和 Azure 儲存體用戶端程式庫。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>取得已驗證的權杖認證

若要取得權杖認證，讓您的程式碼可以用來授權 Azure 儲存體的要求，請建立[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)類別的實例。

下列程式碼片段顯示如何取得已驗證的權杖認證，並使用它來建立 Blob 儲存體的服務用戶端：

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>取得使用者委派金鑰

每個 SAS 都會以金鑰簽署。 若要建立使用者委派 SAS，您必須先要求使用者委派金鑰，然後用它來簽署 SAS。 使用者委派金鑰類似于用來簽署服務 SAS 或帳戶 SAS 的帳戶金鑰，不同之處在于它會依賴您的 Azure AD 認證。 當用戶端要求使用 OAuth 2.0 權杖的使用者委派金鑰時，Azure 儲存體會代表使用者傳回使用者委派金鑰。

擁有使用者委派金鑰後，您就可以使用該金鑰在金鑰存留期內建立任意數目的使用者委派共用存取簽章。 使用者委派金鑰與用來取得它的 OAuth 2.0 權杖無關，因此只要金鑰仍然有效，就不需要更新權杖。 您可以指定金鑰有效期限最多7天。

使用下列其中一種方法來要求使用者委派金鑰：

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

下列程式碼片段會取得使用者委派金鑰，並寫出其屬性：

```csharp
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>建立 SAS 權杖

下列程式碼片段示範如何建立新的[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) ，並提供使用者委派 SAS 的參數。 然後，程式碼片段會呼叫[ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters)以取得 SAS 權杖字串。 最後，此程式碼會建立完整的 URI，包括資源位址和 SAS 權杖。

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>範例：取得使用者委派 SAS

下列範例方法顯示驗證安全性主體和建立使用者委派 SAS 的完整程式碼：

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>範例：讀取具有使用者委派 SAS 的 blob

下列範例會測試在上一個範例中，從模擬的用戶端應用程式建立的使用者委派 SAS。 如果 SAS 有效，用戶端應用程式就能夠讀取 blob 的內容。 如果 SAS 無效（例如，如果它已過期），Azure 儲存體會傳回錯誤碼403（禁止）。

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (StorageRequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>請參閱

- [取得使用者委派金鑰作業](/rest/api/storageservices/get-user-delegation-key)
- [建立使用者委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
