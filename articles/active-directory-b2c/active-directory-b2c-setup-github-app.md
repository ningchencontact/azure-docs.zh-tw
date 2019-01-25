---
title: 使用 Azure Active Directory B2C 設定註冊，並以 GitHub 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 GitHub 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8e87528f798e806217e9ee6b317f502570782791
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843722"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 GitHub 帳戶登入

> [!NOTE]
> 這項功能處於預覽狀態。
> 

若要在 Azure Active Directory (Azure AD) B2C 中使用 GitHub 帳戶做為識別提供者，您需要在租用戶中建立代表該帳戶的應用程式。 如果您還沒有 GitHub 帳戶，可以至 [https://www.github.com/](https://www.github.com/) \(英文\) 取得。

## <a name="create-a-github-oauth-application"></a>建立 GitHub OAuth 應用程式

1. 使用您的 GitHub 認證登入 [GitHub 開發人員](https://github.com/settings/developers)網站。
2. 選取 [OAuth 應用程式]，然後選取 [新增 OAuth 應用程式]。
3. 輸入 [Application name] \(應用程式名稱\) 和您的 [Homepage URL] \(首頁 URL\)。
4. 在 [授權回呼 URL] 中輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，仍請在輸入租用戶名稱時，全部使用小寫字母。
5. 按一下 [註冊應用程式]。
6. 複製 [用戶端識別碼] 和 [用戶端密碼] 的值。 您必須同時將識別提供者新增至您的租用戶。

## <a name="configure-a-github-account-as-an-identity-provider"></a>將 GitHub 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 提供 [名稱]。 例如，輸入 *GitHub*。
6. 選取 [識別提供者類型]，選取 [GitHub (預覽)]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]，並輸入您先前記錄為之前建立的 GitHub 帳戶應用程式之**用戶端識別碼**的 [用戶端識別碼]，然後輸入您記錄為**用戶端密碼**的 [用戶端密碼]。
8. 按一下 [確定]，然後按一下 [建立] 以儲存您的 GitHub 帳戶設定。
