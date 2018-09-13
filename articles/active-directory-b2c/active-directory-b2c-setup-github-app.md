---
title: 使用 Azure Active Directory B2C 設定註冊，並以 GitHub 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 GitHub 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11f3f190c0f55e45c549a8bd1de35f78eb7b752d
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337424"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 GitHub 帳戶登入

> [!NOTE]
> 這項功能處於預覽狀態。
> 

若要在 Azure Active Directory (Azure AD) B2C 中使用 GitHub 帳戶做為識別提供者，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 GitHub 帳戶，可以至 [https://www.github.com/](https://www.github.com/) 取得。

## <a name="create-a-github-oauth-application"></a>建立 GitHub OAuth 應用程式

1. 使用您的 GitHub 認證登入 [GitHub 開發人員](https://github.com/settings/developers)網站。
2. 選取 [OAuth 應用程式]，然後選取 [註冊新的應用程式]。
3. 輸入 [Application name] \(應用程式名稱\) 和您的 [Homepage URL] \(首頁 URL\)。
4. 在 [授權回呼 URL] 中輸入 `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`。 使用您的 Azure AD B2C 租用戶名稱 (例如 contosob2c) 來取代 **{tenant}**。
5. 按一下 [註冊應用程式]。
6. 複製 [用戶端識別碼] 和 [用戶端密碼] 的值。 您必須同時將識別提供者新增至您的租用戶。

## <a name="configure-a-github-account-as-an-identity-provider"></a>將 GitHub 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的右上角切換到您的 Azure AD B2C 租用戶，確定您使用的目錄包含該租用戶。 選取您的訂用帳戶資訊，然後選取 [切換目錄]。 

    ![切換為您的 Azure AD B2C 租用戶](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    選擇包含您租用戶的目錄。

    ![選取目錄](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 提供 [名稱]。 例如，輸入 *Github*。
6. 依序選取 [識別提供者類型]、[Github (預覽)]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]，並輸入 [用戶端識別碼] (您先前記錄的**用戶端識別碼**) ，然後輸入 [用戶端密碼] (您記錄之先前建立的 Github 帳戶應用程式的**用戶端密碼**)。
8. 依序按一下 [確定] 和 [建立]，以儲存您的 Github 帳戶設定。