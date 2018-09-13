---
title: 使用 Azure Active Directory B2C 設定註冊，並以 LinkedIn 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 LinkedIn 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e1949c32833bb1d5e6603a6f5e36e22dc58e8cec
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336923"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 LinkedIn 帳戶登入

## <a name="create-a-linkedin-application"></a>建立 LinkedIn 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 LinkedIn 帳戶做為識別提供者，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 LinkedIn 帳戶，可以在 [https://www.linkedin.com/](https://www.linkedin.com/) 中取得。

1. 使用您的 LinkedIn 帳戶認證登入 [LinkedIn 開發人員網站](https://www.developer.linkedin.com/)。
2. 選取 [我的應用程式]，然後按一下 [建立應用程式]。
3. 請輸入 [公司名稱][應用程式名稱]、[應用程式描述]、[應用程式標誌]、[應用程式使用]、[網站 URL]、[公司電子郵件]，以及 [公司電話]。
4. 同意 **LinkedIn API 使用條款**，然後按一下 [提交]。
5. 複製 [用戶端識別碼] 和 [用戶端密碼] 的值。 您可以在 [驗證金鑰] 下方找到這些資訊。 您必須使用這兩個值，將 LinkedIn 設為租用戶中的身分識別提供者。 **用戶端密碼** 是重要的安全性認證。
6. 請在 [授權重新導向 URL] 中輸入 `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`。 使用您的租用戶名稱 (例如 contosob2c) 來取代 **{tenant}**。 選取 [新增]，然後按一下 [更新]。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>將 LinkedIn 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的右上角切換到您的 Azure AD B2C 租用戶，確定您使用的目錄包含該租用戶。 選取您的訂用帳戶資訊，然後選取 [切換目錄]。 

    ![切換為您的 Azure AD B2C 租用戶](./media/active-directory-b2c-setup-li-app/switch-directories.png)

    選擇包含您租用戶的目錄。

    ![選取目錄](./media/active-directory-b2c-setup-li-app/select-directory.png)

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 提供 [名稱]。 例如，輸入 *LinkedIn*。
6. 依序選取 [識別提供者類型]、[LinkedIn]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]，並輸入 [用戶端識別碼] (您先前記錄的**用戶端識別碼**) ，然後輸入 [用戶端密碼] (您記錄之先前建立的 LinkedIn 帳戶應用程式的**用戶端密碼**)。
8. 按一下 [確定]，然後按一下 [建立]，儲存您的 LinkedIn 帳戶設定。

