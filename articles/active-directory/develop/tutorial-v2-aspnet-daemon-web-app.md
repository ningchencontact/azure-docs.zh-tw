---
title: 呼叫受 Azure AD 保護的 ASP.NET Web API - Microsoft 身分識別平台
description: 在本快速入門中，了解如何從 Windows 桌面 (WPF) 應用程式，呼叫受 Azure Active Directory 保護的 ASP.NET Web API。 WPF 用戶端會驗證使用者、要求存取權杖，以及呼叫 Web API。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328547"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>使用 Microsoft 身分識別平台端點建置多租用戶精靈

在本教學課程中，您將了解如何使用 Microsoft 身分識別平台，在長時間執行的非互動式程序中存取 Microsoft 企業客戶的資料。 範例精靈會使用 [OAuth2 用戶端認證授與](v2-oauth2-client-creds-grant-flow.md)取得存取權杖，然後用它來呼叫 [Microsoft Graph](https://graph.microsoft.io) 並存取組織資料。

應用程式會建置為 ASP.NET MVC 應用程式，並使用 OWIN OpenID Connect 中介軟體來登入使用者。  在此範例中，其「精靈」元件是 API 控制器，此控制器經呼叫時，會從 Microsoft Graph 提取客戶 Azure AD 租用戶中的使用者清單。  此 `SyncController.cs` 由 Web 應用程式中的 AJAX 呼叫所觸發，並使用[適用於 .NET 的 Microsoft 驗證程式庫 (MSAL)](msal-overview.md) 來取得 Microsoft Graph 的存取權杖。

對於較簡單的主控台精靈應用程式，請參閱 [.NET Core 精靈快速入門](quickstart-v2-netcore-daemon.md)。

## <a name="scenario"></a>案例

由於此應用程式是供任何 Microsoft 企業客戶使用的多租用戶應用程式，因此必須提供讓客戶將應用程式「註冊」或「連線」至其公司資料的方法。  在連線流程內，公司管理員會先將**應用程式權限**直接授與應用程式，讓它能夠在沒有登入使用者的情況下，以非互動方式存取公司資料。  此範例中的邏輯主要將說明如何使用身分識別平台[管理員同意](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)端點來完成此連線流程。

![拓撲](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

若要進一步了解此範例中使用的概念，請務必閱讀[身分識別平台端點用戶端認證通訊協定文件](v2-oauth2-client-creds-grant-flow.md)。

## <a name="prerequisites"></a>必要條件

若要執行本快速入門中的範例，您將需要：

- [Visual Studio 2017 或 2019](https://visualstudio.microsoft.com/downloads/)
- Azure Active Directory (Azure AD) 租用戶。 如需有關如何取得 Azure AD 租用戶的詳細資訊，請參閱[如何取得 Azure AD 租用戶](quickstart-create-new-tenant.md)
- Azure AD 租用戶中的一或多個使用者帳戶。 此範例不會使用 Microsoft 帳戶 (前身為 Windows Live 帳戶)。 因此，如果您使用 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)，且您之前從未在目錄中建立使用者帳戶，您就必須於此時建立。

## <a name="clone-or-download-this-repository"></a>複製或下載此存放庫

從殼層或命令列：

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

或者，[下載 ZIP 檔案中的範例](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)。

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>向您的 Azure AD 租用戶註冊範例應用程式

此範例中有一個專案。 若要加以註冊，您可以：

- 執行[向您的 Azure Active Directory 租用戶註冊範例](#register-the-sample-application-with-your-azure-ad-tenant)或[設定範例以使用您的 Azure AD 租用戶](#choose-the-azure-ad-tenant-for-the-applications)步驟
- 或者，使用 PowerShell 指令碼來：
  - **自動**為您建立 Azure AD 應用程式和相關物件 (密碼、權限、相依性)
  - 修改 Visual Studio 專案的組態檔。

如果您想要使用此自動化功能：

1. 在 Windows 上，執行 PowerShell 並瀏覽至複製目錄的根目錄
1. 在 PowerShell 中執行：

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. 執行建立 Azure AD 應用程式的指令碼，並據以設定範例應用程式的程式碼。
1. 在 PowerShell 中執行：

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > [應用程式建立指令碼](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)中說明執行指令碼的其他方式

1. 開啟 Visual Studio 解決方案，然後按一下 [啟動] 以執行程式碼。

如果您不想要使用此自動化功能，請遵循下列步驟。

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>選擇應用程式的 Azure AD 租用戶

在第一個步驟中，您必須：

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶出現在多個 Azure AD 租用戶中，請在頁面頂端功能表的右上角選取您的設定檔，然後**切換目錄**。
   將入口網站工作階段變更為所需的 Azure AD 租用戶。

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>註冊用戶端應用程式 (dotnet-web-daemon-v2)

1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. 選取 [新增註冊]  。
1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
   - 在 [名稱]  區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `dotnet-web-daemon-v2`。
   - 在 [支援的帳戶類型]  區段中，選取 [任何組織目錄中的帳戶]  。
   - 在 [重新導向 URI (選用)] 區段中，選取下拉式方塊中的 [Web]  ，然後輸入下列重新導向 URI：
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions`如果有多個重新導向 URI，您稍後必須在應用程式建立成功之後，從 [驗證]  索引標籤新增這些 URI。
1. 選取 [註冊]  以建立應用程式。
1. 在應用程式 [概觀]  頁面上，尋找 [應用程式 (用戶端) 識別碼]  值並將它記下供稍後使用。 您必須用此識別碼來設定此專案的 Visual Studio 組態檔。
1. 在應用程式頁面清單中，選取 [驗證]  。
   - 在 [進階設定]  區段中，將 [登出 URL]  設定為 `https://localhost:44316/Account/EndSession`
   - 在 [進階設定]   | [隱含授與]  區段中，勾選 [存取權杖]  和 [識別碼權杖]  ，因為此範例需要啟用 [隱含授與流程](v2-oauth2-implicit-grant-flow.md)，才能登入使用者以及呼叫 API。
1. 選取 [儲存]  。
1. 從 [憑證和祕密]  頁面的 [用戶端密碼]  區段中，選擇 [新增用戶端密碼]  ：

   - 輸入 (執行個體 `app secret` 的) 金鑰描述。
   - 選取金鑰持續時間，此值可為 [1 年]  、[2 年]  或 [永不過期]  。
   - 當您按下 [新增]  按鈕時，將會顯示金鑰值、複製該值，並將其儲存在安全的位置。
   - 稍後您將需要用此金鑰在 Visual Studio 中設定專案。 此金鑰值不會再次顯示，也無法透過任何其他方式來擷取，因此，請於此值出現在 Azure 入口網站後就立即記錄下來。
1. 在應用程式頁面清單中，選取 [API 權限] 
   - 按一下 [新增權限]  按鈕，然後
   - 確定已選取 [Microsoft API]  索引標籤
   - 在 [常用的 Microsoft API]  區段中，按一下 [Microsoft Graph] 
   - 在 [應用程式權限]  區段中，確定已勾選正確的權限：**User.Read.All**
   - 選取 [新增權限]  按鈕

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>設定範例以使用您的 Azure AD 租用戶

在下列步驟中，"ClientID" 與「應用程式識別碼」或 "AppId" 相同。

在 Visual Studio 中開啟解決方案以設定專案

### <a name="configure-the-client-project"></a>設定用戶端專案

如果您使用安裝指令碼，系統會為您套用下列變更。

1. 開啟 `UserSync\Web.Config` 檔案
1. 尋找應用程式金鑰 `ida:ClientId`，並將現有的值取代為從 Azure 入口網站複製的 `dotnet-web-daemon-v2` 應用程式的應用程式識別碼 (clientId)。
1. 尋找應用程式金鑰 `ida:ClientSecret`，並將現有的值取代為您在 Azure 入口網站中建立 `dotnet-web-daemon-v2` 應用程式期間所儲存的金鑰。

## <a name="run-the-sample"></a>執行範例

清除解決方案、重建解決方案，並執行 UserSync 應用程式，然後在 Azure AD 租用戶中以管理員身分登入，展開作業。  如果您沒有 Azure AD 租用戶可用於測試，您可以[依照這些指示](quickstart-create-new-tenant.md)取得一個。

當您登入時，應用程式會先要求您提供登入權限，並要求您讀取使用者設定檔。  此同意可讓應用程式確保您是企業使用者。

![使用者同意](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

應用程式接著會嘗試透過 Microsoft Graph，從您的 Azure AD 租用戶同步使用者清單。  若無法執行此作業，則會要求您 (租用戶管理員) 將租用戶連線至應用程式。

然後，應用程式會要求在您的租用戶中讀取使用者清單的權限。

![管理員同意](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**授與權限後，您將會登出應用程式**。 這是為了確保用於 Graph 的任何現有存取權杖都會從權杖快取中移除。 當您再次登入之後，取得的全新權杖將會有對 MS Graph 進行呼叫的必要權限。
在您授與權限後，應用程式就能夠在任何時間點查詢使用者。  您可以按一下 [使用者] 頁面上的 [同步使用者]  按鈕，並重新整理使用者清單，來確認這一點。  嘗試新增或移除使用者，並重新同步清單 (但請注意，這樣只會同步使用者的第一個頁面)。

## <a name="about-the-code"></a>關於程式碼

此範例的相關程式碼位於下列檔案中：

- 初始登入：`App_Start\Startup.Auth.cs`、`Controllers\AccountController.cs`。  特別是，控制器上的動作具有「授權」屬性，會強制使用者登入。 應用程式會使用[授權碼流程](v2-oauth2-auth-code-flow.md)來登入使用者。
- 將使用者清單同步至本機記憶體內部存放區：`Controllers\SyncController.cs`
- 顯示本機記憶體內部存放區中的使用者清單：`Controllers\UserController.cs`
- 使用管理員同意端點，取得租用戶管理員提供的權限：`Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>重新建立此範例應用程式

1. 在 Visual Studio 中，建立新的 `Visual C#` `ASP.NET Web Application (.NET Framework)` 專案。 在下一個畫面中，選擇 `MVC` 專案範本。 此外，新增 `Web API` 的資料夾和核心參考，因為您稍後將會新增 Web API 控制器。  將專案選擇的驗證模式保留為預設值，也就是 `No Authentication`。
2. 在 [方案總管]  視窗中選取專案，然後按 **F4** 鍵以顯示專案屬性。 在 [專案屬性] 中，將 [已啟用 SSL]  設定為 `True`。 記下 **SSL URL**。 在 Azure 入口網站中設定此應用程式的註冊時，將會用到這項資料。
3. 新增下列 ASP.Net OWIN 中介軟體 Nuget：`Microsoft.Owin.Security.ActiveDirectory`、`Microsoft.Owin.Security.Cookies` 和 `Microsoft.Owin.Host.SystemWeb`、`Microsoft.IdentityModel.Protocol.Extensions`、`Microsoft.Owin.Security.OpenIdConnect` 和 `Microsoft.Identity.Client`。 
4. 在 `App_Start` 資料夾中，建立類別 `Startup.Auth.cs`。 從命名空間名稱中移除 `.App_Start`。  請將 `Startup` 類別的程式碼取代為範例應用程式的相同檔案中的程式碼。  請務必採用整個類別定義！  定義會從 `public class Startup` 變更為 `public partial class Startup`
5. 在 `Startup.Auth.cs` 中，藉由新增 Visual Studio intellisense 所建議的 `using` 陳述式來解析遺漏的參考。
6. 以滑鼠右鍵按一下專案，依序選取 [新增] 和 [類別]，然後在搜尋方塊中輸入 "OWIN"。  「OWIN 啟動類別」將會顯示為選取項目；請加以選取，並將類別命名為 `Startup.cs`。
7. 在 `Startup.cs` 中，將 `Startup` 類別的程式碼取代為範例應用程式的相同檔案中的程式碼。  同樣地，請留意定義會從 `public class Startup` 變更為 `public partial class Startup`。
8. 在資料夾中，新增名為 `MsGraphUser.cs` 的新類別。  請將實作取代為範例中同名檔案的內容。
9. 新增名為 `AccountController` 的 **MVC 5 控制器 - 空**。 請將實作取代為範例中同名檔案的內容。
10. 新增名為 `UserController` 的 **MVC 5 控制器 - 空**。 請將實作取代為範例中同名檔案的內容。
11. 新增名為 `SyncController` 的 **Web API 2 控制器 - 空**。 請將實作取代為範例中同名檔案的內容。
12. 針對使用者介面，在 `Views\Account` 資料夾中新增三個分別名為 `GrantPermissions`、`Index` 和 `UserMismatch` 的 **空 (沒有模型) 檢視**，並在 `Views\User` 資料夾中新增一個名為 `Index` 的檢視。 請將實作取代為範例中同名檔案的內容。
13. 更新 `Shared\_Layout.cshtml` 和 `Home\Index.cshtml`，以正確地將各種不同的檢視連結在一起。

## <a name="deploy-this-sample-to-azure"></a>將此範例部署至 Azure

此專案具有 WebApp/Web API 專案。 若要將其部署至 Azure 網站，您將需要分別執行下列動作：

- 建立 Azure 網站
- 將 Web 應用程式/Web API 發佈至網站，並且
- 更新其用戶端以呼叫網站，而不是 IIS Express。

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>建立 `dotnet-web-daemon-v2` 並將其發佈至 Azure 網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下左上角的 `Create a resource`，選取 [Web]   --> [Web 應用程式]  ，然後為您的網站命名，例如 `dotnet-web-daemon-v2-contoso.azurewebsites.net`。
1. 然後，選取 `Subscription`、`Resource Group`、`App service plan and Location`。 `OS` 將是 [Windows]  ，`Publish` 將是 [程式碼]  。
1. 按一下 `Create` 並等待系統建立 App Service。
1. 在收到 `Deployment succeeded` 通知後，按一下 `Go to resource` 以瀏覽至新建立的 App Service。
1. 網站建立後，請在 [儀表板]  中找出該網站，並按一下以開啟 **App Service** 的 [概觀]  畫面。
1. 從 App Service 的 [概觀]  索引標籤中，按一下 [取得發行設定檔]  連結以下載發行設定檔，並加以儲存。  您也可以使用其他部署機制，例如從原始檔控制。
1. 切換至 Visual Studio，並移至 dotnet-web-daemon-v2 專案。  以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [發佈]  。  按一下底部列上的 [匯入設定檔]  ，然後匯入您先前下載的發行設定檔。
1. 按一下 [設定]  ，然後在 `Connection tab` 中更新 [目的地 URL]，使其在首頁 URL 中成為 `https`，例如 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)。 按 [下一步]  。
1. 在 [設定] 索引標籤上，確定未選取 `Enable Organizational Authentication`。  按一下 [檔案]  。 按一下主畫面上的 [發佈]  。
1. Visual Studio 會發佈專案，並自動開啟瀏覽器並導向至專案的 URL。  如果您看到專案的預設網頁，表示發佈成功。

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>更新 `dotnet-web-daemon-v2` 的 Azure AD 租用戶應用程式註冊

1. 瀏覽回 [Azure 入口網站](https://portal.azure.com)。
在左側導覽窗格中，選取 [Azure Active Directory]  服務，然後選取 [應用程式註冊]  。
1. 在產生的畫面中，選取 `dotnet-web-daemon-v2` 應用程式。
1. 在您應用程式的 [驗證]  頁面上，以您的服務位址更新 [登出 URL] 欄位，例如 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. 從 [商標]  功能表，將 [首頁 URL]  更新為您的服務位址，例如 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)。 儲存組態。
1. 在 [驗證 -> 重新導向 URI]  功能表的值清單中，新增相同的 URL。 如果您有多個重新導向 URL，請確定在使用應用程式服務的 URI 作為每個重新導向 URL 時，都有新的輸入項目。

## <a name="community-help-and-support"></a>社群協助與支援

使用 [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) 取得來自社群的支援。
請先在 Stack Overflow 上詢問您的問題，並瀏覽現有的問題，以查看先前是否有人已提出您的問題。
請確定您的問題或意見已標記為 [`adal` `msal` `dotnet`]。

如果您在範例中發現錯誤 (bug)，請在 [GitHub 問題](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)上提出問題。

如果您在 MSAL.NET 中發現錯誤 (bug)，請在 [MSAL.NET GitHub 問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)上提出問題。

若要提供建議，請造訪下列 [User Voice 頁面](https://feedback.azure.com/forums/169401-azure-active-directory)。

## <a name="next-steps"></a>後續步驟
深入了解 Microsoft 身分識別平台所支援的不同[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)。

如需詳細資訊，請參閱下列概念文件：

- [Azure Active Directory 中的租用](single-and-multi-tenant-apps.md)
- [了解 Azure AD 應用程式同意體驗](application-consent-experience.md)
- [操作說明：讓任何 Azure Active Directory (AD) 使用者以多租用戶應用程式的模式登入](howto-convert-app-to-be-multi-tenant.md)
- [了解使用者和管理員同意](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Azure Active Directory 中的應用程式和服務主體物件](app-objects-and-service-principals.md)
- [快速入門：使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)
- [快速入門：設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md)
- [取得具有用戶端認證流程的應用程式適用的權杖](msal-client-applications.md)

對於較簡單的多租用戶主控台精靈應用程式，請參閱 [.NET Core 精靈快速入門](quickstart-v2-netcore-daemon.md)。
