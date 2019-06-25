---
title: 教學課程 - 將識別提供者新增至您的應用程式 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ceae908b37c825721a42c49db4503382eb35c71c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055090"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式

在您的應用程式中，您可以讓使用者使用不同的識別提供者登入。 *識別提供者*可建立、維護及管理身分識別資訊，同時對應用程式提供驗證服務。 您可以使用 Azure 入口網站，將 Azure Active Directory (Azure AD) B2C 支援的識別提供者新增至您的[使用者流程](active-directory-b2c-reference-policies.md)。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立識別提供者應用程式
> * 將識別提供者新增至您的租用戶
> * 將識別提供者新增至您的使用者流程

您在應用程式中通常只會使用一個識別提供者，但您可以選擇新增更多個。 本教學課程說明如何將 Azure AD 識別提供者和 Facebook 識別提供者新增至您的應用程式。 將這兩個這些識別提供者新增至應用程式是選用作業。 您可以也新增其他識別提供者，例如 [Amazon](active-directory-b2c-setup-amzn-app.md)、[Github](active-directory-b2c-setup-github-app.md)、[Google](active-directory-b2c-setup-goog-app.md)、[LinkedIn](active-directory-b2c-setup-li-app.md)、[Microsoft](active-directory-b2c-setup-msa-app.md) 或 [Twitter](active-directory-b2c-setup-twitter-app.md)。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

