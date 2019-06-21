---
title: Web 應用程式登入使用者 （應用程式註冊）-Microsoft 身分識別平台
description: 了解如何建置 web 應用程式登入使用者 （應用程式註冊）
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
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150232"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>登入使用者-應用程式註冊的 web 應用程式

此頁面說明登入使用者的專屬的 web 應用程式的應用程式註冊詳細資料。

若要註冊您的應用程式，您可以使用：

- [Web 應用程式快速入門](#register-an-app-using-the-quickstarts)-除了建立應用程式第一次更優質的體驗，在 Azure 入口網站中的快速入門包含名為按鈕**我進行這項變更**。 您可以使用此按鈕以設定所需，即使現有的應用程式的屬性。 您將需要調整這些屬性，以您自己的案例中的值。 特別是，您的應用程式的 web API URL 可能將會不同於建議的預設值，也會影響登出 URI。
- Azure 入口網站[手動註冊您的應用程式](#register-an-app-using-azure-portal)
- PowerShell 和命令列工具

## <a name="register-an-app-using-the-quickstarts"></a>註冊應用程式使用快速入門

如果您導覽至這個連結，您可以建立啟動程序建立您的 web 應用程式：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>註冊使用 Azure 入口網站應用程式

> [!NOTE]
> 不同取決於您的應用程式是在 Microsoft Azure 公用雲端，或國家或主權雲端中執行，如果使用入口網站。 如需詳細資訊，請參閱[國家/地區雲端](./authentication-national-cloud.md#app-registration-endpoints)

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。 或者，登入 Azure 入口網站選擇的國家雲端。
1. 如果您的帳戶可讓您存取多個租用戶、 右上角選取您的帳戶和設定您的入口網站工作階段所需的 Azure ad 租用戶。
1. 在左側導覽窗格中，選取**Azure Active Directory**服務，然後按**應用程式註冊** > **新註冊**。
1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
   1. 選擇您的應用程式的支援的帳戶類型 (請參閱[支援的帳戶類型](./v2-supported-account-types.md))
   1. 在 [名稱]  區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `AspNetCore-WebApp`。
   1. 在 **重新導向 URI**、 新增應用程式的類型，以及將會接受 URI 目的地成功驗證之後傳回權杖回應。 例如： `https://localhost:44321/`。  選取 [註冊]  。
1. 選取 [驗證]  功能表，然後新增下列資訊：
   1. 在 **回覆 URL**，新增`https://localhost:44321/signin-oidc`。
   1. 在 [進階設定]  區段中，將 [登出 URL]  設定為 `https://localhost:44321/signout-oidc`。
   1. 在 [隱含授與]  底下，核取 [識別碼權杖]  。
   1. 選取 [ **儲存**]。

### <a name="register-an-app-using-powershell"></a>註冊應用程式使用 PowerShell

> [!NOTE]
> 目前 Azure AD PowerShell 只會使用下列支援的帳戶類型會建立應用程式：
>
> - MyOrg （僅此組織目錄中的帳戶）
> - AnyOrg （任何組織的目錄中的帳戶）。
>
> 如果您想要建立應用程式的設定，該登入的使用者使用其個人的 Microsoft 帳戶 (例如 Skype、 XBox、 Outlook.com)，您可以先建立多租用戶應用程式 (支援的帳戶類型 = 任何組織的目錄中的帳戶)，然後變更`signInAudience`從 Azure 入口網站應用程式資訊清單中的屬性。 這所述步驟的詳細資料[1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant)的 ASP.NET Core 教學課程 （和任何語言中的 web 應用程式可通用化）。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼組態](scenario-web-app-sign-user-app-configuration.md)
