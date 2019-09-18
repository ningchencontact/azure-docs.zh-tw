---
title: 開始使用自訂原則-Azure Active Directory B2C
description: 瞭解如何開始使用 Azure Active Directory B2C 中的自訂原則。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b7eb004dbeba499e6f67f98165b72d7ec8615f1b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065854"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中開始使用自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自訂原則](active-directory-b2c-overview-custom.md)是定義 Azure Active Directory B2C （Azure AD B2C）租使用者行為的設定檔。 在此文章中，您會建立自訂原則，使用電子郵件地址與密碼來支援本機帳戶註冊或登入。 您也會準備環境以新增識別提供者。

## <a name="prerequisites"></a>必要條件

- 如果您還沒有租用戶，就必須[建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)並連結到您的 Azure 訂用帳戶。
- 在您建立的租使用者中[註冊您的應用程式](tutorial-register-applications.md), 讓它可以與 Azure AD B2C 進行通訊。

## <a name="add-signing-and-encryption-keys"></a>新增簽署與加密金鑰

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]。

### <a name="create-the-signing-key"></a>建立簽署金鑰

1. 選取 [原則金鑰]，然後選取 [新增]。
2. 針對 [選項] 選擇 `Generate`。
3. 在 [名稱] 中輸入 `TokenSigningKeyContainer`。 可能會自動加入前置詞 `B2C_1A_`。
4. 針對 [金鑰類型] 選取 [RSA]。
5. 針對 [金鑰使用方法] 選取 [簽章]。
6. 按一下 [建立]。

### <a name="create-the-encryption-key"></a>建立加密金鑰

1. 選取 [原則金鑰]，然後選取 [新增]。
2. 針對 [選項] 選擇 `Generate`。
3. 在 [名稱] 中輸入 `TokenEncryptionKeyContainer`。 可能會自動加入前置詞 `B2C_1A`_。
4. 針對 [金鑰類型] 選取 [RSA]。
5. 針對 [金鑰使用方法] 選取 [加密]。
6. 按一下 [建立]。

### <a name="create-the-facebook-key"></a>建立 Facebook 金鑰

如果您已經有 [Facebook 應用程式祕密](active-directory-b2c-setup-fb-app.md)，請將它當作原則金鑰來新增到您的租用戶。 否則，您必須建立具有預留位置值的金鑰，原則才能通過驗證。

1. 選取 [原則金鑰]，然後選取 [新增]。
2. 針對 [選項] 選擇 `Manual`。
3. 針對 [名稱] 輸入 `FacebookSecret`。 可能會自動加入前置詞 `B2C_1A_`。
4. 在 [祕密] 中，輸入 developers.facebook.com 提供給您的 Facebook 祕密，或輸入 `0` 作為預留位置。 這個值是秘密, 而不是應用程式識別碼。
5. 針對 [金鑰使用方法] 選取 [簽章]。
6. 按一下 [建立]。

## <a name="register-identity-experience-framework-applications"></a>註冊身分識別體驗架構應用程式

Azure AD B2C 會要求您註冊兩個用來註冊和登入使用者的應用程式：IdentityExperienceFramework (Web 應用程式)，以及具有來自 IdentityExperienceFramework 應用程式之委派權限的 ProxyIdentityExperienceFramework (原生應用程式)。 本機帳戶只存在於您的租用戶。 您的使用者會使用唯一的電子郵件地址/密碼組合進行註冊，以存取您租用戶所註冊的應用程式。

### <a name="register-the-identityexperienceframework-application"></a>註冊 IdentityExperienceFramework 應用程式

1. 選取 Azure 入口網站左上角的 [**所有服務**]。
1. 在搜尋方塊中，輸入 `Azure Active Directory`。
1. 在搜尋結果中選取 [Azure Active Directory]。
1. 在左側功能表中的 [**管理**] 底下, 選取 **[應用程式註冊 (舊版)** ]。
1. 選取 [新增應用程式註冊]。
1. 針對 [名稱] 輸入 `IdentityExperienceFramework`。
1. 針對 [應用程式類型] 選擇 [Web 應用程式/API]。
1. 針對 [登入 URL] 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶網域名稱。 所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。
1. 按一下 [建立]。 建立之後，複製應用程式識別碼，並儲存它以供日後使用。

### <a name="register-the-proxyidentityexperienceframework-application"></a>註冊 ProxyIdentityExperienceFramework 應用程式

1. 在 **[應用程式註冊 (舊版)** ] 中, 選取 [**新增應用程式註冊**]。
2. 針對 [名稱] 輸入 `ProxyIdentityExperienceFramework`。
3. 針對 [應用程式類型] 選擇 [原生]。
4. 針對 [重新導向 URI] 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶。
5. 按一下 [建立]。 建立之後，複製應用程式識別碼，並儲存它以供日後使用。
6. 在 [設定] 頁面上，選取 [必要權限]，然後選取 [新增]。
7. 選擇 [**選取 API**], 搜尋並選取 [ **IdentityExperienceFramework**], 然後按一下 [**選取**]。
9. 選取 [存取 IdentityExperienceFramework] 旁的核取方塊、按一下 [選取]，然後按一下 [完成]。
10. 選取 [授與權限]，然後選取 [是] 加以確認。

## <a name="custom-policy-starter-pack"></a>自訂原則入門套件

