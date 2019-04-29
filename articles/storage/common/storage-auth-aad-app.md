---
title: 以 Azure Active Directory 驗證來存取 blob 和佇列資料從您的應用程式 |Microsoft Docs
description: 若要從驗證的應用程式內，然後再授權使用 Azure Active Directory 要求 blob 和佇列。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c4959d6abd1b33c81f731ad0479bd59d271c7797
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483920"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>向 Azure Active Directory 從應用程式存取 blob 和佇列

搭配使用 Azure Active Directory (Azure AD) 與 Azure 儲存體的主要優點是，您的認證不再需要儲存在程式碼中。 相反地，您可以從 Azure AD 要求 OAuth 2.0 存取權杖。 Azure AD 會針對執行應用程式的安全性主體 (使用者、群組或服務主體) 處理驗證作業。 如果驗證成功，Azure AD 會將存取權杖傳回給應用程式，然後應用程式就可以使用存取權杖來授權存取 Azure 儲存體的要求。

本文將示範如何設定應用程式以使用 Azure AD 進行驗證。 程式碼範例以 .NET 為主，但其他語言也是使用類似的方法。

從您的 Azure 儲存體應用程式驗證時的安全性主體之前，設定為該安全性主體的角色型存取控制 (RBAC) 設定。 Azure 儲存體會定義包容器和佇列權限的 RBAC 角色。 當 RBAC 角色指派給安全性主體時，此安全性主體會獲得存取該資源的權限。 如需詳細資訊，請參閱 <<c0> [ 儲存體的資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../../active-directory/develop/v1-protocols-oauth-code.md)。

若要以 OAuth 權杖為 Blob 和佇列作業授權，您必須使用 HTTPS。

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>將 RBAC 角色指派給 Azure AD 安全性主體

若要從 Azure 儲存體應用程式中驗證安全性主體，必須先為該安全性主體設定角色型存取控制 (RBAC) 設定。 Azure 儲存體會定義包容器和佇列權限的 RBAC 角色。 當 RBAC 角色指派給安全性主體時，此安全性主體會獲得存取該資源的權限。 如需詳細資訊，請參閱 < [Azure Blob 和佇列資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式

使用 Azure AD 授與儲存體資源存取權的第一個步驟是在 Azure AD 租用戶中註冊您的應用程式。 註冊您的應用程式，可讓您從程式碼中呼叫 [Azure Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL)。 ADAL 提供 API，從您的應用程式使用 Azure AD 進行驗證。 註冊您的應用程式也可讓您使用存取權杖，對該應用程式到 Azure 儲存體 API 的呼叫進行授權。

當您註冊應用程式時，會向 Azure AD 提供應用程式的相關資訊。 Azure AD 接著會提供您在執行階段用來將應用程式與 Azure AD 產生關聯的用戶端識別碼 (也稱為「應用程式識別碼」)。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../../active-directory/develop/app-objects-and-service-principals.md)。

若要註冊 Azure 儲存體應用程式，請遵循[整合應用程式與 Azure Active Directory](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) 中[新增應用程式](../../active-directory/active-directory-integrating-applications.md)一節的步驟。 如果您將應用程式註冊為原生應用程式，就能為**重新導向 URI** 指定任何有效的 URI。 此值不需要是實際的端點。

![示範如何使用 Azure AD 中註冊您的儲存體應用程式的螢幕擷取畫面](./media/storage-auth-aad-app/app-registration.png)

註冊您的應用程式之後，您會在 [設定] 下看到應用程式識別碼 (或用戶端識別碼)︰

![螢幕擷取畫面顯示用戶端識別碼](./media/storage-auth-aad-app/app-registration-client-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)。 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>將 Azure 儲存體的權限授與已註冊的應用程式

接下來，您必須授與應用程式可呼叫 Azure 儲存體 API 的權限。 此步驟可讓應用程式使用 Azure AD 對 Azure 儲存體的呼叫進行授權。

