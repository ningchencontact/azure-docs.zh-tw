---
title: 使用 .NET （預覽）建立容器或 blob 的使用者委派 SAS-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫在 Azure 儲存體中 Azure Active Directory 認證來建立使用者委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 59de768e75a88d7cfa5b68fa306d0e83f1aa0ba3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671335"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>使用 .NET 建立容器或 blob 的使用者委派 SAS （預覽）

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文說明如何使用 Azure Active Directory （Azure AD）認證，利用[適用于 .net 的 Azure 儲存體用戶端程式庫](https://www.nuget.org/packages/Azure.Storage.Blobs)來建立容器或 blob 的使用者委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>安裝預覽套件

本文中的範例會使用適用于 Blob 儲存體的 Azure 儲存體用戶端程式庫的最新預覽版本。 若要安裝預覽套件，請從 NuGet 套件管理員主控台執行下列命令：

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

本文中的範例也會使用適用于 .NET 的 Azure 身分[識別用戶端程式庫](https://www.nuget.org/packages/Azure.Identity/)的最新預覽版本，以 Azure AD 認證進行驗證。 Azure 身分識別用戶端程式庫會驗證安全性主體。 然後，已驗證的安全性主體就可以建立使用者委派 SAS。 如需 Azure 身分識別用戶端程式庫的詳細資訊，請參閱[適用于 .net 的 azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>建立服務主體

若要透過 Azure 身分識別用戶端程式庫使用 Azure AD 認證進行驗證，請根據您程式碼的執行位置，使用服務主體或受控識別做為安全性主體。 如果您的程式碼是在開發環境中執行，請將服務主體用於測試用途。 如果您的程式碼正在 Azure 中執行，請使用受控識別。 本文假設您是從開發環境執行程式碼，並顯示如何使用服務主體來建立使用者委派 SAS。

若要使用 Azure CLI 建立服務主體並指派 RBAC 角色，請呼叫[az ad sp create for rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)命令。 提供 Azure 儲存體資料存取角色，以指派給新的服務主體。 角色必須包含**storageAccounts/blobServices/generateUserDelegationKey**動作。 如需有關 Azure 儲存體提供之內建角色的詳細資訊，請參閱[Azure 資源的內建角色](../../role-based-access-control/built-in-roles.md)。

此外，請提供角色指派的範圍。 服務主體會建立使用者委派金鑰，這是在儲存體帳戶層級執行的作業，因此角色指派的範圍應設定為儲存體帳戶、資源群組或訂用帳戶的層級。 如需建立使用者委派 SAS 之 RBAC 許可權的詳細資訊，請參閱[建立使用者委派 sas （REST API）](/rest/api/storageservices/create-user-delegation-sas)中的**使用 rbac 指派許可權**一節。

如果您沒有足夠的許可權可將角色指派給服務主體，您可能需要要求帳戶擁有者或系統管理員執行角色指派。

下列範例會使用 Azure CLI 建立新的服務主體，並使用帳戶範圍將**儲存體 Blob 資料讀取器**角色指派給它

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

@No__t-0 命令會以 JSON 格式傳回服務主體屬性的清單。 複製這些值，讓您可以在下一個步驟中使用它們來建立必要的環境變數。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> RBAC 角色指派可能需要幾分鐘的時間才能傳播。

## <a name="set-environment-variables"></a>設定環境變數

Azure 身分識別用戶端程式庫會在執行時間讀取來自三個環境變數的值，以驗證服務主體。 下表描述要針對每個環境變數設定的值。

|環境變數|值
|-|-
|`AZURE_CLIENT_ID`|服務主體的應用程式識別碼
|`AZURE_TENANT_ID`|服務主體的 Azure AD 租使用者識別碼
|`AZURE_CLIENT_SECRET`|為服務主體產生的密碼

> [!IMPORTANT]
> 設定環境變數之後，請關閉並重新開啟主控台視窗。 如果您使用 Visual Studio 或其他開發環境，您可能需要重新開機開發環境，才能讓它註冊新的環境變數。

## <a name="add-using-directives"></a>新增 using 指示詞

將下列 `using` 指示詞新增至您的程式碼，以使用 Azure 身分識別和 Azure 儲存體用戶端程式庫的預覽版本。

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

## <a name="authenticate-the-service-principal"></a>驗證服務主體

若要驗證服務主體，請建立[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)類別的實例。 @No__t-0 的函數會讀取您先前建立的環境變數。

下列程式碼片段顯示如何取得已驗證的認證，並使用它來建立 Blob 儲存體的服務用戶端

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

## <a name="see-also"></a>另請參閱

- [取得使用者委派金鑰作業](/rest/api/storageservices/get-user-delegation-key)
- [建立使用者委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