自訂原則是一組您上傳至 Azure AD B2C 租使用者以定義技術設定檔和使用者旅程的 XML 檔案。 我們會提供入門套件, 其中包含數個預先建立的原則, 可讓您快速進行。 這些入門套件中的每一個都包含最小數目的技術設定檔, 以及完成所述案例所需的使用者旅程:

- **LocalAccounts** -僅允許使用本機帳戶。
- **Socialaccounts 只能**-只允許使用社交 (或同盟) 帳戶。
- **SocialAndLocalAccounts** -啟用本機和社交帳戶的使用。
- **SocialAndLocalAccountsWithMFA** -啟用社交、本機和多重要素驗證選項。

每個入門套件均包含：

- **基底**檔案-基底需要一些修改。 範例：*Trustframeworkbase.xml .xml*
- **擴充**檔案-此檔案是大部分設定變更的位置。 範例：*TrustFrameworkExtensions .xml*
- **信賴**憑證者檔案-應用程式所呼叫的工作特定檔案。 例如：*Signuporsignin.xml .xml*、 *profileedit.xml*、 *passwordreset.xml .xml*

在本文中, 您會編輯**SocialAndLocalAccounts**入門套件中的 XML 自訂原則檔案。 如果您需要 XML 編輯器, 請嘗試[Visual Studio Code](https://code.visualstudio.com/download), 這是一個輕巧的跨平臺編輯器。

### <a name="get-the-starter-pack"></a>取得入門套件

從 GitHub 取得自訂原則入門套件, 然後使用您的 Azure AD B2C 租使用者名稱更新 SocialAndLocalAccounts 入門套件中的 XML 檔案。

1. [下載 .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)檔案或複製存放庫:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 在**SocialAndLocalAccounts**目錄中的所有檔案中, 將字串`yourtenant`取代為 Azure AD B2C 租使用者的名稱。

    例如, 如果您的 B2C 租使用者名稱是*contosotenant*, 的`yourtenant.onmicrosoft.com`所有實例就會變成。 `contosotenant.onmicrosoft.com`

### <a name="add-application-ids-to-the-custom-policy"></a>將應用程式識別碼新增至自訂原則

將應用程式識別碼新增至擴充檔案 *TrustFrameworkExtensions.xml*。

1. 開啟`SocialAndLocalAccounts/` `<TechnicalProfile Id="login-NonInteractive">`並**尋找元素。`TrustFrameworkExtensions.xml`**
1. 以您稍早`IdentityExperienceFrameworkAppId`建立之 IdentityExperienceFramework 應用程式的應用程式識別碼, 取代的兩個實例。
1. 以您稍早`ProxyIdentityExperienceFrameworkAppId`建立之 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼, 取代的兩個實例。
1. 儲存檔案。

## <a name="upload-the-policies"></a>上傳原則

1. 在 Azure 入口網站的 B2C 租使用者中, 選取 [ **Identity Experience Framework** ] 功能表項目。
1. 選取 **[上傳自訂原則**]。
1. 在此順序中, 上傳原則檔案:
    1. *Trustframeworkbase.xml .xml*
    1. *TrustFrameworkExtensions .xml*
    1. *Signuporsignin.xml .xml*
    1. *Profileedit.xml .xml*
    1. *Passwordreset.xml .xml*

當您上傳檔案時, Azure 會將`B2C_1A_`前置詞新增至每個檔案。

> [!TIP]
> 如果您的 xml 編輯器支援驗證, 請針對`TrustFrameworkPolicy_0.3.0.0.xsd`位於入門套件根目錄中的 xml 架構來驗證檔案。 在上載之前，XML 結構描述驗證會識別錯誤。

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 在 [**自訂原則**] 底下，選取 [ **B2C_1A_signup_signin**]。
1. 針對 [**選取應用程式**], 在自訂原則的 [總覽] 頁面上, 選取您先前註冊之名為*webapp1*的 web 應用程式。
1. 請確定 [**回復 URL** ] 為`https://jwt.ms`。
1. 選取 [立即執行]。
1. 使用電子郵件地址註冊。
1. 再次選取 [**立即執行**]。
1. 使用相同的帳戶登入，以確認您的設定正確。

## <a name="add-facebook-as-an-identity-provider"></a>將 Facebook 新增為識別提供者

1. 完成[設定註冊和以 Facebook 帳戶登入](active-directory-b2c-setup-fb-app.md)中的步驟, 以設定 facebook 應用程式。
1. 在檔案中`client_id` , 將的值取代為 Facebook 應用程式識別碼: `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 將 *TrustFrameworkExtensions.xml* 檔案上傳至您的租用戶。
1. 在 [**自訂原則**] 底下，選取 [ **B2C_1A_signup_signin**]。
1. 選取 [**立即執行**], 然後選取 [facebook] 以使用 Facebook 登入並測試自訂原則。 或者, 直接從已註冊的應用程式叫用原則。

## <a name="next-steps"></a>後續步驟

接下來, 請嘗試新增 Azure Active Directory (Azure AD) 做為身分識別提供者。 本快速入門手冊中所使用的基底檔案, 已包含新增其他身分識別提供者 (例如 Azure AD) 所需的部分內容。

如需將 Azure AD 設定為和識別提供者的詳細資訊, 請參閱[使用 Active Directory B2C 自訂原則, 設定使用 Azure Active Directory 帳戶的註冊和登入](active-directory-b2c-setup-aad-custom.md)。
