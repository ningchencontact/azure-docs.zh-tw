---
title: 教學課程 - 在 Web 應用程式中啟用驗證 - Azure Active Directory B2C | Microsoft Docs
description: 關於如何使用 Azure Active Directory B2C 為 ASP.NET Web 應用程式提供使用者登入的教學課程。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 243df4457263a3653c501c7a409d2c3a77ee07e1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690158"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 在 Web 應用程式中啟用驗證

本教學課程將說明如何使用 Azure Active Directory (Azure AD) B2C 在 ASP.NET Web 應用程式中登入和註冊使用者。 Azure AD B2C 可讓您的應用程式使用開放式標準通訊協定向社交帳戶、企業帳戶和 Azure Active Directory 帳戶進行驗證。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

- [建立使用者流程](tutorial-create-user-flows.md)以在您的應用程式中達成使用者體驗。 
- 安裝包含 **ASP.NET 和 Web 開發**工作負載的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="update-the-application"></a>更新應用程式

在已完成的必要教學課程中，您在 Azure AD B2C 中新增了 Web 應用程式。 若要啟用範例與本教學課程中的通訊，您需要新增 Azure AD B2C 中應用程式的重新導向 URI。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式]，然後選取 [webapp1] 應用程式。
5. 在 [回覆 URL] 下方，新增 `https://localhost:44316`。
6. 選取 [ **儲存**]。
7. 在 [屬性] 頁面上，記錄您會在設定 Web 應用程式時使用的應用程式識別碼。
8. 依序選取 [金鑰]、[產生金鑰] 和 [儲存]。 記錄您會在設定 Web 應用程式時使用的金鑰。

## <a name="configure-the-sample"></a>設定範例

在本教學課程中，您會設定可從 GitHub 下載的範例。 此範例會使用 ASP.NET 來提供簡單的待辦事項清單。 此範例會使用 [Microsoft OWIN 中介軟體元件](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)。 [下載 zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)，或從 GitHub 複製範例。 請務必要擷取資料夾中的檔案範例，路徑的總字元長度少於 260。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

範例解決方案中有下列兩個專案：

- **TaskWebApp** - 建立和編輯工作清單。 此範例會使用**註冊或登入**使用者流程來註冊或登入使用者。
- **TaskService** - 支援建立、讀取、更新和刪除工作清單功能。 此 API 會受到 Azure AD B2C 的保護，且由 TaskWebApp 呼叫。

您可將範例變更為使用租用戶中註冊的應用程式，其中包含您先前記錄下來的應用程式識別碼和金鑰。 您也會設定您所建立的使用者流程。 此範例會在 Web.config 檔案中將組態值定義為設定。 若要變更設定：

1. 在 Visual Studio 中開啟 **B2C-WebAPI-DotNet** 方案。
2. 在 **TaskWebApp** 專案中，開啟 **Web.config** 檔案。 將 `ida:Tenant` 的值更換為您所建立的租用戶名稱。 將 `ida:ClientId` 的值更換為您所記錄下來的應用程式識別碼。 將 `ida:ClientSecret` 的值更換為您所記錄下來的金鑰。
3. 在 **Web.config** 檔案中，將 `ida:SignUpSignInPolicyId` 的值更換為 `b2c_1_signupsignin1`。 將 `ida:EditProfilePolicyId` 的值更換為 `b2c_1_profileediting1`。 將 `ida:ResetPasswordPolicyId` 的值更換為 `b2c_1_passwordreset1`。

## <a name="run-the-sample"></a>執行範例

1. 在方案總管中，以滑鼠右鍵按一下 **TaskWebApp** 專案，然後按一下 [設為起始專案]。
2. 按 **F5**。 預設瀏覽器隨即啟動，並顯示本機網站位址 `https://localhost:44316/`。

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

1. 按一下 [註冊 / 登入] 以應用程式的使用者身分註冊。 便會使用 **b2c_1_signupsignin1** 使用者流程。
2. Azure AD B2C 會顯示含有註冊連結的登入頁面。 由於您還沒有帳戶，因此選取 [立即註冊]。 註冊工作流程會顯示一個使用電子郵件地址來收集並驗證使用者身分識別的頁面。 註冊工作流程也會收集使用者的密碼，以及在使用者流程中定義的要求屬性。
3. 請使用有效的電子郵件地址，並使用驗證碼進行驗證。 設定密碼。 輸入要求的屬性值。 

    ![註冊工作流程](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. 按一下 [建立]，在 Azure AD B2C 租用戶中建立本機帳戶。

現在，使用者已可使用電子郵件地址登入並使用 Web 應用程式。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

> [!div class="nextstepaction"]
> [教學課程：使用 Azure Active Directory B2C 保護 ASP.NET Web API](active-directory-b2c-tutorials-web-api.md)
