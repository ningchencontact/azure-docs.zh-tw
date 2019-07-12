---
title: 教學課程-將身分識別提供者新增至您的應用程式-Azure Active Directory B2C
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33f595dd36ac9448cc1276647f9943326b0b74c1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655228"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式

在您的應用程式中，您可以讓使用者使用不同的識別提供者登入。 *識別提供者*可建立、維護及管理身分識別資訊，同時對應用程式提供驗證服務。 您可以使用 Azure 入口網站，將 Azure Active Directory (Azure AD) B2C 支援的識別提供者新增至您的[使用者流程](active-directory-b2c-reference-policies.md)。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立識別提供者應用程式
> * 將識別提供者新增至您的租用戶
> * 將識別提供者新增至您的使用者流程

您在應用程式中通常只會使用一個識別提供者，但您可以選擇新增更多個。 本教學課程說明如何將 Azure AD 識別提供者和 Facebook 識別提供者新增至您的應用程式。 將這兩個這些識別提供者新增至應用程式是選用作業。 您可以也加入其他身分識別提供者，例如[Amazon](active-directory-b2c-setup-amzn-app.md)， [GitHub](active-directory-b2c-setup-github-app.md)， [Google](active-directory-b2c-setup-goog-app.md)， [LinkedIn](active-directory-b2c-setup-li-app.md)， [Microsoft](active-directory-b2c-setup-msa-app.md)，或[Twitter](active-directory-b2c-setup-twitter-app.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>先決條件

[建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。

## <a name="create-applications"></a>建立應用程式

識別提供者應用程式會提供識別碼和金鑰，以啟用與 Azure AD B2C 租用戶之間的通訊。 在本節的教學課程中，您將建立 Azure AD 應用程式和 Facebook 應用程式，以從中取得識別碼和金鑰，將識別提供者新增至您的租用戶。 如果您只要新增其中一個識別提供者，則只需建立該提供者的應用程式。

### <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

若要讓使用者可從 Azure AD 登入，您必須在 Azure AD 租用戶內註冊應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下頂端功能表中的 [目錄和訂用帳戶篩選]  ，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [應用程式註冊]  。
1. 選取 [新增註冊]  。
1. 輸入應用程式的名稱。 例如： `Azure AD B2C App` 。
1. 接受的選項**只有此組織目錄中的帳戶**此應用程式。
1. 針對**重新導向 URI**，接受值**Web** ，然後輸入下列 URL 中所有的小寫字母，取代`your-B2C-tenant-name`與 Azure AD B2C 租用戶的名稱。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如： `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 。

    所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。

1. 選取 **註冊**，然後記錄**應用程式 （用戶端） 識別碼**您在稍後步驟中使用。
1. 底下**管理**在應用程式 功能表中，選取**憑證與祕密**，然後選取**新的用戶端祕密**。
1. 請輸入**描述**的用戶端祕密。 例如： `Azure AD B2C App Secret` 。
1. 選取的逾期期限。 針對此應用程式中，接受的選項**1 年**。
1. 選取 **新增**，然後記錄您使用新的用戶端祕密的值，在稍後的步驟。

### <a name="create-a-facebook-application"></a>建立 Facebook 應用程式

若要在 Azure AD B2C 中使用 Facebook 帳戶作為識別提供者，您必須在 Facebook 建立應用程式。 如果您還沒有 Facebook 帳戶，可以至 [https://www.facebook.com/](https://www.facebook.com/) 取得。

1. 請以您的 Facebook 帳戶認證登入 [Facebook for developers (開發人員專用的 Facebook)](https://developers.facebook.com/)。
1. 如果您尚未這麼做，您必須註冊為 Facebook 開發人員。 若要這樣做，請選取**開始**在頁面的右上角，接受 Facebook 的原則，並完成註冊步驟。
1. 選取 **我的應用程式**，然後**建立應用程式**。
1. 輸入**顯示名稱**和有效的**連絡人電子郵件**。
1. 按一下 [建立應用程式識別碼]  。 這可能會要求您接受 Facebook 平台原則，並完成線上安全性檢查。
1. 選取 [設定]   > [基本]  。
1. 選擇**分類**，例如 `Business and Pages`。 這個值會 Facebook，所需，但不會使用 Azure AD B2C 所。
1. 在頁面底部選取 [新增平台]  ，然後選取 [網站]  。
1. 在 [Site URL]  \(網站 URL\) 中，輸入 `https://your-tenant-name.b2clogin.com/`，並將 `your-tenant-name` 取代為您的租用戶名稱。
1. 輸入**隱私權原則 URL** 的 URL，如 `http://www.contoso.com/`。 隱私權原則 URL 是您維護提供您的應用程式的隱私權資訊的網頁。
1. 選取 [Save Changes] \(儲存變更\)  。
1. 在頁面頂端，記錄的值**應用程式識別碼**。
1. 旁**應用程式祕密**，選取**顯示**並記錄其值。 您可以將 Facebook 設定為身分識別提供者，在您的租用戶中使用的應用程式識別碼 」 和 「 應用程式祕密。 **應用程式祕密**是您應該安全地儲存重要的安全性認證。
1. 選取旁邊的加號**產品**，然後在**Facebook 登入**，選取**設定**。
1. 底下**Facebook 登入**在左側功能表中，選取**設定**。
1. 在 [Valid OAuth redirect URIs]  \(有效的 OAuth 重新導向 URI\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 選取 **儲存變更**在頁面底部。
1. 若要讓 Azure AD B2C 使用您的 Facebook 應用程式，按一下**狀態**頂端的選取器的頁面並將它**上**以將應用程式設為公用，然後按一下 **確認**. 此時，狀態應該會從 [開發]  變更為 [作用中]  。

## <a name="add-the-identity-providers"></a>新增識別提供者

在您為要新增的識別提供者建立應用程式之後，請將識別提供者新增至您的租用戶。

### <a name="add-the-azure-active-directory-identity-provider"></a>新增 Azure Active Directory 識別提供者

1. 按一下頂端功能表中的 [目錄和訂用帳戶篩選]  ，然後選擇包含您 Azure AD B2C 租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [識別提供者]  ，然後選取 [新增]  。
1. 輸入 [名稱]  。 例如，輸入 *Contoso Azure AD*。
1. 選取 [識別提供者類型]  ，選取 [Open ID Connect (預覽)]  ，然後按一下 [確定]  。
1. 按一下 [設定此識別提供者]  。
1. 針對**中繼資料 url**，輸入下列 URL，取代`your-AD-tenant-domain`與 Azure AD 租用戶的網域名稱。

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 。

1. 針對**用戶端識別碼**，輸入*應用程式 （用戶端） 識別碼*以您先前記錄下來。
1. 針對**用戶端祕密**，輸入*用戶端祕密*先前記錄的值。
1. 您也可以選擇輸入 **Domain_hint** 的值。 例如： `ContosoAD` 。 [網域提示](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)是應用程式的驗證要求中包含的指示詞。 它們可用來加快使用者進入其同盟 IdP 登入頁面的速度。 或者，多租用戶應用程式也可使用網域提示來加速使用者直接前往其租用戶的品牌化 Azure AD 登入頁面。
1. 選取 [確定]  。
1. 選取 [對應此識別提供者的宣告]  ，然後設定下列宣告：

    - 針對 [使用者識別碼]  ，輸入 `oid`。
    - 針對 [顯示名稱]  ，輸入 `name`。
    - 針對 [名字]  ，輸入 `given_name`。
    - 針對 [姓氏]  ，輸入 `family_name`。
    - 針對 [電子郵件]  ，輸入 `unique_name`。

1. 選取  **確定**，然後選取**建立**來儲存您的組態。

### <a name="add-the-facebook-identity-provider"></a>新增 Facebook 識別提供者

1. 選取 [識別提供者]  ，然後選取 [新增]  。
1. 輸入 [名稱]  。 例如，輸入 *Facebook*。
1. 選取 **身分識別提供者類型**，選取**Facebook**，然後選取**確定**。
1. 選取 **設定此身分識別提供者**，然後輸入*應用程式識別碼*記錄稍早為**用戶端識別碼**。
1. 請輸入*應用程式祕密*記錄為**用戶端祕密**。
1. 選取  **確定** ，然後選取**建立**，儲存您的 Facebook 設定。

## <a name="update-the-user-flow"></a>更新使用者流程

在教學課程中，您已完成必要條件的部分，建立了名為 *B2C_1_signupsignin1* 的註冊和登入使用者流程。 在本節中，您會將識別提供者新增至 *B2C_1_signupsignin1* 使用者流程。

1. 選取 [使用者流程 (原則)]  ，然後選取 *B2C_1_signupsignin1* 使用者流程。
2. 選取 [識別提供者]  ，然後選取您新增的 **Facebook** 和 **Contoso Azure AD** 識別提供者。
3. 選取 [ **儲存**]。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 在您所建立使用者流程的 [概觀] 頁面上，選取 [執行使用者流程]  。
1. 針對 [應用程式]  ，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 選取 **執行使用者流程**，並接著以您先前新增的身分識別提供者的登入。
1. 針對您新增的其他識別提供者重複步驟 1 到 3。

如果登入作業成功時，將您重新導向至`https://jwt.ms`其中顯示已解碼的語彙基元，類似於：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立識別提供者應用程式
> * 將識別提供者新增至您的租用戶
> * 將識別提供者新增至您的使用者流程

接下來，了解如何自訂 UI 做為其身分識別體驗，在您的應用程式的一部分，向使用者顯示的頁面：

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中自訂應用程式的使用者介面](tutorial-customize-ui.md)
