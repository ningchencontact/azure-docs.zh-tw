---
title: 使用 Azure Active Directory B2C 設定註冊，並以 Twitter 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 Twitter 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bf5ae39d83fd021775fbd18cf23d2e6b9078e748
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927891"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Twitter 帳戶登入

## <a name="create-a-twitter-application"></a>建立 Twitter 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 Twitter 帳戶做為識別提供者，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 Twitter 帳戶，可以在 [https://twitter.com/signup](https://twitter.com/signup) 中取得。

1. 使用您的 Twitter 認證登入 [Twitter 應用程式](https://apps.twitter.com/)。
2. 選取 [Create New App] \(建立新的應用程式\)。
3. 輸入 [名稱]、[描述] 和 [網站]。
4. 請在 [回呼 URL] 中，輸入 `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`。 使用租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**，並使用原則識別碼 (例如 b2c_1_policy) 來取代 **{policyId}**。 您應該為所有使用 Twitter 帳戶的原則新增回呼 URL。 如果要在應用程式中使用，請務必使用 `b2clogin.com` 而不是 ` login.microsoftonline.com`。
5. 同意 [開發人員合約]，然後按一下 [建立 Twitter 應用程式]。
7. 選取 [ **金鑰和存取權杖** ] 索引標籤。
8. 複製 [取用者金鑰] 和 [取用者祕密] 的值。 您必須使用這兩個值，將 Twitter 帳戶設為租用戶中的識別提供者。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 Twitter 設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的右上角切換到您的 Azure AD B2C 租用戶，確定您使用的目錄包含該租用戶。 選取您的訂用帳戶資訊，然後選取 [切換目錄]。 

    ![切換為您的 Azure AD B2C 租用戶](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    選擇包含您租用戶的目錄。

    ![選取目錄](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 提供 [名稱]。 例如，輸入 *Twitter*。
6. 依序選取 [識別提供者類型]、[Twitter]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]，然後在 [用戶端識別碼] 輸入取用者金鑰，並在 [用戶端密碼] 輸入**取用者密碼**。
8. 依序按一下 [確定] 和 [建立]，儲存您的 Twitter 設定。