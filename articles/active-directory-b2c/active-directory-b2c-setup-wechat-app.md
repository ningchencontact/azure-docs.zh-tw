---
title: 使用 Azure Active Directory B2C 設定註冊，並以 WeChat 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 WeChat 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 418f399213f2b1b60d4d4f464fd9fe89c6f35ba7
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338202"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 WeChat 帳戶登入

> [!NOTE]
> 這項功能處於預覽狀態。
> 

## <a name="create-a-wechat-application"></a>建立 WeChat 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 WeChat 帳戶做為識別提供者，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 WeChat 帳戶，可以至 [http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) 取得資訊。

### <a name="register-a-wechat-application"></a>註冊 WeChat 應用程式

1. 使用您的 WeChat 認證登入 [https://open.weixin.qq.com/](https://open.weixin.qq.com/)。
2. 選取 [管理中心]\(管理中心\)。
3. 遵循註冊新應用程式的步驟。
4. 在 [授权回调域]\(回呼 URL\) 中，輸入 `https://{tenant_name}.b2clogin.com/te/{tenant_name}.onmicrosoft.com/oauth2/authresp`。 例如，如果您的 `tenant_name` 是 contoso，請將 URL 設為 `https://contoso.b2clogin.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
5. 複製**應用程式識別碼**和**應用程式金鑰**。 您需要這些值，才能將識別提供者新增至您的租用戶。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 WeChat 設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的右上角切換到您的 Azure AD B2C 租用戶，確定您使用的目錄包含該租用戶。 選取您的訂用帳戶資訊，然後選取 [切換目錄]。 

    ![切換為您的 Azure AD B2C 租用戶](./media/active-directory-b2c-setup-wechat-app/switch-directories.png)

    選擇包含您租用戶的目錄。

    ![選取目錄](./media/active-directory-b2c-setup-wechat-app/select-directory.png)

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 提供 [名稱]。 例如，輸入 *WeChat*。
6. 依序選取 [識別提供者類型]、[WeChat (預覽)]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]，並輸入 [用戶端識別碼] (您先前記錄的用戶端識別碼)，然後輸入 [用戶端密碼] (您先前建立的 WeChat 應用程式的應用程式金鑰)。
8. 依序按一下 [確定] 和 [建立]，儲存您的 WeChat 設定。

