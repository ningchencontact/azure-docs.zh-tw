---
title: 使用 Azure Active Directory B2C 設定註冊，並以 QQ 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 QQ 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 82668446f139a5a003c33178e2d415a9314c61bc
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952172"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 QQ 帳戶登入

> [!NOTE]
> 這項功能處於預覽狀態。
> 

## <a name="create-a-qq-application"></a>建立 QQ 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 QQ 帳戶做為識別提供者，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 QQ 帳戶，可以至 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) 取得。

### <a name="register-for-the-qq-developer-program"></a>註冊 QQ 開發人員計劃

1. 使用您的 QQ 帳戶認證登入 [QQ 開發人員入口網站](http://open.qq.com)。
2. 登入之後，請移至 [http://open.qq.com/reg](http://open.qq.com/reg)，將您自己註冊為開發人員。
3. 選取 [个人]\(個人開發人員\)。
4. 輸入必要資訊，並選取 [下一步]\(下一步\)。
5. 完成電子郵件驗證程序。 在註冊為開發人員之後，您必須等待數天以進行核准。 

### <a name="register-a-qq-application"></a>註冊 QQ 應用程式

1. 移至 [https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
2. 選取 [应用管理]\(應用程式管理\)。
5. 選取 [创建应用]\(建立應用程式\)，然後輸入所需的資訊。
7. 在 [授权回调域]\(回呼 URL\) 中，輸入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果您的 `tenant_name` 是 contoso.onmicrosoft.com，請將 URL 設為 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
8. 選取 [创建应用]\(建立應用程式\)。
9. 在 [確認] 頁面上，選取 [应用管理]\(應用程式管理\) 以返回應用程式管理頁面。
10. 在您建立的應用程式旁邊，選取 [查看]\(檢視\)。
11. 選取 [修改]\(編輯\)。
12. 複製**應用程式識別碼**和**應用程式金鑰**。 您需要這兩個值，才能將識別提供者新增至您的租用戶。

## <a name="configure-qq-as-an-identity-provider"></a>將 QQ 設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的右上角切換到您的 Azure AD B2C 租用戶，確定您使用的目錄包含該租用戶。 選取您的訂用帳戶資訊，然後選取 [切換目錄]。 

    ![切換為您的 Azure AD B2C 租用戶](./media/active-directory-b2c-setup-qq-app/switch-directories.png)

    選擇包含您租用戶的目錄。

    ![選取目錄](./media/active-directory-b2c-setup-qq-app/select-directory.png)

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 提供 [名稱]。 例如，輸入 *QQ*。
6. 依序選取 [識別提供者類型]、[QQ (預覽)]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]，並輸入 [用戶端識別碼] (您先前記錄的用戶端識別碼)，然後輸入 [用戶端密碼] (您先前建立的 QQ 應用程式的應用程式金鑰)。
8. 依序按一下 [確定] 和 [建立]，儲存您的 QQ 設定。

