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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bdf04014d7b0382913c0a4094f7474686658441
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086708"
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

### <a name="register-an-app-using-azure-portal"></a>使用 Azure 入口網站註冊應用程式

> [!NOTE]
> 要使用的入口網站會根據您的應用程式是在 Microsoft Azure 公用雲端或在全國或主權雲端中執行而有所不同。 如需詳細資訊，請參閱[國家/地區](./authentication-national-cloud.md#app-registration-endpoints)雲端

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。 或者，登入選擇的國家雲端 Azure 入口網站。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 在左側導覽窗格中，選取 [ **Azure Active Directory** ] 服務，然後選取 [**應用程式註冊** > **新增註冊**]。
1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   1. 為您的應用程式選擇支援的帳戶類型（請參閱[支援的帳戶類型](./v2-supported-account-types.md)）
   1. 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `AspNetCore-WebApp`。
   1. 在 [重新**導向 URI**] 中，新增應用程式類型和 URI 目的地，以在成功驗證後接受傳回的權杖回應。 例如： `https://localhost:44321/` 。  選取 [註冊]。
1. 選取 [驗證] 功能表，然後新增下列資訊：
   1. 在 [**回復 URL**] `https://localhost:44321/signin-oidc`中，新增。
   1. 在 [進階設定] 區段中，將 [登出 URL]設定為 `https://localhost:44321/signout-oidc`。
   1. 在 [隱含授與] 底下，核取 [識別碼權杖]。
   1. 選取 [儲存]。

### <a name="register-an-app-using-powershell"></a>使用 PowerShell 註冊應用程式

> [!NOTE]
> 目前 Azure AD PowerShell 只會使用下列支援的帳戶類型來建立應用程式：
>
> - MyOrg （僅限此組織目錄中的帳戶）
> - AnyOrg （在任何組織目錄中的帳戶）。
>
> 如果您想要建立使用其個人 Microsoft 帳戶（例如 Skype、XBox、Outlook.com）登入使用者的應用程式，您可以先建立多租使用者應用程式（支援的帳戶類型 = 任何組織目錄中的帳戶），然後變更Azure 入口網站`signInAudience`的應用程式資訊清單中的屬性。 這會在 ASP.NET Core 教學課程的步驟[1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant)中詳細說明（並可通用於任何語言的 web 應用程式）。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼設定](scenario-web-app-sign-user-app-configuration.md)
