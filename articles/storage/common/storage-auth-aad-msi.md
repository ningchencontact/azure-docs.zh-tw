---
title: 使用適用于 Azure 資源的 Azure Active Directory 和受控識別，授權對 blob 和佇列的存取-Azure 儲存體
description: Azure Blob 和佇列儲存體支援使用 Azure 資源的 Azure Active Directory 和受控識別來授權資源的存取權。 您可以使用 Azure 資源的受控識別，從 Azure 虛擬機器中執行的應用程式、函式應用程式、虛擬機器擴展集和其他專案，授權存取 blob 和佇列。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 833aa7dcce5c429b3005a378e93e2177df1eb0d4
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595189"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>使用適用于 Azure 資源的 Azure Active Directory 和受控識別，授權對 blob 和佇列的存取

Azure Blob 和佇列儲存體支援使用 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)來進行 Azure Active Directory (Azure AD) 驗證。 適用于 Azure 資源的受控識別可以使用 Azure 虛擬機器（Vm）中執行的應用程式、函式應用程式、虛擬機器擴展集和其他服務的 Azure AD 認證，來授權 blob 和佇列資料的存取權。 藉由使用適用于 Azure 資源的受控識別搭配 Azure AD authentication，您可以避免將認證儲存在雲端中執行的應用程式。  

本文說明如何使用 Azure 資源的受控識別，來授權從 Azure VM 存取 blob 或佇列資料。 它也會說明如何在開發環境中測試您的程式碼。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上啟用受控識別

您必須先在 VM 上啟用 Azure 資源的受控識別，才可以使用 Azure 資源的受控識別來授與 VM 的存取權。 若要了解如何啟用 Azure 資源的受控識別，請參閱下列其中一篇文章：

- [Azure 入口網站](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 範本](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 用戶端程式庫](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

如需受控識別的詳細資訊，請參閱[適用于 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md)識別。

## <a name="authenticate-with-the-azure-identity-library-preview"></a>使用 Azure 身分識別程式庫（預覽）進行驗證

適用于 .NET 的 Azure 身分識別用戶端程式庫（預覽）會驗證安全性主體。 當您的程式碼在 Azure 中執行時，安全性主體是適用于 Azure 資源的受控識別。

當您的程式碼在開發環境中執行時，可能會自動處理驗證，或視您使用的工具而定，可能需要瀏覽器登入。 Microsoft Visual Studio 支援單一登入（SSO），讓作用中的 Azure AD 使用者帳戶自動用於驗證。 如需 SSO 的詳細資訊，請參閱[單一登入應用程式](../../active-directory/manage-apps/what-is-single-sign-on.md)。

其他開發工具可能會提示您透過網頁瀏覽器登入。 您也可以使用服務主體，從開發環境進行驗證。 如需詳細資訊，請參閱[在入口網站中建立 Azure 應用程式](../../active-directory/develop/howto-create-service-principal-portal.md)的身分識別。

驗證之後，Azure 身分識別用戶端程式庫會取得權杖認證。 然後，此權杖認證會封裝在您建立來對 Azure 儲存體執行作業的服務用戶端物件中。 程式庫會藉由取得適當的權杖認證，順暢地為您處理這項工作。

如需 Azure 身分識別用戶端程式庫的詳細資訊，請參閱[適用于 .net 的 azure 身分識別用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。

## <a name="assign-rbac-roles-for-access-to-data"></a>指派 RBAC 角色以存取資料

當 Azure AD 安全性主體嘗試存取 blob 或佇列資料時，該安全性主體必須擁有該資源的許可權。 無論安全性主體是 Azure 中的受控識別，或是在開發環境中執行程式碼的 Azure AD 使用者帳戶，都必須將 RBAC 角色指派給安全性主體，以授與 Azure 儲存體中 blob 或佇列資料的存取權。 如需透過 RBAC 指派許可權的相關資訊，請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)中的

## <a name="install-the-preview-packages"></a>安裝預覽套件

本文中的範例會使用適用于 Blob 儲存體的 Azure 儲存體用戶端程式庫的最新預覽版本。 若要安裝預覽套件，請從 NuGet 套件管理員主控台執行下列命令：

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

本文中的範例也會使用適用于 .NET 的 Azure 身分[識別用戶端程式庫](https://www.nuget.org/packages/Azure.Identity/)的最新預覽版本，以 Azure AD 認證進行驗證。 若要安裝預覽套件，請從 NuGet 套件管理員主控台執行下列命令：

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

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

若要取得權杖認證，讓您的程式碼可以用來授權 Azure 儲存體的要求，請建立[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)類別的實例。 下列程式碼範例示範如何取得已驗證的權杖認證，並使用它來建立服務用戶端物件，然後使用服務用戶端來上傳新的 blob：

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> 若要使用 Azure AD 來授權對 blob 或佇列資料的要求，您必須針對這些要求使用 HTTPS。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 Azure 儲存體的 RBAC 角色，請參閱[使用 Rbac 管理儲存體資料的存取權限](storage-auth-aad-rbac.md)。
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與儲存體應用程式](storage-auth-aad-app.md)。
- 若要瞭解如何使用 Azure AD 認證來執行 Azure CLI 和 PowerShell 命令，請參閱[使用 Azure AD 認證來執行 Azure CLI 或 powershell 命令，以存取 blob 或佇列資料](storage-auth-aad-script.md)。