1. 在 Azure 入口網站的左側導覽窗格中，選擇 [所有服務]，然後搜尋**應用程式註冊**。
2. 搜尋您在上一個步驟中建立的已註冊應用程式名稱。
3. 選取已註冊的應用程式，然後按一下 [設定]。 在 [API 存取] 區段中，選取 [必要權限]。
4. 在 [必要權限] 刀鋒視窗中，按一下 [新增] 按鈕。
5. 在 [選取 API] 下方，搜尋「Azure 儲存體」，然後從結果清單中選取 **Azure 儲存體**。

    ![存放裝置的螢幕擷取畫面顯示使用權限](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. 在 [選取權限] 下方，核取 [存取 Azure 儲存體] 旁的方塊，然後按一下 [選取]。
7. 按一下 [完成] 。

[必要權限] 視窗現在會顯示出您的 Azure AD 應用程式具備 Azure Active Directory 與 Azure 儲存體的存取權。 當您第一次向 Azure AD 註冊應用程式時，會自動將權限授與 Azure AD。

![螢幕擷取畫面顯示註冊應用程式權限](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

程式碼範例會示範如何從 Azure AD 取得存取權杖。 存取權杖會用來驗證指定的使用者，然後對建立區塊 Blob 的要求進行授權。 若要讓此範例運作，需先遵循前面幾節中所述的步驟。

> [!NOTE]
> 身為 Azure 儲存體帳戶擁有者的您，並不會自動收到指派的資料存取權限。 您必須明確地將 Azure 儲存體的 RBAC 角色指派給自己。 您可以在訂用帳戶、資源群組、儲存體帳戶或容器/佇列層級上指派此角色。 
>
> 例如，若要以您自己的使用者身分識別，在您具有擁有權的儲存體帳戶上執行範例程式碼，您必須將「Blob 資料參與者」的 RBAC 角色指派給自己。 否則，建立 Blob 的呼叫將會失敗，而 HTTP 狀態碼為 403 (禁止)。 如需詳細資訊，請參閱 <<c0> [ 儲存體的資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。

### <a name="well-known-values-for-authentication-with-azure-ad"></a>使用 Azure AD 進行驗證所需的已知值

若要使用 Azure AD 驗證安全性主體，您需要在程式碼中包含一些已知值。

#### <a name="azure-ad-authority"></a>Azure AD 授權單位

針對 Microsoft 公用雲端，以下是基本 Azure AD 授權單位，其中 *tenant-id* 是 Active Directory 租用戶識別碼 (或目錄識別碼)：

`https://login.microsoftonline.com/<tenant-id>/`

租用戶識別碼會識別要用來驗證的 Azure AD 租用戶。 若要擷取租用戶識別碼，請依照**取得 Azure Active Directory 租用戶識別碼**所述的步驟執行。

#### <a name="storage-resource-id"></a>儲存體資源識別碼

使用 Azure 儲存體資源識別碼來取得權杖，以驗證對 Azure 儲存體的要求：

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>取得 Azure Active Directory 的租用戶識別碼

若要取得租用戶識別碼，請遵循下列步驟：

1. 在 Azure 入口網站中，選取您的 Active Directory。
2. 按一下 [內容] 。
3. 複製針對**目錄識別碼**提供的 GUID 值。 此值也稱為租用戶識別碼。

![螢幕擷取畫面顯示如何複製的租用戶識別碼](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>新增參考並使用陳述式  

從 Visual Studio 中，安裝 Azure 儲存體用戶端程式庫。 在 [工具] 功能表中，依序選取 [Nuget 套件管理員] 及 [套件管理員主控台]。 在主控台中輸入下列命令，以安裝適用於 .NET 的最新版用戶端程式庫：

```
Install-Package WindowsAzure.Storage
```

同時也請安裝最新版的 ADAL：

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

接下來，將下列 using 陳述式新增至您的程式碼：

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>從 Azure AD 取得 OAuth 權杖

接下來，新增從 Azure AD 要求權杖的方法。 若要要求權杖，請呼叫 [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) 方法。 請確定您已從先前執行的步驟中取得下列值：

- 租用戶 (目錄) 識別碼
- 用戶端 (應用程式) 識別碼
- 用戶端重新導向 URI

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>建立區塊 Blob

最後，使用存取權杖來建立新的儲存體認證，並使用這些認證來建立 blob。 請記住，若要授權 OAuth 權杖的 blob 和佇列作業，您必須使用 HTTPS。:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> Azure AD 與 Azure 儲存體的整合需要您使用 HTTPS 來進行 Azure 儲存體作業。

在上面的範例中，.NET 用戶端程式庫會處理要求的授權以建立區塊 Blob。 其他儲存體用戶端程式庫也會為您處理要求的授權。 不過，若您透過 OAuth 權杖使用 REST API 呼叫 Azure 儲存體作業，則您將必須使用 OAuth 權杖授權該要求。   

若要使用 OAuth 存取權杖呼叫 Blob 與佇列服務作業，請使用 **Bearer** 結構描述在 **Authorization** 標頭中傳遞存取權杖，並指定 2017-11-09 或更高的服務版本，如下列範例所示：

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

如需有關授權的其餘部分的 Azure 儲存體作業的詳細資訊，請參閱[向 Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory)。

## <a name="next-steps"></a>後續步驟

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[使用 RBAC 管理存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何使用受管理的身分識別與 Azure 儲存體的 Azure 資源，請參閱[驗證適用於 Azure 資源的存取權的 blob 和佇列與 Azure Active Directory 與受管理的身分識別](storage-auth-aad-msi.md)。
- 若要了解如何以 Azure AD 認證來執行 Azure CLI 和 PowerShell 命令，請參閱[執行 Azure CLI 或 PowerShell 命令與 Azure AD 認證來存取 blob 或佇列資料](storage-auth-aad-script.md)。
