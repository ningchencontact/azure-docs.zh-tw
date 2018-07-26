---
title: 使用 Azure Active Directory B2C 設定註冊，並以 Google 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 Google 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dd0bf50d73b70e37195e8e5e45336b68e4e883e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915634"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Google 帳戶登入

## <a name="create-a-google-application"></a>建立 Google 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 Google 帳戶做為識別提供者，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 Google 帳戶，可以在 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) 中取得。

1. 以您的 Google 帳戶認證登入 [Google 開發人員主控台](https://console.developers.google.com/)。
2. 選取 [建立專案]，然後按一下 [建立]。 如果您先前曾建立過專案，請選取專案清單，然後選取 [新增專案]。
3. 輸入 [專案名稱]，然後按一下 [建立]。
3. 在左側功能表選取 [認證]，然後選取 [建立認證] > [Oauth 用戶端識別碼]。
4. 選取 [設定同意畫面]。
5. 選取或指定有效的**電子郵件地址**、提供**向使用者顯示的產品名稱**，然後按一下 [儲存]。
6. 在 [應用程式類型] 下方，選取 [Web 應用程式]。
7. 輸入應用程式的**名稱**，在 [授權 JavaScript 來源] 中輸入 `https://login.microsoftonline.com`，接著在 [授權重新導向 URI] 中輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 使用您的租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**。
8. 按一下頁面底部的 [新增] 。
9. 複製 [用戶端識別碼] 和 [用戶端密碼] 的值。 您必須使用這兩個值，將 Google 設為租用戶中的身分識別提供者。 **用戶端密碼** 是重要的安全性認證。

## <a name="configure-a-google-account-as-an-identity-provider"></a>將 Google 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的右上角切換到您的 Azure AD B2C 租用戶，確定您使用的目錄包含該租用戶。 選取您的訂用帳戶資訊，然後選取 [切換目錄]。 

    ![切換為您的 Azure AD B2C 租用戶](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    選擇包含您租用戶的目錄。

    ![選取目錄](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 輸入 [名稱]。 例如，輸入 *Google*。
6. 選取 [識別提供者類型]、選取 [Google]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]，並輸入 [用戶端識別碼] (您先前記錄的用戶端識別碼)，然後輸入 [用戶端密碼] (您記錄之先前建立的 Google 應用程式的用戶端密碼)。
8. 依序按一下 [確定] 和 [建立]，儲存您的 Google 設定。

