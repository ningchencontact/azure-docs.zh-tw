---
title: "GitHub 身分識別提供者設定 - Azure AD B2C | Microsoft Docs"
description: "在受 Azure Active Directory B2C 保護的應用程式中，針對具有 GitHub 帳戶的客戶提供註冊和登入。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 5518e135-36b3-4f86-9a01-be25f021ed22
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parja
ms.openlocfilehash: e827bea257309f6d6dda1af4e68ccda5a26d30ef
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C：針對具有 GitHub 帳戶的取用者提供註冊和登入

> [!NOTE]
> 這項功能處於預覽狀態。
> 

本文將說明如何針對具有 GitHub 帳戶的使用者啟用登入。

## <a name="create-a-github-oauth-application"></a>建立 GitHub OAuth 應用程式

若要在 Azure AD B2C 中使用 GitHub 作為身分識別提供者，您必須建立 GitHub OAuth 應用程式，並為其提供正確參數。

1. 在登入 GitHub 之後，移至 [GitHub 開發人員設定](https://github.com/settings/developers)。
1. 按一下 [New OAuth App] \(新增 OAuth 應用程式\)
1. 輸入 [Application name] \(應用程式名稱\) 和您的 [Homepage URL] \(首頁 URL\)。
1. 針對 [Authorization callback URL] \(授權回呼 URL\)，輸入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 使用您的 Azure AD B2C 租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 **{tenant}**。

    >[!NOTE]
    >[Sign-on URL] \(登入 URL\) 中 "tenant" 的值必須全部小寫。

1. 按一下 [註冊應用程式]。
1. 儲存 [Client ID] \(用戶端識別碼\) 和 [Client Secret] \(用戶端密碼\) 的值。 在下一節中將會需要這兩項資料。

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>將 GitHub 帳戶設定為 Azure AD B2C 租用戶中的身分識別提供者

1. 遵循下列步驟以 [瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
1. 在 B2C 功能刀鋒視窗中，按一下 [ **身分識別提供者**]。
1. 按一下刀鋒視窗頂端的 [新增]  。
1. 針對身分識別提供者組態，提供容易辨識的 **名稱** 。 例如，輸入 "GitHub"。
1. 按一下 [身分識別提供者類型]、選取 [GitHub]，然後按一下 [確定]。
1. 按一下 [設定此身分識別提供者] ，然後輸入您先前複製的 GitHub OAuth 應用程式用戶端識別碼和用戶端密碼。
1. 依序按一下 [確定] 和 [建立]來儲存您的 GitHub 設定。

## <a name="next-steps"></a>後續步驟

建立或編輯[內建原則](active-directory-b2c-reference-policies.md)，然後將 GitHub 新增為身分識別提供者。