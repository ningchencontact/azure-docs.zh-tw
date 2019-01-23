---
title: 教學課程 - 在 Azure Active Directory B2C 中註冊應用程式 | Microsoft Docs
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中註冊您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 511e1e9f29e6ae7602a977819f5295f76236595d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248720"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中註冊您的應用程式

[應用程式](active-directory-b2c-apps.md)必須先在您管理的租用戶中完成註冊，才可以與 Azure Active Directory (Azure AD) B2C 互動。 本教學課程示範如何使用 Azure 入口網站註冊應用程式。

在本文中，您將了解：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 註冊 Web API
> * 註冊行動或原生應用程式

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

如果您尚未建立自己的 [Azure AD B2C 租用戶](tutorial-create-tenant.md)，請立即建立一個。 您可以使用現有的租用戶。

## <a name="register-a-web-application"></a>註冊 Web 應用程式

1. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。

    ![切換至訂用帳戶目錄](./media/tutorial-register-applications/switch-directories.png)

2. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
3. 選取 [應用程式]，然後選取 [新增]。

    ![新增應用程式](./media/tutorial-register-applications/add-application.png)

4. 輸入應用程式的名稱。 例如，*webapp1*。
5. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
6. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 例如，您可以將它設定為在本機的 `https://localhost:44316` 接聽。如果還不知道連接埠號碼，您可以輸入預留位置值，之後再變更。 若要用於測試，您可以將它設為 `https://jwt.ms`，這會顯示權杖內容以進行檢測。 針對本教學課程，請將它設為 `https://jwt.ms`。 

    回覆 URL 必須以配置 `https` 開頭，而且所有回覆 URL 值必須共用單一 DNS 網域。 例如，如果應用程式的回覆 URL 為 `https://login.contoso.com`，您可以新增它，如這個 URL 所示：`https://login.contoso.com/new`。 或者，您可以參照 `login.contoso.com` 的 DNS 子網域，例如 `https://new.login.contoso.com`。 如果您想要有以 `login-east.contoso.com` 和 `login-west.contoso.com` 作為回覆 URL 的應用程式，您必須依照以下順序新增這些回覆 URL：`https://contoso.com`、`https://login-east.contoso.com`、`https://login-west.contoso.com`。 您可以新增後面兩個，因為它們是第一個回覆 URL (`contoso.com`) 的子網域。

7. 按一下頁面底部的 [新增] 。

    ![設定應用程式屬性](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>建立用戶端密碼

如果您的應用程式會交換權杖的程式碼，您必須建立應用程式密碼。

1. 選取 [金鑰]，然後按一下 [產生金鑰]。

    ![產生金鑰](./media/tutorial-register-applications/generate-keys.png)

2. 選取 [儲存] 以檢視金鑰。 請記下 [應用程式金鑰] 值。 您可以使用此值，作為應用程式程式碼中的應用程式祕密。

    ![儲存金鑰](./media/tutorial-register-applications/save-key.png)
    
3. 選取 [API 存取]，按一下 [新增]，然後選取您的 Web API 和範圍 (權限)。

    ![設定 API 存取權](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>註冊 Web API

1. 選取 [應用程式]，然後選取 [新增]。
3. 輸入應用程式的名稱。 例如，*webapi1*。
4. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
5. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 例如，您可以將它設定為在 `https://localhost:44316` 於本機接聽。 如果還不知道連接埠號碼，您可以輸入預留位置值，之後再變更。
6. 針對**應用程式識別碼 URI**，請輸入您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 例如： `https://contosotenant.onmicrosoft.com/api`。
7. 按一下頁面底部的 [新增] 。
8. 選取您建立的 *webapi1* 應用程式，然後視需要選取 [發佈的範圍] 以新增更多範圍。 根據預設，已定義 `user_impersonation` 範圍。 `user_impersonation` 範圍讓其他應用程式能夠代表已登入的使用者存取此 API。 如果您希望的話，可以移除 `user_impersonation` 範圍。

    ![設定發佈的範圍](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>註冊行動或原生應用程式

1. 選取 [應用程式]，然後選取 [新增]。
2. 輸入應用程式的名稱。 例如，*nativeapp1*。
3. 針對 [包含 Web 應用程式/Web API]，選取 [否]。
4. 針對 [包含原生用戶端]，選取 [是]。
5. 針對 [重新導向 URI]，輸入具有自訂配置的有效重新導向 URI。 選擇重新導向 URI 時，有兩個重要考量︰

    - **唯一** - 每個應用程式的重新導向 URI 的配置都應該是唯一。 在範例 `com.onmicrosoft.contoso.appname://redirect/path`中，`com.onmicrosoft.contoso.appname` 為配置。 應該遵循這個模式。 如果兩個應用程式共用相同配置，系統會讓使用者選擇應用程式。 如果使用者做出錯誤的選擇，登入便會失敗。
    - **完成** - 重新導向 URI 必須有配置和路徑。 路徑的網域後面必須至少包含一個正斜線。 例如，`//contoso/` 可正常運作，而 `//contoso` 會失敗。 確定重新導向 URI 未包含特殊字元 (例如底線)。

6. 按一下頁面底部的 [新增] 。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 註冊 Web API
> * 註冊行動或原生應用程式

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中建立使用者流程](tutorial-create-user-flows.md)