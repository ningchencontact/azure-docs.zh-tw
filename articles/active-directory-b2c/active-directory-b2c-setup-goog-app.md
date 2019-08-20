---
title: 設定註冊, 並以 Google 帳戶登入-Azure Active Directory B2C
description: 使用 Azure Active Directory B2C，讓具有 Google 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7286bf1fd51883587aa41dc69d5dae0a3e6fb824
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622418"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Google 帳戶登入

## <a name="create-a-google-application"></a>建立 Google 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 Google 帳戶做為[識別提供者](active-directory-b2c-reference-oauth-code.md)，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 Google 帳戶, 您可以在[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)註冊。

1. 以您的 Google 帳戶認證登入 [Google 開發人員主控台](https://console.developers.google.com/)。
1. 在頁面的左上角, 選取 [專案] 清單, 然後選取 [**新增專案**]。
1. 輸入**專案名稱**、按一下 [建立]，然後確定您使用的是新專案。
1. 在左側功能表選取 [認證]，然後選取 [建立認證] > [Oauth 用戶端識別碼]。
1. 在 [應用程式類型] 下方，選取 [Web 應用程式]。
1. 輸入應用程式的**名稱**，在 [授權 JavaScript 來源] 中輸入 `https://your-tenant-name.b2clogin.com`，接著在 [授權重新導向 URI] 中輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。
1. 按一下 [建立]。
1. 複製 [用戶端識別碼] 和 [用戶端密碼] 的值。 您必須使用這兩個值，將 Google 設為租用戶中的身分識別提供者。 **用戶端密碼** 是重要的安全性認證。

## <a name="configure-a-google-account-as-an-identity-provider"></a>將 Google 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選, 然後選擇包含您租使用者的目錄, 以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [**識別提供者**], 然後選取 [ **Google**]。
1. 輸入 [名稱]。 例如, *Google*。
1. 針對 [**用戶端識別碼**], 輸入您稍早建立的 Google 應用程式的用戶端識別碼。
1. 針對 [**用戶端密碼**], 輸入您所記錄的用戶端密碼。
1. 選取 [ **儲存**]。
