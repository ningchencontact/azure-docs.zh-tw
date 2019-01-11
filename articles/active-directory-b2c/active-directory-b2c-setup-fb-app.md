---
title: 使用 Azure Active Directory B2C 設定註冊，並以 Facebook 帳戶登入 | Microsoft Docs
description: 使用 Azure Active Directory B2C，讓具有 Facebook 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 183afaeb40b90de078a44f4677dd0218b8d86ba8
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2019
ms.locfileid: "53968975"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Facebook 帳戶登入

## <a name="create-a-facebook-application"></a>建立 Facebook 應用程式

若要在 Azure Active Directory (Azure AD) B2C 中使用 Facebook 帳戶做為識別提供者，您需要在代表該帳戶的租用戶中建立應用程式。 如果您還沒有 Facebook 帳戶，可以至 [https://www.facebook.com/](https://www.facebook.com/) 取得。

1. 請以您的 Facebook 帳戶認證登入 [Facebook for developers (開發人員專用的 Facebook)](https://developers.facebook.com/)。
2. 如果您尚未這麼做，您需要註冊為 Facebook 開發人員。 若要這樣做，請選取頁面右上角的 [註冊]，接受 Facebook 的原則，然後完成註冊步驟。
3. 選取 [My Apps] \(我的應用程式\)，然後按一下 [Add a New App] \(新增應用程式\)。 
4. 輸入**顯示名稱**和有效的**連絡人電子郵件**。
5. 按一下 [建立應用程式識別碼]。 這可能會要求您接受 Facebook 平台原則，並完成線上安全性檢查。
6. 選取 [設定] > [基本]。
7. 選擇**分類**，例如 `Business and Pages`。 此為 Facebook 所需的值，但不會用於 Azure AD B2C。
8. 在頁面底部選取 [新增平台]，然後選取 [網站]。
9. 在 [Site URL] \(網站 URL\) 中，輸入 `https://your-tenant-name.b2clogin.com/`，並將 `your-tenant-name` 取代為您的租用戶名稱。 輸入**隱私權原則 URL** 的 URL，如 `http://www.contoso.com`。 原則 URL 是您需維護以提供應用程式隱私權資訊的網頁。
10. 選取 [Save Changes] \(儲存變更\)。
11. 在頁面頂端複製 [應用程式識別碼] 的值。 
12. 按一下 [顯示]，並複製 [應用程式密碼] 的值。 您必須同時使用這兩個值，將 Facebook 設定為租用戶中的身分識別提供者。 **應用程式密碼**是重要的安全性認證。
13. 選取 [產品]，然後在 [Facebook 登入] 下方選取 [設定]。
14. 在 [Facebook 登入] 下方選取 [設定]。
15. 在 [Valid OAuth redirect URIs] \(有效的 OAuth 重新導向 URI\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 按一下頁面底部的 [儲存變更]。
16. 若要讓 Azure AD B2C 能使用您的 Facebook 應用程式，請按一下頁面右上方的 [狀態選取器]，然後將其切換至 [開啟]，以將應用程式設為公用，然後按一下 [確認]。  此時，狀態應該會從 [開發] 變更為 [作用中]。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>將 Facebook 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。 
3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後選取 [新增]。
5. 輸入 [名稱]。 例如，輸入 *Facebook*。
6. 選取 [識別提供者類型]、選取 [Facebook]，然後按一下 [確定]。
7. 選取 [設定此識別提供者]、輸入您先前記錄的應用程式識別碼作為**用戶端識別碼**，然後輸入您所記錄的應用程式祕密作為您先前建立的 Facebook 應用程式**用戶端密碼**。
8. 依序按一下 [確定] 與 [建立]，以儲存您的 Facebook 設定。
