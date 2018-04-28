---
title: Azure AD B2C 的 Twitter 設定 | Microsoft Docs
description: 在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Twitter 帳戶的客戶提供註冊和登入。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>使用 Azure AD B2C 對具有 Twitter 帳戶的取用者提供註冊和登入

## <a name="create-a-twitter-application"></a>建立 Twitter 應用程式
若要在 Azure Active Directory (Azure AD) B2C 中使用 Twitter 作為識別提供者，您必須建立 Twitter 應用程式，並對其提供正確參數。 您需要 Twitter 帳戶才能執行這項操作。 如果您沒有帳戶，您可以在 [https://twitter.com/signup](https://twitter.com/signup) 上取得。

1. 移至 [Twitter 應用程式](https://apps.twitter.com/)，並以您的認證登入。
2. 按一下 [建立新的應用程式] 。 
3. 在表單中，提供**名稱**、**描述**和**網站**值。
4. 在 [回呼 URL] 中輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 務必要使用您的租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**。
5. 勾選方塊以同意 [開發人員合約]，然後按一下 [建立 Twitter 應用程式]。
6. 應用程式建立完成後，按一下 [金鑰和存取權杖]。
7. 複製 [取用者金鑰] 和 [取用者祕密] 的值。 您必須使用這兩個值，將 Twitter 設為租用戶中的身分識別提供者。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 Twitter 設為識別提供者
1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。 
2. 若要切換為您的 Azure AD B2C 租用戶，請選取入口網站右上角的 Azure AD B2C 目錄。
3. 按一下 [所有服務]，然後從 [安全性 + 身分識別] 底下的服務清單選取 [Azure AD B2C]。
4. 按一下 [識別提供者]。
4. 針對身分識別提供者組態，提供容易辨識的 **名稱** 。 例如，輸入「Twitter」。
5. 按一下 [識別提供者類型]、選取 [Twitter (預覽)]，然後按一下 [確定]。
6. 按一下 [設定此識別提供者]，然後輸入 Twitter 的**取用者金鑰**來作為**用戶端識別碼**，以及輸入 Twitter 的**取用者祕密**來作為**用戶端祕密**。
7. 依序按一下 [確定] 和 [建立]，儲存您的 Twitter 設定。

## <a name="next-steps"></a>後續步驟

建立或編輯[內建原則](active-directory-b2c-reference-policies.md)，然後將 Twitter 新增為身分識別提供者。