---
title: 教學課程 - 在單頁應用程式中啟用驗證 - Azure Active Directory B2C | Microsoft Docs
description: 關於如何使用 Azure Active Directory B2C 為單頁應用程式 (JavaScript) 提供使用者登入的教學課程。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9541d635ff69444459470cf1e486568a58af0a1e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730170"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 在單頁應用程式中啟用驗證

本教學課程將說明如何使用 Azure Active Directory (Azure AD) B2C 在單頁應用程式 (SPA) 中登入和註冊使用者。 Azure AD B2C 可讓您的應用程式使用開放式標準通訊協定向社交帳戶、企業帳戶和 Azure Active Directory 帳戶進行驗證。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* [建立使用者流程](tutorial-create-user-flows.md)以在您的應用程式中達成使用者體驗。 
* 安裝包含 **ASP.NET 和 Web 開發**工作負載的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* 安裝 [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更新版本
* 安裝 [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>更新應用程式

在已完成的必要教學課程中，您在 Azure AD B2C 中新增了 Web 應用程式。 若要啟用範例與教學課程中的通訊，您需要新增 Azure AD B2C 中應用程式的重新導向 URI。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式]，然後選取 [webapp1] 應用程式。
5. 在 [回覆 URL] 下方，新增 `http://localhost:6420`。
6. 選取 [ **儲存**]。
7. 在 [屬性] 頁面上，記錄您會在設定 Web 應用程式時使用的應用程式識別碼。
8. 依序選取 [金鑰]、[產生金鑰] 和 [儲存]。 記錄您會在設定 Web 應用程式時使用的金鑰。

## <a name="configure-the-sample"></a>設定範例

在本教學課程中，您會設定可從 GitHub 下載的範例。 此範例會示範單頁應用程式如何使用 Azure AD B2C 進行使用者註冊、登入及呼叫受保護的 Web API。

[下載 zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)，或從 GitHub 複製範例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

若要變更設定：

1. 開啟範例中的 `index.html` 檔案。
2. 使用您稍早記錄的應用程式識別碼與金鑰設定範例。 變更下列幾行程式碼，做法是將其中的值取代為您的目錄和 API 名稱：

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    本教學課程中使用的使用者流程名稱是 **B2C_1_signupsignin1**。 如果您使用不同的使用者流程名稱，請在 `authority` 值中使用您的使用者流程名稱。

## <a name="run-the-sample"></a>執行範例

1. 啟動 Node.js 命令提示字元。
2. 切換至包含 Node.js 範例的目錄。 例如 `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. 執行下列命令：

    ```
    npm install && npm update
    node server.js
    ```

    主控台視窗會顯示裝載應用程式的連接埠號碼。
    
    ```
    Listening on port 6420...
    ```

4. 使用瀏覽器巡覽至位址 `http://localhost:6420` 以檢視應用程式。

此範例支援註冊、登入、設定檔編輯和密碼重設。 本教學課程特別說明使用者如何以電子郵件地址註冊。

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

1. 按一下 [登入] 以應用程式的使用者身分註冊。 這會使用您在上一個步驟中定義的 **B2C_1_signupsignin1** 使用者流程。
2. Azure AD B2C 會顯示含有註冊連結的登入頁面。 由於您還沒有帳戶，因此請按一下 [立即註冊] 連結。 
3. 註冊工作流程會顯示一個使用電子郵件地址來收集並驗證使用者身分識別的頁面。 註冊工作流程也會收集使用者的密碼，以及在使用者流程中定義的要求屬性。

    請使用有效的電子郵件地址，並使用驗證碼進行驗證。 設定密碼。 輸入要求的屬性值。 

    ![註冊工作流程](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. 按一下 [建立]，在 Azure AD B2C 目錄中建立本機帳戶。

現在，使用者可使用電子郵件地址登入並使用 SPA 應用程式。

> [!NOTE]
> 登入之後，應用程式會顯示「權限不足」錯誤。 因為您嘗試從示範目錄存取資源，所以會收到這個錯誤。 因為您的存取權杖只對您的 Azure AD 目錄有效，所以此 API 呼叫未經授權。 繼續進行下一個教學課程，為您的目錄建立受保護的 Web API。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

> [!div class="nextstepaction"]
> [教學課程：使用 Azure Active Directory B2C 授與從單頁應用程式存取 ASP.NET Core Web API 的權限](active-directory-b2c-tutorials-spa-webapi.md)
