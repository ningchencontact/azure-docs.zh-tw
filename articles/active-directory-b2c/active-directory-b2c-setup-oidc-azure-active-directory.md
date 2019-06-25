---
title: 設定 Azure Active Directory 組織的登入 - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28dbf0382ac151857e72d4bb59e207f07c8ad3f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508429"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定特定 Azure Active Directory 組織的登入

>[!NOTE]
> 這項功能處於公開預覽狀態。 請勿在生產環境中使用這項功能。

若要在 Azure AD B2C 中使用 Azure Active Directory (Azure AD) 做為[識別提供者](active-directory-b2c-reference-oauth-code.md)，您需要建立代表該識別提供者的應用程式。 本文說明如何讓特定 Azure AD 組織中的使用者能夠使用 Azure AD B2C 中的使用者流程登入。

## <a name="create-an-azure-ad-app"></a>建立 Azure AD 應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式，此租用戶與您的 Azure AD B2C 租用戶不同。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請確定您使用包含 Azure AD 租用戶的目錄。 選取 **目錄和訂用帳戶篩選**上方功能表中，然後選擇包含您的 Azure AD 租用戶的目錄。 這不是相同的租用戶與 Azure AD B2C 租用戶。
3. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [應用程式註冊]  。
4. 選取 [新增註冊]  。
5. 輸入應用程式的名稱。 例如： `Azure AD B2C App`。
6. 接受的選項**只有此組織目錄中的帳戶**此應用程式。
7. 針對**重新導向 URI**，接受值**Web**，並輸入下列 URL 中的所有小寫字母，其中`your-B2C-tenant-name`會取代為您的 Azure AD B2C 租用戶的名稱。 例如，`https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`：

    ```
    https://your--B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。

8. 按一下 [註冊]  。 複製**應用程式 （用戶端） 識別碼**以供稍後使用。
9. 選取 **憑證與祕密**應用程式功能表，然後選取**新的用戶端祕密**。
10. 輸入用戶端祕密的名稱。 例如： `Azure AD B2C App Secret`。
11. 選取的逾期期限。 針對此應用程式中，接受的選項**1 年**。
12. 選取 **新增**複製的值會顯示以供稍後使用的新用戶端祕密。

## <a name="configure-azure-ad-as-an-identity-provider"></a>將 Azure AD 設定成識別提供者

1. 請確定您使用包含 Azure AD B2C 租用戶的目錄。 選取 **目錄和訂用帳戶篩選**上方功能表中，然後選擇包含您的 Azure AD B2C 租用戶的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
3. 選取 [識別提供者]  ，然後選取 [新增]  。
4. 輸入 [名稱]  。 例如，輸入 `Contoso Azure AD`。
5. 選取 [識別提供者類型]  ，選取 [Open ID Connect (預覽)]  ，然後按一下 [確定]  。
6. 選取**設定此身分識別提供者**
7. 針對 [中繼資料 URL]  輸入以下 URL，並將 `your-AD-tenant-domain` 取代為您的 Azure AD 租用戶網域名稱。 例如：`https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. 針對**用戶端識別碼**，輸入您先前記錄下來的應用程式識別碼以及**用戶端祕密**，輸入您先前記錄下來的用戶端密碼。
9. 您也可以選擇輸入 **Domain_hint** 的值。 例如： `ContosoAD`。 這是在要求中使用 *domain_hint* 參考此識別提供者時，要使用的值。 
10. 按一下 [確定]  。
11. 選取 [對應此識別提供者的宣告]  ，然後設定下列宣告：
    
    - 針對 [使用者識別碼]  ，輸入 `oid`。
    - 針對 [顯示名稱]  ，輸入 `name`。
    - 針對 [名字]  ，輸入 `given_name`。
    - 針對 [姓氏]  ，輸入 `family_name`。
    - 針對 [電子郵件]  ，輸入 `unique_name`。

12. 按一下 [確定]  ，然後按一下 [建立]  以儲存您設定。
