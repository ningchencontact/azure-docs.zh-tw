---
title: 設定 Azure Active Directory 組織的登入-Azure Active Directory B2C
description: 在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 58c6d1b032f5b492c5641ff51da80426124069b1
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716773"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入

>[!NOTE]
> 這項功能處於公開預覽狀態。 請勿在生產環境中使用這項功能。

若要在 Azure AD B2C 中使用 Azure Active Directory (Azure AD) 做為[識別提供者](active-directory-b2c-reference-oauth-code.md)，您需要建立代表該識別提供者的應用程式。 本文說明如何讓特定 Azure AD 組織中的使用者能夠使用 Azure AD B2C 中的使用者流程登入。

## <a name="create-an-azure-ad-app"></a>建立 Azure AD 應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式，此租用戶與您的 Azure AD B2C 租用戶不同。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請確定您使用的是包含您 Azure AD 租使用者的目錄。 選取頂端功能表中的 [**目錄和訂**用帳戶] 篩選, 然後選擇包含您 Azure AD 租使用者的目錄。 這與您的 Azure AD B2C 租使用者不是相同的租使用者。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
4. 選取 [新增註冊]。
5. 輸入應用程式的名稱。 例如： `Azure AD B2C App` 。
6. 只接受此應用程式**在此組織目錄中**選取的帳戶。
7. 針對 [重新**導向 URI**], 接受 [ **Web**] 的值, 並以小寫字母輸入下列 URL, `your-B2C-tenant-name`其中會以您的 Azure AD B2C 租使用者名稱取代。 例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`：

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。

8. 按一下 [註冊]。 複製**應用程式 (用戶端) 識別碼**, 以供稍後使用。
9. 在 [應用程式] 功能表中選取 [**憑證 & 秘密**], 然後選取 [**新增用戶端密碼**]。
10. 輸入用戶端密碼的 [名稱]。 例如： `Azure AD B2C App Secret` 。
11. 選取 [到期時間]。 針對此應用程式, 接受**1 年**的選取專案。
12. 選取 [新增] 並複製所顯示的新用戶端密碼值, 以供稍後使用。

## <a name="configure-azure-ad-as-an-identity-provider"></a>將 Azure AD 設定成識別提供者

1. 請確定您使用的是包含 Azure AD B2C 租使用者的目錄。 選取頂端功能表中的 [**目錄和訂**用帳戶] 篩選, 然後選擇包含您 Azure AD B2C 租使用者的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
3. 選取 [識別提供者]，然後選取 [新增]。
4. 輸入 [名稱]。 例如，輸入 `Contoso Azure AD`。
5. 選取 [**識別提供者類型**], 選取 [ **OpenID connect (預覽)** ], 然後按一下 **[確定]** 。
6. 選取 [**設定此識別提供者**]
7. 針對 [中繼資料 URL] 輸入以下 URL，並將 `your-AD-tenant-domain` 取代為您的 Azure AD 租用戶網域名稱。 例如：`https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. 針對 [**用戶端識別碼**], 輸入您先前記錄的 [應用程式識別碼] 和 [**用戶端密碼**], 輸入您先前記錄的用戶端密碼。
9. 您也可以選擇輸入 **Domain_hint** 的值。 例如： `ContosoAD` 。 這是在要求中使用 *domain_hint* 參考此識別提供者時，要使用的值。
10. 按一下 [確定 **Deploying Office Solutions**]。
11. 選取 [對應此識別提供者的宣告]，然後設定下列宣告：

    - 針對 [使用者識別碼]，輸入 `oid`。
    - 針對 [顯示名稱]，輸入 `name`。
    - 針對 [名字]，輸入 `given_name`。
    - 針對 [姓氏]，輸入 `family_name`。
    - 針對 [電子郵件]，輸入 `unique_name`。

12. 按一下 [確定]，然後按一下 [建立] 以儲存您設定。
