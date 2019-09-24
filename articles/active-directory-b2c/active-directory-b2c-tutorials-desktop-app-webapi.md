---
title: 教學課程 - 授與從傳統型應用程式存取 Node.js Web API 的權限 - Azure Active Directory B2C | Microsoft Docs
description: 關於如何使用 Active Directory B2C 來保護 Node.js Web API，以及如何從 .NET 傳統型應用程式加以呼叫的教學課程。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 75469d4522cea2914e0f69d5aa1850e468cb0d50
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064843"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 授與從傳統型應用程式存取 Node.js Web API 的權限

本教學課程將說明如何從 Windows Presentation Foundation (WPF) 傳統型應用程式呼叫 Azure Active Directory B2C (Azure AD B2C) 所保護的 Node.js Web API 資源。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 更新範例以使用應用程式

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

完成以下教學課程中的步驟和必要條件：[教學課程：讓傳統型應用程式能夠使用 Azure Active Directory B2C 向帳戶進行驗證](active-directory-b2c-tutorials-desktop-app.md)。

## <a name="add-a-web-api-application"></a>新增 Web API 應用程式

Web API 資源必須先在您的租用戶中註冊，才能接受及回應受到用戶端應用程式保護而提供存取權杖的資源要求。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
4. 選取 [應用程式]  ，然後選取 [新增]  。
5. 輸入應用程式的名稱。 例如，*webapi1*。
6. 針對 [包含 Web 應用程式/Web API]  和 [允許隱含流程]  ，選取 [是]  。
7. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `https://localhost:5000` 接聽。
8. 針對**應用程式識別碼 URI**，請輸入您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 例如： `https://contosotenant.onmicrosoft.com/api` 。
9. 按一下頁面底部的 [新增]  。
10. 在 [屬性] 頁面上，記錄您會在設定 Web 應用程式時使用的應用程式識別碼。

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，有些使用者可能同時具有讀取和寫入權限，而有些則可能只有唯讀權限。 在本教學課程中，您會定義 Web API 的讀取權限。

1. 選取 [應用程式]  ，然後選取 [webapi1]  。
2. 選取 [發佈的範圍]  。
3. 針對 [範圍]  ，輸入 `Hello.Read`，以及輸入 `Read access to hello` 作為描述。
4. 針對 [範圍]  ，輸入 `Hello.Write`，以及輸入 `Write access to hello` 作為描述。
5. 按一下 [檔案]  。

發佈的範圍可以用來為用戶端應用程式授與對 Web API 的權限。

## <a name="grant-permissions"></a>授與權限

若要從應用程式呼叫受保護的 Web API，您必須為應用程式授與對 API 的權限。 在必要的教學課程中，您已在 Azure AD B2C 中建立名為 *app1* 的 Web 應用程式。 您可使用此應用程式來呼叫 Web API。

1. 選取 [應用程式]  ，然後選取 [nativeapp1]  。
2. 選取 [API 存取]  ，然後選取 [新增]  。
3. 在 [選取 API]  下拉式清單中，選取 [webapi1]  。
4. 在 [選取範圍]  下拉式清單中，選取您先前定義的 **Hello.Read** 和 **Hello.Write** 範圍。
5. 按一下 [確定]  。

使用者會向 Azure AD B2C 進行驗證以使用該 WPF 傳統型應用程式。 該傳統型應用程式會從 Azure AD B2C 取得授權授與，以存取受保護的 Web API。

## <a name="configure-the-sample"></a>設定範例

現在已註冊 Web API 且已定義範圍，您可設定 Web API 程式碼以使用您的 Azure AD B2C 租用戶。 在本教學課程中，您會設定可從 GitHub 下載的範例 Node.js Web 應用程式。

[下載 zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)，或從 GitHub 複製範例 Web 應用程式。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Node.js Web API 範例會使用 Passport.js 程式庫，讓 Azure AD B2C 能夠保護 API 的呼叫。

1. 開啟 `index.js` 檔案。
2. 使用 Azure AD B2C 租用戶註冊資訊設定此範例。 變更下列幾行程式碼：

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>執行範例

1. 啟動 Node.js 命令提示字元。
2. 切換至包含 Node.js 範例的目錄。 例如 `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. 執行下列命令：
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>執行桌面應用程式

1. 在 Visual Studio 中開啟 **active-directory-b2c-wpf** 方案。
2. 按 **F5** 以執行傳統型應用程式。
3. 使用[在傳統型應用程式中使用 Azure Active Directory B2C 進行使用者驗證](active-directory-b2c-tutorials-desktop-app.md)教學課程中使用的電子郵件地址和密碼進行登入。
4. 按一下 [呼叫 API]  按鈕。

傳統型應用程式會對 Web API 提出要求，並取得包含已登入使用者顯示名稱的回應。 您受保護的傳統型應用程式會呼叫您的 Azure AD B2C 租用戶中受保護的 Web API。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 更新範例以使用應用程式

> [!div class="nextstepaction"]
> [教學課程：在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式](tutorial-add-identity-providers.md)
