---
title: 在 國家/地區雲端-Microsoft 身分識別平台使用 Microsoft Authentication Library (MSAL)
description: Microsoft Authentication Library (MSAL) 可讓應用程式開發人員若要取得權杖以呼叫受保護的 web Api。 這些 web Api 可以是 Microsoft Graph、 其他 Microsoft Api、 協力廠商 web Api 或您自己的 web API。 MSAL 支援多種應用程式架構和平台。
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235272"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>在國家雲端環境中使用 MSAL

[國家/地區雲端](authentication-national-cloud.md)是 Azure 的實體隔離的執行個體。 Azure 的這些區域有助於確保資料存放區、 主權範圍和合規性需求會遵守地理界限內。

除了 Microsoft 的全球雲端，Microsoft Authentication Library (MSAL) 可讓應用程式開發人員在國家雲端來取得權杖以驗證並呼叫受保護的 web Api。 Microsoft Graph 或其他 Microsoft Api，可以使用這些 web Api。

全域定域機組，包括 Azure Active Directory (Azure AD) 是在下列國家/地區雲端部署：  

- Azure Government
- Azure China 21Vianet
- Azure Germany

本指南示範如何登入工作和學校帳戶、 取得存取權杖，並呼叫 Microsoft Graph API [Azure Government 雲端](https://azure.microsoft.com/global-infrastructure/government/)環境。

## <a name="prerequisites"></a>先決條件

在開始之前，請確定您符合下列先決條件。

### <a name="choose-the-appropriate-identities"></a>選擇適當的身分識別

[Azure Government](https://docs.microsoft.com/azure/azure-government/)應用程式可以使用 Azure AD 政府的身分識別與 Azure AD 公開的身分識別來驗證使用者。 因為您可以使用任何這些身分識別，您必須決定您的案例，您應該選擇哪一個授權單位端點：

- Azure AD 公用：如果貴組織已有的公用 Azure AD 租用戶支援 Office 365 （公用或 GCC） 或另一個應用程式，通常使用。
- Azure AD 政府服務：如果您的組織已經具備 Azure AD 政府租用戶支援 Office 365 （GCC High 或 DoD），或正在建立新的租用戶 Azure AD 政府機關內，常用。

在決定之後，特殊的考量是您用來執行應用程式註冊。 如果您選擇 Azure Government 應用程式的 Azure AD 公開的身分識別，您必須在公用 Azure AD 租用戶中註冊應用程式。

### <a name="get-an-azure-government-subscription"></a>取得 Azure Government 訂用帳戶

若要取得 Azure Government 訂用帳戶，請參閱[管理和連接到您的訂用帳戶，在 Azure Government 中](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)。

如果您還沒有 Azure Government 訂用帳戶，建立[免費帳戶](https://azure.microsoft.com/global-infrastructure/government/request/)開始之前。

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>步驟 1：註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.us/)。
    
   若要尋找其他國家/地區雲端的 Azure 入口網站的端點，請參閱[應用程式註冊端點](authentication-national-cloud.md#app-registration-endpoints)。

1. 如果您的帳戶可讓您存取多個租用戶、 右上角選取您的帳戶和設定您的入口網站工作階段所需的 Azure ad 租用戶。
1. 移至[應用程式註冊](https://aka.ms/ra/ff)適用於開發人員的 Microsoft 身分識別平台上的頁面。
1. [註冊應用程式]  頁面出現時，輸入您應用程式的名稱。
1. 底下**支援的帳戶類型**，選取**任何組織的目錄中的帳戶**。
1. 在 **重新導向 URI**區段中，選取**Web**平台和應用程式的 URL 的值會根據您的 web 伺服器的集合。 如何設定和取得 Visual Studio 和節點中的重新導向 URL，請參閱下一節中的指示。
1. 選取 [註冊]  。
1. 在應用程式 [概觀]  頁面上，記下 [應用程式 (用戶端) 識別碼]  值。
1. 本教學課程要求您啟用[隱含授與流程](v2-oauth2-implicit-grant-flow.md)。 在所註冊應用程式的左側窗格中，選取 [驗證]  。
1. 在 [進階設定]  的 [隱含授與]  底下，選取 [識別碼權杖]  和 [存取權杖]  核取方塊。 因為此應用程式需要使用者登入，以及呼叫 API，所需識別碼權杖和存取權杖。
1. 選取 [ **儲存**]。

### <a name="step-2--set-up-your-web-server-or-project"></a>步驟 2：設定您的網頁伺服器或專案

- [下載專案檔](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)的本機 web 伺服器，例如節點。

  或

- [下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。

然後跳至[設定您的 JavaScript SPA](#step-4-configure-your-javascript-spa)來執行它之前先設定程式碼範例。

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>步驟 3：使用 Microsoft Authentication Library 登入使用者

中的後續步驟[JavaScript 教學課程](tutorial-v2-javascript-spa.md#create-your-project)來建立您的專案，並與登入使用者的 MSAL 整合。

### <a name="step-4-configure-your-javascript-spa"></a>步驟 4：設定您的 JavaScript SPA

在於專案設定期間建立的 `index.html` 檔案中，新增應用程式註冊資訊。 在 `index.html` 檔案主體頂端的 `<script></script>` 標記內，新增下列程式碼：

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

在該程式碼：

- `Enter_the_Application_Id_here` 已**應用程式 （用戶端） 識別碼**註冊，讓您的應用程式的值。
- `Enter_the_Tenant_Info_Here` 設定為下列選項之一：
    - 如果您的應用程式支援**此組織的目錄中的帳戶**，這個值取代為租用戶識別碼或租用戶名稱 (例如 contoso.microsoft.com)。
    - 如果您的應用程式支援**任何組織的目錄中的帳戶**，將此值取代`organizations`。
    
    若要尋找所有國家/地區的雲端驗證端點，請參閱[Azure AD 驗證端點](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)。

    > [!NOTE]
    > 在 國家/地區雲端中不支援個人 Microsoft 帳戶。
  
- `graphEndpoint` 是適用於美國政府的 Microsoft 雲端的 Microsoft Graph 端點。

   若要尋找 Microsoft Graph 端點的所有國家/地區雲端，請參閱[國家/地區雲端中的 Microsoft Graph 端點](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

## <a name="net"></a>.NET

您可以使用 MSAL.NET 登入使用者、 取得權杖，並呼叫 Microsoft Graph API 在國家雲端。

下列教學課程會示範如何建置.NET Core 2.2 MVC Web 應用程式。 應用程式使用 OpenID Connect 登入使用者所屬國家雲端的組織的公司和學校帳戶。

- 若要將使用者登入，並取得權杖，請遵循[本教學課程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)。
- 若要呼叫 Microsoft Graph API，請遵循[本教學課程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)。

## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)