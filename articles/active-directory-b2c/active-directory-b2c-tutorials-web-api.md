---
title: 教學課程 - 授與存取 ASP.NET Web API 的權限 - Azure Active Directory B2C | Microsoft Docs
description: 關於如何使用 Active Directory B2C 來保護 ASP.NET Web API，以及如何從 ASP.NET Web 應用程式加以呼叫的教學課程。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cc4db0f2fe8f5db41f6e8332a398029bd105f3af
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756337"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 授與存取 ASP.NET Web API 的權限

本教學課程說明如何從 ASP.NET Web 應用程式呼叫 Azure Active Directory (Azure AD) B2C 中受保護的 Web API 資源。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 設定範例以使用應用程式

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

完成以下教學課程中的步驟和必要條件：[教學課程：使用 Azure Active Directory B2C 在 Web 應用程式中啟用驗證](active-directory-b2c-tutorials-web-app.md)。

## <a name="add-a-web-api-application"></a>新增 Web API 應用程式

Web API 資源必須先在您的租用戶中註冊，才能接受及回應受到用戶端應用程式保護而提供存取權杖的資源要求。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式]，然後選取 [新增]。
5. 輸入應用程式的名稱。 例如，*webapi1*。
6. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
7. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `https://localhost:44332` 接聽。
8. 針對**應用程式識別碼 URI**，請輸入您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 例如： `https://contosotenant.onmicrosoft.com/api`。
9. 按一下頁面底部的 [新增] 。
10. 在 [屬性] 頁面上，記錄您會在設定 Web 應用程式時使用的應用程式識別碼。

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，Web API 的使用者可以同時具有讀取和寫入權限，Web API 的使用者也可能只具有讀取權限。 在本教學課程中，您會使用範圍來定義 Web API 的讀取和寫入權限。

1. 選取 [應用程式]，然後選取 [webapi1]。
2. 選取 [發佈的範圍]。
3. 針對 [範圍]，輸入 `Hello.Read`，以及輸入 `Read access to hello` 作為描述。
4. 針對 [範圍]，輸入 `Hello.Write`，以及輸入 `Write access to hello` 作為描述。
5. 按一下 [檔案] 。

發佈的範圍可以用來為用戶端應用程式授與對 Web API 的權限。

## <a name="grant-permissions"></a>授與權限

若要從應用程式呼叫受保護的 Web API，您必須為應用程式授與對 API 的權限。 在必要的教學課程中，您已在 Azure AD B2C 中建立名為 *webapp1* 的 Web 應用程式。 您可使用此應用程式來呼叫 Web API。

1. 選取 [應用程式]，然後選取 [webapp1]。
2. 選取 [API 存取]，然後選取 [新增]。
3. 在 [選取 API] 下拉式清單中，選取 [webapi1]。
4. 在 [選取範圍] 下拉式清單中，選取您先前定義的 **Hello.Read** 和 **Hello.Write** 範圍。
5. 按一下 [確定]。

您的應用程式會進行註冊，以呼叫受保護的 Web API。 使用者會透過 Azure AD B2C 進行驗證以使用應用程式。 該應用程式會從 Azure AD B2C 取得授權授與，以存取受保護的 Web API。

## <a name="configure-the-sample"></a>設定範例

現在已註冊 Web API 且已定義範圍，您可設定 Web API 以使用您的 Azure AD B2C 租用戶。 在本教學課程中，您會設定範例 Web API。 您在必要的教學課程中下載的專案包含範例 Web API。

範例方案中有兩個專案：

範例解決方案中有下列兩個專案：

- **TaskWebApp** - 建立和編輯工作清單。 此範例會使用**註冊或登入**使用者流程來註冊或登入使用者。
- **TaskService** - 支援建立、讀取、更新和刪除工作清單功能。 此 API 會受到 Azure AD B2C 的保護，且由 TaskWebApp 呼叫。

### <a name="configure-the-web-application"></a>設定 Web 應用程式

1. 在 Visual Studio 中開啟 **B2C-WebAPI-DotNet** 方案。
2. 開啟 **TaskWebApp** 專案中的 **Web.config**。
3. 若要在本機執行 API，請使用 **api:TaskServiceUrl** 的 localhost 設定。 變更 Web.config，如下所示： 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. 設定 API 的 URI。 這是 Web 應用程式用來進行 API 要求的 URI。 此外，請設定要求的權限。

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>設定 Web API

1. 開啟 **TaskService** 專案中的 **Web.config**。
2. 設定 API 以使用您的租用戶。

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. 將用戶端識別碼設為您的 API 已註冊的應用程式識別碼。

    ```C#
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. 使用註冊和登入使用者流程的名稱來更新使用者流程設定。

    ```C#
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. 設定比對您在入口網站中建立的項目時所使用的範圍設定。

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>執行範例

您必須同時執行 **TaskWebApp** 和 **TaskService** 專案。 

1. 在方案總管中，以滑鼠右鍵按一下方案，然後選取 [設定啟始專案]。 
2. 選取 [多個啟始專案]。
3. 將兩個專案的 [動作] 變更為 [啟動]。
4. 按一下 [確定] 以儲存組態。
5. 按 **F5** 鍵執行兩個應用程式。 這兩個應用程式會分別在其本身的瀏覽器索引標籤中開啟。`https://localhost:44316/` 為 Web 應用程式。
    `https://localhost:44332/` 為 Web API。

6. 在 Web 應用程式中，按一下 [註冊 / 登入]，以登入 Web 應用程式。 使用您先前建立的帳戶。 
7. 登入之後，按一下 [待辦事項清單] 並建立待辦事項清單項目。

在您建立待辦事項清單項目時，Web 應用程式會對 Web API 提出要求，以產生待辦事項清單項目。 您受保護的 Web 應用程式會呼叫您的 Azure AD B2C 租用戶中受保護的 Web API。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 設定範例以使用應用程式

> [!div class="nextstepaction"]
> [教學課程：在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式](tutorial-add-identity-providers.md)
