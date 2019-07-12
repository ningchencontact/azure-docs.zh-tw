---
title: 開始使用自訂原則-Azure Active Directory B2C |Microsoft Docs
description: 了解如何開始使用 Azure Active Directory B2C 中的自訂原則。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2929c033b4744ea89f8e3d711a5e2e0df6301c14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730018"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中開始使用自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自訂原則](active-directory-b2c-overview-custom.md)是定義 Azure Active Directory (Azure AD) B2C 租用戶行為的設定檔。 在此文章中，您會建立自訂原則，使用電子郵件地址與密碼來支援本機帳戶註冊或登入。 您也會準備環境以新增識別提供者。

## <a name="prerequisites"></a>必要條件

- 如果您還沒有租用戶，就必須[建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)並連結到您的 Azure 訂用帳戶。
- [註冊您的應用程式](tutorial-register-applications.md)您所建立，讓它能夠與 Azure AD B2C 通訊的租用戶中。

## <a name="add-signing-and-encryption-keys"></a>新增簽署與加密金鑰

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 請確定您使用包含 Azure AD B2C 租用戶的目錄。 按一下 **目錄和訂用帳戶篩選**上方功能表中，然後選擇包含您的租用戶的目錄。 
3. 選擇 Azure 入口網站左上角的 [所有服務]  ，搜尋並選取 [Azure AD B2C]  。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]  。

### <a name="create-the-signing-key"></a>建立簽署金鑰

1. 選取 [原則金鑰]  ，然後選取 [新增]  。
2. 針對 [選項]  選擇 `Generate`。
3. 在 [名稱]  中輸入 `TokenSigningKeyContainer`。 可能會自動加入前置詞 `B2C_1A_`。
4. 針對 [金鑰類型]  選取 [RSA]  。
5. 針對 [金鑰使用方法]  選取 [簽章]  。
6. 按一下 [建立]  。

### <a name="create-the-encryption-key"></a>建立加密金鑰

1. 選取 [原則金鑰]  ，然後選取 [新增]  。
2. 針對 [選項]  選擇 `Generate`。
3. 在 [名稱]  中輸入 `TokenEncryptionKeyContainer`。 可能會自動加入前置詞 `B2C_1A`_。
4. 針對 [金鑰類型]  選取 [RSA]  。
5. 針對 [金鑰使用方法]  選取 [加密]  。
6. 按一下 [建立]  。

### <a name="create-the-facebook-key"></a>建立 Facebook 金鑰

如果您已經有 [Facebook 應用程式祕密](active-directory-b2c-setup-fb-app.md)，請將它當作原則金鑰來新增到您的租用戶。 否則，您必須建立具有預留位置值的金鑰，原則才能通過驗證。

1. 選取 [原則金鑰]  ，然後選取 [新增]  。
2. 針對 [選項]  選擇 `Manual`。
3. 針對 [名稱]  輸入 `FacebookSecret`。 可能會自動加入前置詞 `B2C_1A_`。
4. 在 [祕密]  中，輸入 developers.facebook.com 提供給您的 Facebook 祕密，或輸入 `0` 作為預留位置。 這個值是祕密，而非應用程式識別碼。
5. 針對 [金鑰使用方法]  選取 [簽章]  。
6. 按一下 [建立]  。

## <a name="register-identity-experience-framework-applications"></a>註冊身分識別體驗架構應用程式

Azure AD B2C 會要求您註冊兩個用來註冊和登入使用者的應用程式：IdentityExperienceFramework (Web 應用程式)，以及具有來自 IdentityExperienceFramework 應用程式之委派權限的 ProxyIdentityExperienceFramework (原生應用程式)。 本機帳戶只存在於您的租用戶。 您的使用者會使用唯一的電子郵件地址/密碼組合進行註冊，以存取您租用戶所註冊的應用程式。

### <a name="register-the-identityexperienceframework-application"></a>註冊 IdentityExperienceFramework 應用程式

1. 選擇**所有的服務**在 Azure 入口網站左上角，搜尋並選取**Azure Active Directory**。
2. 在功能表中，選取**應用程式註冊 （舊版）** 。
3. 選取 [新增應用程式註冊]  。
4. 針對 [名稱]  輸入 `IdentityExperienceFramework`。
5. 針對 [應用程式類型]  選擇 [Web 應用程式/API]  。
6. 針對 [登入 URL]  輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶網域名稱。 所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。
7. 按一下 [建立]  。 建立之後，複製應用程式識別碼，並儲存它以供日後使用。

### <a name="register-the-proxyidentityexperienceframework-application"></a>註冊 ProxyIdentityExperienceFramework 應用程式

