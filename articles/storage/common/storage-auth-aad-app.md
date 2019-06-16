---
title: 從用戶端應用程式驗證與 Azure Active Directory 存取 blob 和佇列資料
description: 您可以使用 Azure Active Directory 來驗證從用戶端應用程式內，取得 OAuth 2.0 權杖，並授權要求到 Azure Blob 儲存體和佇列儲存體。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 30ebfec88182684f8e852808e978a51854389898
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073466"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>向 Azure Active Directory 從應用程式存取 blob 和佇列

使用 Azure Active Directory (Azure AD) 與 Azure Blob 儲存體或佇列儲存體的主要優點是您的認證，不再需要儲存在您的程式碼。 相反地，您可以要求 OAuth 2.0 存取權杖，Microsoft 身分識別平台 (先前稱為 Azure AD)。 Azure AD 會驗證安全性主體 （使用者、 群組或服務主體） 執行應用程式。 如果驗證成功，Azure AD 會傳回存取權杖給應用程式，以及應用程式可以接著使用存取權杖授權要求到 Azure Blob 儲存體或佇列儲存體。

本文說明如何使用 Microsoft 身分識別平台 2.0 設定您的原生應用程式或 web 應用程式進行驗證。 程式碼範例以 .NET 為主，但其他語言也是使用類似的方法。 如需有關 Microsoft 身分識別平台 2.0 的詳細資訊，請參閱 < [Microsoft 身分識別平台 (v2.0) 概觀](../../active-directory/develop/v2-overview.md)。

如需 OAuth 2.0 程式碼授與流程的概觀，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>將角色指派給 Azure AD 安全性主體

