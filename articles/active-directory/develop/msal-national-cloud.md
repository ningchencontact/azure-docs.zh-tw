---
title: 在 國家/地區雲端-Microsoft 身分識別平台使用 Microsoft Authentication Library (MSAL)
description: Microsoft Authentication Library (MSAL) 可讓應用程式開發人員若要取得權杖以呼叫受保護的 web Api。 這些 web Api 可以是 Microsoft Graph、 其他 Microsoft API，第三方 web Api 或您自己的 web API。 MSAL 也支援多個應用程式架構及平台。
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
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075786"
---
# <a name="use-msal-in-national-cloud-environment"></a>在國家雲端環境中使用 MSAL

[國家/地區雲端](authentication-national-cloud.md)是 Azure 的實體隔離的執行個體。 這些 Azure 區域的設計目的，是要確保符合地理界限內的資料落地、主權及合規性需求。

除了 Microsoft 全球雲端，Microsoft Authentication Library (MSAL) 也可讓應用程式開發人員在國家雲端來取得權杖以驗證並呼叫受保護的 web Api。 Microsoft Graph 或其他 Microsoft API，可以使用這些 web Api。

全域定域機組，包括 Azure Active Directory (Azure AD) 是在下列國家/地區雲端部署：  

- Azure US Government
- Azure China 21Vianet
- Azure Germany

本指南示範如何在公司和學校帳戶登入、 取得存取權杖及呼叫 Microsoft Graph API 中[適用於美國政府的 Microsoft 雲端](https://azure.microsoft.com/global-infrastructure/government/)環境。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您符合下列先決條件。

### <a name="choose-the-appropriate-identities"></a>選擇適當的身分識別

[Azure Government](https://docs.microsoft.com/azure/azure-government/)應用程式可以使用 Azure AD 政府的身分識別，以及與 Azure AD 公開的身分識別來驗證使用者。 因為您可以使用任何這些身分識別，您需要了解，並決定您的案例，您應該選擇哪一個授權單位端點：

- Azure AD 公用：如果貴組織已有的公用 Azure AD 租用戶支援 Office 365 （公用或 GCC） 或另一個應用程式，通常使用。
- Azure AD 政府服務：如果您的組織已擁有 Azure AD 政府租用戶支援 Office 365 （GCC High 或 DoD），或要在 Azure AD 政府機構中建立新的租用戶，常使用。

一旦決定後，特殊的考量是您用來執行應用程式註冊。 如果您選擇 Azure Government 應用程式的 Azure AD 公開的身分識別，您必須在公用 Azure AD 租用戶中註冊應用程式。

### <a name="get-an-azure-government-subscription"></a>取得 Azure Government 訂用帳戶

- 若要取得 Azure Government 訂用帳戶，請參閱[管理，以及連接到您的訂用帳戶，在 Azure Government 中](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)。
- 如果您還沒有 Azure Government 訂用帳戶，建立[免費帳戶](https://azure.microsoft.com/global-infrastructure/government/request/)開始之前。

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>步驟 1：註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.us/)以註冊應用程式。
    1. 若要尋找其他國家/地區雲端的 Azure 入口網站的端點，請參閱[應用程式註冊端點](authentication-national-cloud.md#app-registration-endpoints)

1. 如果您的帳戶可讓您存取多個租用戶、 右上角選取您的帳戶和設定您的入口網站工作階段所需的 Azure ad 租用戶。
1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://aka.ms/ra/ff)頁面。
1. [註冊應用程式] 頁面出現時，輸入您應用程式的名稱。
1. 底下**支援的帳戶類型**，選取**任何組織的目錄中的帳戶**。
1. 在 [重新導向 URI] 區段底下，選取 [Web] 平台，然後根據您的 Web 伺服器，將值設定為應用程式的 URL。 如需有關如何設定及取得 Visual Studio 和 Node 中重新導向 URL 的相關指示，請參閱下列各節。
1. 完成時，選取 [註冊]。
1. 在應用程式 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 值。
1. 本教學課程需要[隱含授與流程](v2-oauth2-implicit-grant-flow.md)啟用。 在所註冊應用程式的左側導覽窗格中，選取 [驗證]。
1. 在 [進階設定] 的 [隱含授與] 底下，啟用 [識別碼權杖] 和 [存取權杖] 核取方塊。 識別碼權杖和存取權杖都是必要權杖，因為此應用程式必須將使用者登入並呼叫 API。
1. 選取 [ **儲存**]。

### <a name="step-2--set-up-your-web-server-or-project"></a>步驟 2：設定您的 web 伺服器或專案

- [下載專案檔](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)的本機 web 伺服器，例如節點。

  或

- [下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。

然後跳至['設定您的 JavaScript SPA'](#step-4-configure-your-javascript-spa)來執行它之前先設定程式碼範例。

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>步驟 3：使用 Microsoft Authentication Library (MSAL) 登入使用者

中的後續步驟[Javascript 教學課程](tutorial-v2-javascript-spa.md#create-your-project)建立您的專案，並整合與 Microsoft Authentication Library (MSAL) 來登入使用者。

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

其中：

- `Enter_the_Application_Id_here` - 是您註冊的應用程式所具備的**應用程式 (用戶端) 識別碼**。
- `Enter_the_Tenant_Info_Here` - 會設為下列其中一個選項：
    - 如果您的應用程式支援**此組織的目錄中的帳戶**，將此值取代**租用戶識別碼**或是**租用戶名稱**（比方說，contoso.microsoft.com)
    - 如果您的應用程式支援 [任何組織目錄中的帳戶]，請將此值取代為 `organizations`
    -  若要尋找所有國家/地區的雲端驗證端點，請參閱[Azure AD 驗證端點](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

     > [!NOTE]
     > 在 國家/地區雲端中不支援個人 Microsoft 帳戶 (MSA) 案例。
  
-   `graphEndpoint` -是適用於美國政府的 Microsoft 雲端的 Microsoft Graph 端點。
    -  若要尋找 Microsoft Graph 端點的所有國家/地區雲端，請參閱[國家雲端中的 Microsoft Graph 端點](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="net"></a>.NET

MSAL.NET 可讓您將使用者登入、 取得權杖及呼叫 Microsoft Graph API 在國家雲端。

下列教學課程會示範如何建置使用 OpenID Connect 登入使用者使用他們的 '公司和學校' 帳戶國家/地區雲端屬於其組織中的.NET Core 2.2 MVC Web 應用程式。

- 若要將使用者登入，並取得權杖，請遵循[本教學課程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)。
- 若要呼叫 Microsoft Graph API，請遵循[本教學課程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)。

## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)