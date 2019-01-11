---
title: 透過使用者流程將存取權杖傳遞到 Azure Active Directory B2C 中的應用程式 | Microsoft Docs
description: 了解如何透過使用者流程，將 OAuth2.0 身分識別提供者的存取權杖作為宣告傳遞給 Azure Active Directory B2C 中的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b26605bd0b436d948fb1f62cbf32a17ea4f386d0
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602251"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>透過使用者流程將存取權杖傳遞到 Azure Active Directory B2C 中的應用程式

> [!NOTE]
> 此功能目前為公開預覽狀態。

> [!Important]
> 此公開預覽功能暫時無法供使用。

Azure Active Directory (Azure AD) B2C 中的[使用者流程](active-directory-b2c-reference-policies.md)為您的應用程式使用者提供了註冊或使用身分識別提供者登入的機會。 當發生這種情況時，Azure AD B2C 會從身分識別提供者處收到[存取權杖](active-directory-b2c-reference-tokens.md)。 Azure AD B2C 會使用該權杖來擷取使用者的相關資訊。 您在使用者流程中啟用宣告，以將權杖傳遞至您在 Azure AD B2C 中註冊的應用程式。

Azure AD B2C 目前僅支援傳遞 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) 身分識別提供者的存取權杖，其中包括 [Facebook](active-directory-b2c-setup-fb-app.md) 和 [Google](active-directory-b2c-setup-goog-app.md)。 對於所有其他識別提供者，宣告會傳回空白。

## <a name="prerequisites"></a>必要條件

- 您的應用程式必須使用 [v2 使用者流程](user-flow-versions.md)。
- 您的自訂原則是使用 OAuth 2.0 識別提供者設定。

## <a name="enable-the-claim"></a>啟用宣告

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [使用者流程]，然後選取您的使用者流程。 例如，**B2C_1_SignupSignIn**。
5. 選取 [應用程式宣告]。
6. 啟用 [識別提供者存取權杖]。

    ![應用程式宣告](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. 按一下 [儲存] 以儲存使用者流程。

## <a name="test-the-user-flow"></a>測試使用者流程

在 Azure AD B2C 中測試應用程式時，將 Azure AD B2C 權杖傳回到 `https://jwt.ms` 以檢閱其中的宣告很有用。

1. 在使用者流程的 [概觀] 頁面中，選取 [執行使用者流程]。
2. 針對**應用程式**，請選取您先前註冊的應用程式。 若要查看下面範例中的權杖，**回覆 URL** 應該會顯示 `https://jwt.ms`。
3. 按一下 [執行使用者流程]，然後使用您的帳戶認證登入。 您應該會在 **idp_access_token** 宣告中看到識別提供者的存取權杖。

    您應該會看到類似下列範例的內容：

    ![已解碼的權杖](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>後續步驟

在 [Azure Active Directory 權杖參考](active-directory-b2c-reference-tokens.md)中深入了解權杖。