1. 在 **應用程式註冊 （傳統）** ，選取**新的應用程式註冊**。
2. 針對 [名稱]  輸入 `ProxyIdentityExperienceFramework`。
3. 針對 [應用程式類型]  選擇 [原生]  。
4. 針對 [重新導向 URI]  輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶。
5. 按一下 [建立]  。 建立之後，複製應用程式識別碼，並儲存它以供日後使用。
6. 在 [設定] 頁面上，選取 [必要權限]  ，然後選取 [新增]  。
7. 選擇**選取 API**，搜尋並選取**IdentityExperienceFramework**，然後按一下**選取**。
9. 選取 [存取 IdentityExperienceFramework]  旁的核取方塊、按一下 [選取]  ，然後按一下 [完成]  。
10. 選取 [授與權限]  ，然後選取 [是]  加以確認。

## <a name="download-starter-pack-and-modify-policies"></a>下載入門套件和修改原則

自訂原則是一組需要上傳至 Azure AD B2C 租用戶的 XML 檔案。 我們提供檔案的入門套件以協助您加快進展速度。 下列清單中的每個入門套件，針對要完成所述的案例，均包含所需的最小數目技術設定檔與使用者旅程圖：

- LocalAccounts - 只能使用本機帳戶。
- SocialAccounts - 只能使用社交 (或同盟) 帳戶。
- SocialAndLocalAccounts - 可使用本機帳戶與社交帳戶。
- SocialAndLocalAccountsWithMFA - 啟用社交、本機與 Multi-Factor Authentication 選項。

每個入門套件均包含：

- 基底檔案。 需要對基底做一些修改。
- 擴充檔案。  大部分的設定變更都在這個檔案中完成。
- 信賴憑證者檔案。 工作特有的檔案，由應用程式所呼叫。

>[!NOTE]
>如果 XML 編輯器支援驗證，請根據入門套件根目錄中的 TrustFrameworkPolicy_0.3.0.0.xsd XML 結構描述來驗證檔案。 在上載之前，XML 結構描述驗證會識別錯誤。

1. [下載 .zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或執行：

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. 在 SocialAndLocalAccounts 資料夾中，透過使用您的租用戶名稱取代 `yourtenant` 以編輯所有檔案。 例如： `contosoTenant.onmicrosoft.com` 。 如果您需要 XML 編輯器，請[試用 Visual Studio Code](https://code.visualstudio.com/download)，這是一個輕巧的跨平台編輯器。

### <a name="add-application-ids-to-the-custom-policy"></a>將應用程式識別碼新增至自訂原則

將應用程式識別碼新增至擴充檔案 *TrustFrameworkExtensions.xml*。

1. 開啟 *TrustFrameworkExtensions.xml* 檔案，並尋找 `<TechnicalProfile Id="login-NonInteractive">` 元素。
2. 以您稍早建立之身分識別體驗架構應用程式的應用程式識別碼，取代 `IdentityExperienceFrameworkAppId` 的兩個執行個體。 以您稍早建立之 Proxy 識別體驗架構應用程式的應用程式識別碼，取代 `ProxyIdentityExperienceFrameworkAppId` 的兩個執行個體。
3. 儲存擴充檔案。

## <a name="upload-the-policies"></a>上傳原則

1. 在識別體驗架構的 [自訂原則] 頁面上，選取 [上傳原則]  。
1. 依此順序上傳 *TrustFrameworkBase.xml*、*TrustFrameworkExtensions.xml*、*SignUpOrSignin.xml*、*ProfileEdit.xml* 與 *PasswordReset.xml*。 上傳檔案時，原則檔案的名稱前面會加上 `B2C_1A_`。

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 在 [自訂原則] 頁面上，選取 [B2C_1A_signup_signin]  。
2. 針對**選取 應用程式**自訂原則的 概觀 頁面上選取 web 應用程式名稱*webapp1*先前登錄。 請確定**回覆 URL**是`https://jwt.ms`。
3. 選取 [立即執行]  。
4. 您應該可以使用電子郵件地址註冊。
5. 使用相同的帳戶登入，以確認您的設定正確。

## <a name="add-facebook-as-an-identity-provider"></a>將 Facebook 新增為識別提供者

1. 設定 [Facebook 應用程式](active-directory-b2c-setup-fb-app.md)。
2. 在 *TrustFrameworkExtensions.xml* 檔案中，使用 Facebook 應用程式識別碼來取代 `client_id` 的值：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. 將 *TrustFrameworkExtensions.xml* 檔案上傳至您的租用戶。
4. 使用 [立即執行]  進行測試，或直接從您已註冊的應用程式中叫用原則。

## <a name="next-steps"></a>後續步驟

- 將 Azure Active Directory 新增為識別提供者。 此入門指南中所使用的基底檔案已經包含您新增其他識別提供者時所需的部分內容。 如需設定登入的相關資訊，請參閱[使用 Active Directory B2C 自訂原則透過 Azure Active Directory 帳戶設定註冊和登入](active-directory-b2c-setup-aad-custom.md)一文。