若要從 Azure 儲存體應用程式中驗證安全性主體，必須先為該安全性主體設定角色型存取控制 (RBAC) 設定。 Azure 儲存體定義內建的 RBAC 角色，其中包含適用於容器和佇列的權限。 當 RBAC 角色指派給安全性主體時，此安全性主體會獲得存取該資源的權限。 如需詳細資訊，請參閱 < [Azure Blob 和佇列資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>向 Azure AD 租用戶註冊應用程式

使用 Azure AD 來授權存取儲存體資源的第一個步驟註冊用戶端應用程式與從 Azure AD 租用戶[Azure 入口網站](https://portal.azure.com)。 當您註冊用戶端應用程式時，您會提供 Azure AD 應用程式的相關資訊。 Azure AD 接著會提供您在執行階段用來將應用程式與 Azure AD 產生關聯的用戶端識別碼 (也稱為「應用程式識別碼」  )。 若要深入了解用戶端識別碼，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../../active-directory/develop/app-objects-and-service-principals.md)。

若要註冊您的 Azure 儲存體應用程式，請依照下列所示的步驟[快速入門：註冊應用程式與 Microsoft 身分識別平台](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)。 下圖顯示註冊 web 應用程式的一般設定：

![示範如何使用 Azure AD 中註冊您的儲存體應用程式的螢幕擷取畫面](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 如果您將應用程式註冊為原生應用程式，就能為**重新導向 URI** 指定任何有效的 URI。 原生應用程式，此值不必是實際的 URL。 Web 應用程式，重新導向 URI 必須是有效的 URI，因為它會指定語彙基元所提供的 URL。

註冊您的應用程式之後，您會在 [設定]  下看到應用程式識別碼 (或用戶端識別碼)︰

![螢幕擷取畫面顯示用戶端識別碼](./media/storage-auth-aad-app/app-registration-client-id.png)

如需有關向 Azure AD 註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md)。

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>將 Azure 儲存體的權限授與已註冊的應用程式

接下來，授與您的應用程式權限來呼叫 Azure 儲存體 Api。 此步驟可讓您的應用程式授權與 Azure AD 的 Azure 儲存體的要求。

1. 在 **概觀**您已註冊應用程式頁面，選取**檢視 API 的權限**。
1. 在  **API 的權限**區段中，選取**新增權限**，然後選擇 **我的組織使用的 Api**。
1. 底下**我的組織使用的 Api**區段中搜尋 「 Azure 儲存體 」，然後選取**Azure 儲存體**從清單中要顯示的結果**要求的 API 權限**窗格。

    ![存放裝置的螢幕擷取畫面顯示使用權限](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. 底下**應用程式需要何種權限？** ，觀察可用的權限類型為**委派的權限**。 預設為您選取此選項。
1. 中**選取權限**一節**要求的 API 權限**窗格中，選取此核取方塊旁**user_impersonation**，然後按一下 **新增權限**。
1. **API 的權限**窗格現在會顯示您的 Azure AD 應用程式具有存取 Microsoft Graph 和 Azure 儲存體。 權限會自動授與 Microsoft graph 時第一次向 Azure AD 的應用程式。

    ![螢幕擷取畫面顯示註冊應用程式權限](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>建立用戶端密碼

應用程式必須證明其識別身分要求權杖時用戶端祕密。 若要新增的用戶端密碼，請遵循下列步驟：

1. 瀏覽至 Azure 入口網站中的應用程式註冊。
1. 選取 **憑證與祕密**設定。
1. 底下**用戶端祕密**，按一下**新的用戶端祕密**來建立新的密碼。
1. 提供的祕密的描述，然後選擇所需的到期間隔。
1. 立即將新的祕密的值複製到安全的位置。 完整的值會顯示您只有一次。

    ![螢幕擷取畫面顯示用戶端祕密](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>用戶端程式庫來取得權杖

一旦您已註冊您的應用程式，並授與權限來存取 Azure Blob 儲存體或佇列儲存體中的資料，您可以加入程式碼，您的應用程式驗證的安全性主體，並取得 OAuth 2.0 權杖。 若要驗證及取得權杖，您可以使用其中一個[Microsoft 身分識別平台驗證程式庫](../../active-directory/develop/reference-v2-libraries.md)或其他支援 OpenID Connect 1.0 的開放原始碼程式庫。 您的應用程式接著可以使用存取權杖來授權對 Azure Blob 儲存體或佇列儲存體的要求。

如需為其取得語彙基元支援的案例，請參閱[案例](https://aka.ms/msal-net-scenarios)一節[Microsoft Authentication Library (MSAL) 適用於.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 存放庫。

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

程式碼範例會示範如何從 Azure AD 取得存取權杖。 存取權杖會用來驗證指定的使用者，然後對建立區塊 Blob 的要求進行授權。 若要讓此範例運作，需先遵循前面幾節中所述的步驟。

若要要求權杖，您需要您的應用程式註冊中的以下值：

- 您的 Azure AD 網域名稱。 擷取此值從**概觀**的 Azure Active Directory 頁面。
- 為租用戶 （或目錄） 識別碼。 擷取此值從**概觀**中的應用程式註冊頁面。
- 用戶端 （或應用程式） 的識別碼。 擷取此值從**概觀**中的應用程式註冊頁面。
- 用戶端重新導向 URI。 擷取此值從**驗證**應用程式註冊的設定。
- 用戶端祕密值。 從您先前複製到它的位置中擷取此值。

### <a name="well-known-values-for-authentication-with-azure-ad"></a>使用 Azure AD 進行驗證所需的已知值

若要使用 Azure AD 驗證安全性主體，您需要在程式碼中包含一些已知值。

#### <a name="azure-ad-authority"></a>Azure AD 授權單位

針對 Microsoft 公用雲端，以下是基本 Azure AD 授權單位，其中 *tenant-id* 是 Active Directory 租用戶識別碼 (或目錄識別碼)：

`https://login.microsoftonline.com/<tenant-id>/`

租用戶識別碼會識別要用來驗證的 Azure AD 租用戶。 它也稱為目錄識別碼。 若要擷取的租用戶識別碼，請瀏覽至**概觀**在 Azure 入口網站中，應用程式註冊頁面，並從該處複製的值。

#### <a name="storage-resource-id"></a>儲存體資源識別碼

您可以使用 Azure 儲存體資源識別碼來取得權杖，以授權要求到 Azure 儲存體：

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>建立儲存體帳戶和容器

若要執行的程式碼範例，建立您的 Azure Active Directory 的相同訂用帳戶內的儲存體帳戶。 然後建立該儲存體帳戶內的容器。 範例程式碼會建立區塊 blob，此容器中。

接下來，明確指派**儲存體 Blob 資料參與者**角色，才能將執行範例程式碼的使用者帳戶。 如需如何指派此角色在 Azure 入口網站中的指示，請參閱 <<c0> [ 授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](storage-auth-aad-rbac-portal.md)。

> [!NOTE]
> 當您建立的 Azure 儲存體帳戶時，您不會自動指派資料透過 Azure AD 存取權限。 您必須明確地將 Azure 儲存體的 RBAC 角色指派給自己。 您可以在訂用帳戶、資源群組、儲存體帳戶或容器/佇列層級上指派此角色。

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>建立會授與存取 Blob 儲存體與 Azure AD web 應用程式

當您的應用程式存取 Azure 儲存體時，它沒有等等代表使用者，這表示 blob 或佇列資源會使用登入之使用者的權限存取。 若要試用此程式碼範例，您必須提示使用者使用 Azure AD 身分識別登入的 web 應用程式。 您可以建立您自己，或使用 Microsoft 所提供的範例應用程式。

完整的範例 web 應用程式取得權杖，並使用它來建立 Azure 儲存體中的 blob，位於[GitHub](http://aka.ms/aadstorage)。 檢閱及執行完整的範例可能有助您了解的程式碼範例。 如需有關如何執行完整的範例，指示，請參閱一節[檢視] 和 [執行完整的範例](#view-and-run-the-completed-sample)。

#### <a name="add-references-and-using-statements"></a>新增參考並使用陳述式  

從 Visual Studio 中，安裝 Azure 儲存體用戶端程式庫。 在 [工具]  功能表中，依序選取 [Nuget 套件管理員]  及 [套件管理員主控台]  。 在主控台視窗，從 Azure 儲存體用戶端程式庫安裝必要的套件，適用於.NET 中輸入下列命令：

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

接下來，新增下列 using 陳述式 HomeController.cs 檔案：

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>建立區塊 Blob

新增下列程式碼片段來建立區塊 blob:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> 若要授權使用 OAuth 2.0 權杖的 blob 和佇列作業，您必須使用 HTTPS。

在上面的範例中，.NET 用戶端程式庫會處理要求的授權以建立區塊 Blob。 針對其他語言的 azure 儲存體用戶端程式庫也會處理您的要求的授權。 不過，若您透過 OAuth 權杖使用 REST API 呼叫 Azure 儲存體作業，則您將必須使用 OAuth 權杖授權該要求。

若要使用 OAuth 存取權杖呼叫 Blob 與佇列服務作業，請使用 **Bearer** 結構描述在 **Authorization** 標頭中傳遞存取權杖，並指定 2017-11-09 或更高的服務版本，如下列範例所示：

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>從 Azure AD 取得 OAuth 權杖

接下來，新增從 Azure AD 要求權杖的方法。 您要求的語彙基元會代表使用者，而我們將使用 GetTokenOnBehalfOfUser 方法。

請記住，如果您最近已登入，以及您所要求的語彙基元`storage.azure.com`資源，您必須顯示的 UI，其中使用者可以同意這類動作，其代表的使用者。 為了方便您要攔截`MsalUiRequiredException`並新增功能，以要求使用者同意，如下列範例所示：

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

同意是使用者授權應用程式代表使用者存取受保護的資源所用的程序。 Microsoft 身分識別平台 2.0 支援增量同意，表示安全性主體可以一開始要求最低權限集合，然後視需要經過一段時間新增權限。 當您的程式碼會要求存取權杖時，以指定的應用程式需要在藉由在任何指定時間的權限範圍`scope`參數。 如需有關累加同意的詳細資訊，請參閱節**增量和動態同意**中[為什麼更新至 Microsoft 身分識別平台 (v2.0)？](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)。

下列方法會建構要求累加同意的驗證屬性：

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>檢視及執行完整的範例

若要執行範例應用程式，請先複製或從[GitHub](http://aka.ms/aadstorage)。 下列各節中所述，然後更新應用程式。

### <a name="provide-values-in-the-settings-file"></a>在 設定檔中提供值

接下來，更新*appsettings.json*檔案，是搭配您自己的值，如下所示：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>更新儲存體帳戶和容器名稱

在  *HomeController.cs*檔案中，更新會參考要使用的儲存體帳戶和容器名稱的區塊 blob 的 URI:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>啟用隱含授與流程

若要執行範例時，您可能需要設定隱含授與流程，您的應用程式註冊。 請遵循下列步驟：

1. 瀏覽至 Azure 入口網站中的應用程式註冊。
1. 在 [管理] 區段中，選取**驗證**設定。
1. 底下**進階設定**，請在**隱含授與**區段中，選取核取方塊來啟用存取權杖和識別碼權杖，如下圖所示：

    ![螢幕擷取畫面顯示如何允許隱含授與流程的設定](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>更新使用 localhost 的連接埠

當您執行範例時，您可能會發現您需要更新中若要使用的應用程式註冊指定 URI 的重新導向*localhost*在執行階段指派連接埠。 若要更新的重新導向 URI 使用指派的連接埠，請遵循下列步驟：

1. 瀏覽至 Azure 入口網站中的應用程式註冊。
1. 在 [管理] 區段中，選取**驗證**設定。
1. 底下**重新導向 Uri**，編輯連接埠，使其符合使用範例應用程式，如下圖所示：

    ![顯示螢幕擷取畫面的應用程式註冊重新導向 Uri](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Microsoft 身分識別平台，請參閱[Microsoft 身分識別平台](https://docs.microsoft.com/azure/active-directory/develop/)。
- 若要深入了解 Azure 儲存體的 RBAC 角色，請參閱[儲存體的資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。
- 若要了解如何使用受管理的身分識別與 Azure 儲存體的 Azure 資源，請參閱[驗證適用於 Azure 資源的存取權的 blob 和佇列與 Azure Active Directory 與受管理的身分識別](storage-auth-aad-msi.md)。
