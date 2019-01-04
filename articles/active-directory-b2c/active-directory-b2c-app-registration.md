---
title: 在 Azure Active Directory B2C 中註冊應用程式 | Microsoft Docs
description: 了解如何向 Azure Active Directory B2C 註冊您的應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fcebada4ef10c3e0bb298e9308d66ecb37247832
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999264"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中註冊應用程式

若要建置可接受取用者註冊與登入的[應用程式](active-directory-b2c-apps.md)，您必須先向 Azure AD B2C 租用戶註冊該應用程式。 本文可協助您在幾分鐘內於 Azure Active Directory (Azure AD) B2C 租用戶中註冊應用程式。 當您完成時，應用程式就已註冊完成，以便在 Azure AD B2C 租用戶中使用。

## <a name="prerequisites"></a>必要條件

使用[建立 Azure Active Directory B2C 租用戶](tutorial-create-tenant.md)中的步驟建立您自己的租用戶。

根據您的應用程式類型選擇後續步驟：

- [註冊 Web 應用程式](#register-a-web-application)
- [註冊 Web API](#register-a-web-api)
- [註冊行動或原生應用程式](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>註冊 Web 應用程式

1. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
3. 選取 [應用程式]，然後選取 [新增]。
4. 輸入應用程式的名稱。 例如，*testapp1*。
5. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
6. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 例如，您可以將它設定為在 `https://localhost:44316` 於本機接聽。 如果還不知道連接埠號碼，您可以輸入預留位置值，之後再變更。
7. 按一下頁面底部的 [新增] 。

### <a name="create-a-client-secret"></a>建立用戶端密碼

如果應用程式呼叫由 Azure AD B2C 保護的 Web API，您需要建立應用程式密碼。

1. 選取 [金鑰]，然後按一下 [產生金鑰]。 
2. 選取 [儲存] 以檢視金鑰。 請記下 [應用程式金鑰] 值。 您可以使用此值，作為應用程式程式碼中的應用程式祕密。
3. 選取 [API 存取]，按一下 [新增]，然後選取您的 Web API 和範圍 (權限)。

## <a name="register-a-web-api"></a>註冊 Web API

1. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
3. 選取 [應用程式]，然後選取 [新增]。
4. 輸入應用程式的名稱。 例如，*testapp2*。
5. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
6. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 例如，您可以將它設定為在 `https://localhost:44316` 於本機接聽。 如果還不知道連接埠號碼，您可以輸入預留位置值，之後再變更。
7. 針對**應用程式識別碼 URI**，請輸入您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 例如： `https://contosotenant.onmicrosoft.com/api`。
8. 按一下頁面底部的 [新增] 。
9. 選取 [已發行的範圍]，視需要新增更多範圍。 根據預設，已定義 `user_impersonation` 範圍。 `user_impersonation` 範圍讓其他應用程式能夠代表已登入的使用者存取此 API。 如果您希望的話，可以移除 `user_impersonation` 範圍。

## <a name="register-a-mobile-or-native-application"></a>註冊行動或原生應用程式

1. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
3. 選取 [應用程式]，然後選取 [新增]。
4. 輸入應用程式的名稱。 例如，*testapp3*。
5. 針對 [包含 Web 應用程式/Web API]，選取 [否]。
6. 針對 [包含原生用戶端]，選取 [是]。
7. 針對 [重新導向 URI]，輸入[具有自訂配置的重新導向 URI](active-directory-b2c-apps.md)。 務必選擇良好的重新導向 URI，且請勿包含特殊字元 (例如底線)。
8. 按一下頁面底部的 [新增] 。

### <a name="create-a-client-secret"></a>建立用戶端密碼

如果應用程式呼叫由 Azure AD B2C 保護的 Web API，您需要建立應用程式密碼。

1. 選取 [金鑰]，然後按一下 [產生金鑰]。 
2. 選取 [儲存] 以檢視金鑰。 請記下 [應用程式金鑰] 值。 您可以使用此值，作為應用程式程式碼中的應用程式祕密。
3. 選取 [API 存取]，按一下 [新增]，然後選取您的 Web API 和範圍 (權限)。

## <a name="next-steps"></a>後續步驟

在[要求存取權杖](active-directory-b2c-access-tokens.md)中，深入了解應用程式如何使用存取權杖授與 API 的權限
