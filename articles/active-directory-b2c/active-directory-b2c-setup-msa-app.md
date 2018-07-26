---
title: 使用 Azure Active Directory B2C 設定註冊，並以 Microsoft 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 Microsoft 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 16e4dbac4c8146b048d4d9b76544677a6111e2a5
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900824"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Microsoft 帳戶登入

## <a name="create-a-microsoft-account-application"></a>建立 Microsoft 帳戶應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 Microsoft 帳戶做為識別提供者，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 Microsoft 帳戶，可以至 [https://www.live.com/](https://www.live.com/) 取得。

1. 請使用您的 Microsoft 帳戶認證登入 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。
2. 在右上角選取 [新增應用程式]。
3. 為應用程式提供 [名稱]，然後按一下 [建立]。
4. 在註冊頁面上，複製 [應用程式識別碼] 的值。您必須使用此值，在租用戶中將 Microsoft 帳戶設定為識別提供者。
5. 選取 [新增平台]，然後選擇 [Web]。
6. 在 [重新導向 URL] 中，輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 使用您的租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**。
7. 在 [應用程式密碼] 下，選取 [產生新密碼]。 複製螢幕上顯示的新密碼。 您必須使用此值，在租用戶中將 Microsoft 帳戶設定為識別提供者。 此密碼是重要的安全性認證。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>將 Microsoft 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的右上角切換到您的 Azure AD B2C 租用戶，確定您使用的目錄包含該租用戶。 選取您的訂用帳戶資訊，然後選取 [切換目錄]。 

    ![切換為您的 Azure AD B2C 租用戶](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    選擇包含您租用戶的目錄。

    ![選取目錄](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 提供 [名稱]。 例如，輸入 *MSA*。
6. 選取 [識別提供者類型]、選取 [Microsoft 帳戶]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]，並輸入 [用戶端識別碼] (您先前記錄下來的應用程式識別碼)，然後輸入 [用戶端密碼] (您先前建立的 Microsoft 帳戶應用程式的密碼)。
8. 依序按一下 [確定] 與 [建立]，以儲存您的 Microsoft 帳戶設定。

