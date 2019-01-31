---
title: 在 Azure Active Directory B2C 中設定 Azure Active Directory 組織的登入 | Microsoft Docs
description: 在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9078cbfd14e61b2de0d513e513413ae3c79137e3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166215"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入

>[!NOTE]
> 這項功能處於公開預覽狀態。 請勿在生產環境中使用這項功能。

本文說明如何讓特定 Azure Active Directory (Azure AD) 組織中的使用者能夠使用 Azure Active Directory (Azure AD) B2C 中的使用者流程登入。

## <a name="create-an-azure-ad-app"></a>建立 Azure AD 應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式，此租用戶與您的 Azure AD B2C 租用戶不同。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
4. 選取 [新增應用程式註冊]。
5. 輸入應用程式的名稱。 例如： `Azure AD B2C App`。
6. 針對 [應用程式類型]，選取 `Web app / API`。
7. 針對 [登入 URL]，以全小寫字母輸入下列 URL，其中以您 Azure AD B2C 租用戶的名稱取代 `your-B2C-tenant-name`。 例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`：

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。

8. 按一下頁面底部的 [新增] 。 複製 [應用程式識別碼] 以供稍後使用。
9. 選取應用程式，然後選取 [設定]。
10. 選取 [金鑰]，輸入金鑰描述，選取持續時間，然後按一下 [儲存]。 複製顯示的金鑰值以供稍後使用。

## <a name="configure-azure-ad-as-an-identity-provider"></a>將 Azure AD 設定成識別提供者

1. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
3. 選取 [識別提供者]，然後選取 [新增]。
4. 輸入 [名稱]。 例如，輸入 "Contoso Azure AD"。
5. 選取 [識別提供者類型]，選取 [Open ID Connect (預覽)]，然後按一下 [確定]。
6. 按一下 [設定此識別提供者]。
7. 針對 [中繼資料 URL] 輸入以下 URL，並將 `your-AD-tenant-domain` 取代為您的 Azure AD 租用戶網域名稱。 例如：`https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. 針對 [用戶端識別碼] 輸入您先前記錄的應用程式識別碼，並輸入您先前記錄的金鑰值作為 [用戶端祕密]。
9. 您也可以選擇輸入 **Domain_hint** 的值。 例如： `ContosoAD`。 這是在要求中使用 *domain_hint* 參考此識別提供者時，要使用的值。 
10. 按一下 [確定]。
11. 選取 [對應此識別提供者的宣告]，然後設定下列宣告：
    
    - 針對 [使用者識別碼]，輸入 `oid`。
    - 針對 [顯示名稱]，輸入 `name`。
    - 針對 [名字]，輸入 `given_name`。
    - 針對 [姓氏]，輸入 `family_name`。
    - 針對 [電子郵件]，輸入 `unique_name`。

12. 按一下 [確定]，然後按一下 [建立] 以儲存您設定。
