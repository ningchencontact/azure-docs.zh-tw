---
title: 教學課程 - 註冊應用程式以啟用使用 Azure Active Directory B2C 的註冊和登入 | Microsoft Docs
description: 使用 Azure 入口網站建立 Azure AD B2C 租用戶，並用它來註冊應用程式。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: patricka
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.openlocfilehash: 85e61f96ff829052e857d4823b3c944c6d981d2f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>教學課程：註冊應用程式以啟用使用 Azure Active Directory B2C 的註冊和登入

本教學課程將協助您在短短幾分鐘內建立 Azure Active Directory (Azure AD) B2C 租用戶，並用它來註冊應用程式。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶
> * 註冊您的應用程式

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶

B2C 功能無法在您現有的租用戶中啟用。 您必須建立 Azure AD B2C 租用戶。

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

恭喜，您已建立 Azure Active Directory B2C 租用戶。 您是租用戶的「全域管理員」。 您可視需要新增其他「全域管理員」。 若要切換到新的租用戶，請按一下*管理新租用戶連結*。

![管理新租用戶連結](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> 如果您打算將 B2C 租用戶用於生產應用程式，請閱讀 [生產級別租用戶與預覽 B2C 租用戶](active-directory-b2c-reference-tenant-type.md)一文。 當您刪除現有的 B2C 租用戶並使用相同的網域名稱加以重建時，會發生已知的問題。 您需要使用不同的網域名稱建立 B2C 租用戶。
>
>

## <a name="link-your-tenant-to-your-subscription"></a>將您的租用戶連結至您的訂用帳戶

您必須將您的 Azure AD B2C 租用戶連結至您的 Azure 訂用帳戶，才能啟用所有的 B2C 功能，並支付使用費用。 若要深入了解，請閱讀[這篇文章](active-directory-b2c-how-to-enable-billing.md)。 如果您未將 Azure AD B2C 租用戶連結到您的 Azure 訂用帳戶，有些功能會遭到封鎖，您將會在 B2C 設定中看見警告訊息 (「沒有訂用帳戶與此 B2C 租用戶連結，或訂用帳戶需要您的注意。」)。 請務必先進行此步驟，然後才將您的應用程式傳送到生產環境。


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

您也可以在入口網站頂端的 [搜尋資源] 中尋入 `Azure AD B2C` 來存取刀鋒視窗。 在結果清單中選取 [Azure AD B2C] 以存取 B2C 設定刀鋒視窗。

## <a name="register-your-application"></a>註冊您的應用程式

若要建置可接受取用者註冊與登入的應用程式，您必須先使用 Azure Active Directory B2C 租用戶註冊該應用程式。 使用 [建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)中概述的步驟來建立您自己的租用戶。

在 Azure 入口網站中建立的應用程式必須從相同的位置進行管理。 如果您使用 PowerShell 或另一個入口網站編輯 Azure AD B2C 應用程式，系統則不支援這些應用程式支援且無法搭配 Azure AD B2C 運作。 如需詳細資料，請參閱[ 發生錯誤的應用程式](#faulted-apps)一節。 

本文使用的範例將協助您開始使用我們的範例。 您可以在後續的文章中深入了解這些範例。

### <a name="navigate-to-b2c-settings"></a>瀏覽至 B2C 設定

以 B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>根據您的應用程式類型選擇後續步驟

* [註冊 Web 應用程式](#register-a-web-app)
* [註冊 Web API](#register-a-web-api)
* [註冊行動或原生應用程式](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>註冊 Web 應用程式

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>建立 Web 應用程式用戶端密碼

如果您的 Web 應用程式呼叫 Azure AD B2C 所保護的 Web API，請執行下列步驟：
   1. 前往 [金鑰] 刀鋒視窗，然後按一下 [產生金鑰] 按鈕，以建立應用程式祕密。 請記下 [應用程式金鑰] 值。 您可以使用此值，作為應用程式程式碼中的應用程式祕密。
   2. 按一下 [API 存取][新增] 並選取您的 Web API 和範圍 (權限)。

> [!NOTE]
> **應用程式密鑰** 是重要的安全性認證，應該適當地加以保護。
> 

[跳至**後續步驟**](#next-steps)

### <a name="register-a-web-api"></a>註冊 Web API

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

按一下 [已發佈的範圍]，視需要新增更多範圍。 預設會定義 "user_impersonation" 範圍。 user_impersonation 範圍賦予其他應用程式代表已登入使用者存取此 API 的能力。 您可以視需要移除 user_impersonation 範圍。

[跳至**後續步驟**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>註冊行動或原生應用程式

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[跳至**後續步驟**](#next-steps)

### <a name="choosing-a-web-app-or-api-reply-url"></a>選擇 Web 應用程式或 API 回覆 URL

目前，已向 Azure AD B2C 註冊的應用程式僅限使用一組受限的回覆 URL 值。 Web 應用程式和服務的回覆 URL 必須以配置 `https` 開頭，而且所有回覆 URL 值必須共用單一 DNS 網域。 例如，您不能註冊具有下列其中一個回覆 URL 的 Web 應用程式︰

`https://login-east.contoso.com`

`https://login-west.contoso.com`

註冊系統會將現有回覆 URL 的整個 DNS 名稱與您要新增之回覆 URL 的 DNS 名稱做比較。 如果下列任一條件成立，新增 DNS 名稱的要求就會失敗：

* 新回覆 URL 的整個 DNS 名稱與現有回覆 URL 的 DNS 名稱不相符。
* 新回覆 URL 的整個 DNS 名稱不是現有回覆 URL 的子網域。

例如，如果應用程式具有下列回覆 URL：

`https://login.contoso.com`

您可以在其中新增，就像這樣：

`https://login.contoso.com/new`

在此情況下，DNS 名稱會完全相符。 或者，您可以這樣做：

`https://new.login.contoso.com`

在此情況下，您參照的是 login.contoso.com 的 DNS 子網域。如果您想要有一個以 login-east.contoso.com 和 login-west.contoso.com 做為回覆 URL 的應用程式，就必須依下列順序新增這些回覆 URL︰

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

您可以新增後面兩個，因為它們是第一個回覆 URL (contoso.com) 的子網域。

### <a name="choosing-a-native-app-redirect-uri"></a>選擇原生應用程式重新導向 URI

選擇行動/原生應用程式的重新導向 URI 時，有兩個重要的考量︰

* **唯一**︰每個應用程式的重新導向 URI 的配置都應該是唯一。 在範例 (com.onmicrosoft.contoso.appname://redirect/path) 中，com.onmicrosoft.contoso.appname 為配置。 我們建議您遵循此模式。 如果兩個應用程式共用相同的結構描述，則使用者會看到「選擇應用程式」對話方塊。 如果使用者選擇不正確的項目，則會登入失敗。
* **完成**︰重新導向 URI 必須有配置和路徑。 路徑中的網域之後必須包含至少一個正斜線 (例如，//contoso/ 可行，而 //contoso 不可行)。

確定重新導向 uri 中沒有任何特殊字元，例如底線。

### <a name="faulted-apps"></a>發生錯誤的應用程式

下列情況不得編輯 B2C 應用程式：

* 在其他應用程式管理員入口網站上，例如[應用程式註冊入口網站](https://apps.dev.microsoft.com/)。
* 使用圖形 API 或 PowerShell

如果您編輯 Azure AD B2C 應用程式 (如上所述) 並再次嘗試在 Azure 入口網站上的 Azure AD B2C 功能中進行編輯，該應用程式會變為錯誤的應用程式，而且無法再搭配 Azure AD B2C 運作。 您必須刪除應用程式並重新加以建立。

若要刪除應用程式，請移至[應用程式註冊入口網站](https://apps.dev.microsoft.com/)並在此刪除應用程式。 為了讓應用程式得以顯示，您必須是應用程式的擁有者 (而不只是租用戶的系統管理員)。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶
> * 註冊您的應用程式

> [!div class="nextstepaction"]
> [讓 Web 應用程式使用帳戶進行驗證](active-directory-b2c-app-registration.md)