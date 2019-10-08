---
title: 教學課程 - 在原生用戶端應用程式中啟用驗證 - Azure Active Directory B2C | Microsoft Docs
description: 關於如何使用 Azure Active Directory B2C 為 .NET 傳統型應用程式提供使用者登入的教學課程。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326319"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 在原生用戶端應用程式中啟用驗證

本教學課程將說明如何使用 Azure Active Directory B2C (Azure AD B2C) 在 Windows Presentation Foundation (WPF) 傳統型應用程式中登入和註冊使用者。 Azure AD B2C 可讓您的應用程式使用開放式標準通訊協定向社交帳戶、企業帳戶和 Azure Active Directory 帳戶進行驗證。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增原生用戶端應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

- [建立使用者流程](tutorial-create-user-flows.md)以在您的應用程式中達成使用者體驗。
- 安裝 [Visual Studio 2019](https://www.visualstudio.com/downloads/)，其中包含 **.NET 傳統型開發**以及 **ASP.NET 和 Web 開發**工作負載。

## <a name="add-the-native-client-application"></a>新增原生用戶端應用程式

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

記錄 [應用程式識別碼]  ，以便在稍後的步驟中使用。

## <a name="configure-the-sample"></a>設定範例

在本教學課程中，您會設定可從 GitHub 下載的範例。 範例 WPF 傳統型應用程式會示範如何在 Azure AD B2C 中註冊、登入及呼叫受保護的 Web API。 [下載 Zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)、[瀏覽存放庫](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)，或從 GitHub 複製範例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

若要變更應用程式設定，以您的租用戶名稱取代 `<your-tenant-name>`，並以您記錄的應用程式識別碼取代 `<application-ID`。

1. 在 Visual Studio 中開啟 `active-directory-b2c-wpf` 解決方案。
2. 在 `active-directory-b2c-wpf` 專案中，開啟 **App.xaml.cs** 檔案並進行下列更新：

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. 使用您建立的使用者流程名稱來更新 **PolicySignUpSignIn** 變數。

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>執行範例

按 **F5** 以建置並執行範例。

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

1. 按一下 [登入]  以使用者身分註冊。 這會使用 **B2C_1_signupsignin1** 使用者流程。
2. Azure AD B2C 會顯示含有註冊連結的登入頁面。 由於您還沒有帳戶，因此請按一下 [立即註冊]  連結。
3. 註冊工作流程會顯示一個使用電子郵件地址來收集並驗證使用者身分識別的頁面。 註冊工作流程也會收集使用者的密碼，以及在使用者流程中定義的要求屬性。

    請使用有效的電子郵件地址，並使用驗證碼進行驗證。 設定密碼。 輸入要求的屬性值。

    ![註冊頁面顯示為登入/註冊工作流程的一部分](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. 按一下 [建立]  ，在 Azure AD B2C 租用戶中建立本機帳戶。

現在，使用者可以使用電子郵件地址登入並使用傳統型應用程式。

> [!NOTE]
> 如果您按一下 [呼叫 API]  按鈕，您會收到「未經授權」錯誤。 因為您嘗試從示範租用戶存取資源，所以會收到這個錯誤。 因為您的存取權杖只對您的 Azure AD 租用戶有效，所以此 API 呼叫未經授權。 繼續進行下一個教學課程，為您的租用戶建立受保護的 Web API。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增原生用戶端應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

> [!div class="nextstepaction"]
> [教學課程：使用 Azure Active Directory B2C 授與從傳統型應用程式存取 Node.js Web API 的權限](active-directory-b2c-tutorials-spa-webapi.md)