[建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。 

## <a name="create-applications"></a>建立應用程式

識別提供者應用程式會提供識別碼和金鑰，以啟用與 Azure AD B2C 租用戶之間的通訊。 在本節的教學課程中，您將建立 Azure AD 應用程式和 Facebook 應用程式，以從中取得識別碼和金鑰，將識別提供者新增至您的租用戶。 如果您只要新增其中一個識別提供者，則只需建立該提供者的應用程式。

### <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

若要讓使用者可從 Azure AD 登入，您必須在 Azure AD 租用戶內註冊應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶篩選]  ，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
3. 選擇**所有的服務**左上角的 Azure 入口網站，然後搜尋並選取**應用程式註冊 （舊版）** 。
4. 選取 [新增應用程式註冊]  。
5. 輸入應用程式的名稱。 例如： `Azure AD B2C App`。
6. 針對 [應用程式類型]  ，選取 `Web app / API`。
7. 針對 [登入 URL]  ，以全小寫字母輸入下列 URL，其中以您 Azure AD B2C 租用戶的名稱取代 `your-B2C-tenant-name`。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    例如： `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
    
    所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。

8. 按一下頁面底部的 [新增]  。 複製 [應用程式識別碼]  以供稍後使用。
9. 選取應用程式，然後選取 [設定]  。
10. 選取 [金鑰]  ，輸入金鑰描述，選取持續時間，然後按一下 [儲存]  。 請複製金鑰的值，以便稍後在本教學課程中使用。

### <a name="create-a-facebook-application"></a>建立 Facebook 應用程式

若要在 Azure AD B2C 中使用 Facebook 帳戶作為識別提供者，您必須在 Facebook 建立應用程式。 如果您還沒有 Facebook 帳戶，可以至 [https://www.facebook.com/](https://www.facebook.com/) 取得。

1. 請以您的 Facebook 帳戶認證登入 [Facebook for developers (開發人員專用的 Facebook)](https://developers.facebook.com/)。
2. 如果您尚未這麼做，您必須註冊為 Facebook 開發人員。 若要註冊，請選取頁面右上角的 [註冊]  ，接受 Facebook 的原則，然後完成註冊步驟。
3. 選取 [My Apps]  \(我的應用程式\)，然後按一下 [Add a New App]  \(新增應用程式\)。 
4. 輸入**顯示名稱**和有效的**連絡人電子郵件**。
5. 按一下 [建立應用程式識別碼]  。 系統可能會要求您接受 Facebook 平台原則，並完成線上安全性檢查。
6. 選取 [設定]   > [基本]  。
7. 選擇**分類**，例如 `Business and Pages`。 此為 Facebook 所需的值，但不會用於 Azure AD B2C。
8. 在頁面底部選取 [新增平台]  ，然後選取 [網站]  。
9. 在 [Site URL]  \(網站 URL\) 中，輸入 `https://your-tenant-name.b2clogin.com/`，並將 `your-tenant-name` 取代為您的租用戶名稱。 輸入**隱私權原則 URL** 的 URL，如 `http://www.contoso.com`。 原則 URL 是您需維護以提供應用程式隱私權資訊的網頁。
10. 選取 [Save Changes] \(儲存變更\)  。
11. 在頁面頂端複製 [應用程式識別碼]  的值。 
12. 按一下 [顯示]  ，並複製 [應用程式密碼]  的值。 您必須同時使用這兩個值，將 Facebook 設定為租用戶中的身分識別提供者。 **應用程式密碼**是重要的安全性認證。
13. 選取 [產品]  ，然後在 [Facebook 登入]  下方選取 [設定]  。
14. 在 [Facebook 登入]  下方選取 [設定]  。
15. 在 [Valid OAuth redirect URIs]  \(有效的 OAuth 重新導向 URI\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 按一下頁面底部的 [儲存變更]  。
16. 若要讓 Azure AD B2C 能使用您的 Facebook 應用程式，請按一下頁面右上方的 [狀態]  選取器，並將其設為 [開啟]  。 按一下 [確認]  。 此時，狀態應該會從 [開發]  變更為 [作用中]  。

## <a name="add-the-identity-providers"></a>新增識別提供者

在您為要新增的識別提供者建立應用程式之後，請將識別提供者新增至您的租用戶。

### <a name="add-the-azure-active-directory-identity-provider"></a>新增 Azure Active Directory 識別提供者

1. 按一下頂端功能表中的 [目錄和訂用帳戶篩選]  ，然後選擇包含您 Azure AD B2C 租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
3. 選取 [識別提供者]  ，然後選取 [新增]  。
4. 輸入 [名稱]  。 例如，輸入 *Contoso Azure AD*。
5. 選取 [識別提供者類型]  ，選取 [Open ID Connect (預覽)]  ，然後按一下 [確定]  。
6. 按一下 [設定此識別提供者]  。
7. 針對 [中繼資料 URL]  輸入以下 URL，並將 `your-AD-tenant-domain` 取代為您的 Azure AD 租用戶網域名稱。

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`。

8. 針對 [用戶端識別碼]  輸入您先前記錄的應用程式識別碼，並輸入您先前記錄的金鑰值作為 [用戶端祕密]  。
9. 您也可以選擇輸入 **Domain_hint** 的值。 例如： `ContosoAD`。 網域提示 (https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) 是應用程式的驗證要求中包含的指示詞。 它們可用來加快使用者進入其同盟 IdP 登入頁面的速度。 或者，多租用戶應用程式也可使用網域提示來加速使用者直接前往其租用戶的品牌化 Azure AD 登入頁面。
10. 按一下 [確定]  。
11. 選取 [對應此識別提供者的宣告]  ，然後設定下列宣告：
    
    - 針對 [使用者識別碼]  ，輸入 `oid`。
    - 針對 [顯示名稱]  ，輸入 `name`。
    - 針對 [名字]  ，輸入 `given_name`。
    - 針對 [姓氏]  ，輸入 `family_name`。
    - 針對 [電子郵件]  ，輸入 `unique_name`。

12. 按一下 [確定]  ，然後按一下 [建立]  以儲存您設定。

### <a name="add-the-facebook-identity-provider"></a>新增 Facebook 識別提供者

1. 選取 [識別提供者]  ，然後選取 [新增]  。
2. 輸入 [名稱]  。 例如，輸入 *Facebook*。
3. 選取 [識別提供者類型]  、選取 [Facebook]  ，然後按一下 [確定]  。
4. 選取 [設定此識別提供者]  ，然後輸入您先前記下的應用程式識別碼，作為 [用戶端識別碼]  。 輸入您先前記下的應用程式秘密，作為 [用戶端秘密]  。
5. 依序按一下 [確定]  與 [建立]  ，以儲存您的 Facebook 設定。

## <a name="update-the-user-flow"></a>更新使用者流程

在教學課程中，您已完成必要條件的部分，建立了名為 *B2C_1_signupsignin1* 的註冊和登入使用者流程。 在本節中，您會將識別提供者新增至 *B2C_1_signupsignin1* 使用者流程。

1. 選取 [使用者流程 (原則)]  ，然後選取 *B2C_1_signupsignin1* 使用者流程。
2. 選取 [識別提供者]  ，然後選取您新增的 **Facebook** 和 **Contoso Azure AD** 識別提供者。
3. 選取 [ **儲存**]。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 在您所建立使用者流程的 [概觀] 頁面上，選取 [執行使用者流程]  。
2. 針對 [應用程式]  ，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
3. 按一下 [執行使用者流程]  ，然後使用您先前新增的識別提供者登入。
4. 針對您新增的其他識別提供者重複步驟 1 到 3。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立識別提供者應用程式
> * 將識別提供者新增至您的租用戶
> * 將識別提供者新增至您的使用者流程

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中自訂應用程式的使用者介面](tutorial-customize-ui.md)
