---
title: 登入使用者的 Web 應用程式（應用程式註冊）-Microsoft 身分識別平臺
description: 瞭解如何建立可登入使用者的 web 應用程式（應用程式註冊）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 641f71f6111930b54d0a2bd548f16d3cb0c07189
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175270"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>登入使用者的 Web 應用程式-應用程式註冊

此頁面說明登入使用者之 web 應用程式的應用程式註冊細節。

若要註冊您的應用程式，您可以使用：

- [Web 應用程式快速入門](#register-an-app-using-the-quickstarts)-除了是建立應用程式的絕佳第一個體驗之外，Azure 入口網站中的快速入門會包含名為 [**為我進行這項變更**] 的按鈕。 您可以使用此按鈕來設定所需的屬性，即使是現有的應用程式也是如此。 您必須將這些屬性的值調整為您自己的案例。 特別是，您應用程式的 Web API URL 可能會與建議的預設值不同，這也會影響登出 URI。
- [手動註冊應用程式](#register-an-app-using-azure-portal)的 Azure 入口網站
- PowerShell 和命令列工具

## <a name="register-an-app-using-the-quickstarts"></a>使用快速入門註冊應用程式

如果您流覽至此連結，則可以建立 web 應用程式建立啟動載入器：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-using-azure-portal"></a>使用 Azure 入口網站註冊應用程式

> [!NOTE]
> 要使用的入口網站會根據您的應用程式是在 Microsoft Azure 公用雲端或在全國或主權雲端中執行而有所不同。 如需詳細資訊，請參閱[國家/地區](./authentication-national-cloud.md#app-registration-endpoints)雲端


1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。 或者，登入選擇的國家雲端 Azure 入口網站。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 在左側導覽窗格中，選取 [ **Azure Active Directory** ] 服務，然後選取 [**應用程式註冊** > **新增註冊**]。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   1. 為您的應用程式選擇支援的帳戶類型（請參閱[支援的帳戶類型](./v2-supported-account-types.md)）
   1. 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `AspNetCore-WebApp`。
   1. 在 [重新**導向 URI**] 中，新增應用程式類型和 URI 目的地，以在成功驗證後接受傳回的權杖回應。 例如： `https://localhost:44321/` 。  選取 [註冊]。
1. 選取 [驗證] 功能表，然後新增下列資訊：
   1. 在 [**回復 URL**] 中，新增「Web」類型的 `https://localhost:44321/signin-oidc`。
   1. 在 [進階設定] 區段中，將 [登出 URL]設定為 `https://localhost:44321/signout-oidc`。
   1. 在 [隱含授與] 底下，核取 [識別碼權杖]。
   1. 選取 [儲存]。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   1. 為您的應用程式選擇支援的帳戶類型（請參閱[支援的帳戶類型](./v2-supported-account-types.md)）
   - 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `MailApp-openidconnect-v2`。
   - 在 [重新導向 URI （選擇性）] 區段中，選取下拉式方塊中的 [ **Web** ]，然後輸入下列重新導向 uri： `https://localhost:44326/`。
1. 選取 [註冊] 以建立應用程式。
1. 選取 [驗證] 功能表，然後新增下列資訊：
   - 在 [**高級設定**] | [**隱含授**與] 區段中，檢查**識別碼權杖**，因為此範例需要啟用[隱含授與流程](v2-oauth2-implicit-grant-flow.md)才能登入使用者。
1. 選取 [儲存]。

# <a name="javatabjava"></a>[Java](#tab/java)

4. 當 [**註冊應用程式] 頁面**出現時，請為應用程式輸入易記名稱，例如 ' java-webapp '，選取 [任何組織目錄中的帳戶和個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）]，然後選取 [Web 應用程式]。/API ' 作為*應用程式類型*。
1. 按一下 [**註冊**] 以註冊應用程式。
1. 在左側功能表上，按一下 [**驗證**]，然後在 [重新*導向 uri*] 底下，選取 [Web]。 您必須輸入兩個不同的重新導向 Uri：一個用於登入頁面，另一個用於圖形頁面。 對於這兩種情況，您應該使用相同的主機和埠號碼，然後在登入頁面中加上 "/msal4jsample/secure/aad"，並在 [使用者資訊] 頁面上輸入 "msal4jsample/graph/me"。
 根據預設，此範例會使用：

    - `http://localhost:8080/msal4jsample/secure/aad`答案中所述步驟，工作帳戶即會啟用。
    - `http://localhost:8080/msal4jsample/graph/me`

    在 [進階設定] 區段中，將 [登出 URL]設定為 `http://localhost:8080/msal4jsample/sign_out`。

     按一下 [**儲存**]。

1. 從功能表選取 [**憑證 & 密碼**]，然後在 [**用戶端密碼**] 區段中按一下 [**新增用戶端密碼**]：

    - 輸入金鑰描述
    - 選取**1 年**的金鑰持續時間。
    - 當您選取 [**新增**] 時，將會顯示金鑰值。
    - 複製金鑰的值，以供稍後用。 此機碼值不會再顯示，也不會透過任何其他方式來加以抓取，因此，請在 Azure 入口網站看到它時立即記錄。

# <a name="pythontabpython"></a>[Python](#tab/python)

4. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   - 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `python-webapp`。
   - 將**支援的帳戶類型**變更為**任何組織目錄中的帳戶和個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）** 。
   - 在 [重新導向 URI （選擇性）] 區段中，選取下拉式方塊中的 [ **Web** ]，然後輸入下列重新導向 uri： `http://localhost:5000/getAToken`。
1. 選取 [註冊] 以建立應用程式。
1. 在應用程式 [概觀] 頁面上，尋找 [應用程式 (用戶端) 識別碼] 值並將它記下供稍後使用。 您將需要它來設定此專案的 Visual Studio 設定檔。
1. 從應用程式的 [總覽] 頁面中，選取 [**驗證**] 區段。
   - 在 [ **Advanced settings** ] 區段中，將 [**登出 URL** ] 設定為 `http://localhost:5000/logout`

  選取 [儲存]。
1. 在左側功能表上，選擇 [憑證與祕密]，然後在 [用戶端密碼] 區段中，按一下 [新增用戶端密碼]：

      - 輸入金鑰描述
      - 選取 [1 年] 作為 [金鑰持續時間]。
      - 當您按一下 [新增] 時，金鑰值會隨即顯示。
      - 複製金鑰的值。 稍後您將會用到此資訊。
---

## <a name="register-an-app-using-powershell"></a>使用 PowerShell 註冊應用程式

> [!NOTE]
> 目前 Azure AD PowerShell 只會使用下列支援的帳戶類型來建立應用程式：
>
> - MyOrg （僅限此組織目錄中的帳戶）
> - AnyOrg （在任何組織目錄中的帳戶）。
>
> 如果您想要建立使用其個人 Microsoft 帳戶（例如 Skype、XBox、Outlook.com）登入使用者的應用程式，您可以先建立多租使用者應用程式（支援的帳戶類型 = 任何組織目錄中的帳戶），然後變更Azure 入口網站的應用程式資訊清單中的 `signInAudience` 屬性。 這會在 ASP.NET Core 教學課程的步驟[1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant)中詳細說明（並可通用於任何語言的 web 應用程式）。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼設定](scenario-web-app-sign-user-app-configuration.md)
