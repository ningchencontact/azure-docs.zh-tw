---
title: 註冊和使用 Microsoft 帳戶-Azure Active Directory B2C 登入設定
description: 使用 Azure Active Directory B2C，讓具有 Microsoft 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 82c1be335bfd39d641f0203116e68a4cb4c0a674
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654218"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Microsoft 帳戶登入

## <a name="create-a-microsoft-account-application"></a>建立 Microsoft 帳戶應用程式

若要使用 Microsoft 帳戶作為[身分識別提供者](active-directory-b2c-reference-oidc.md)在 Azure Active Directory (Azure AD) B2C 中，您需要在 Azure AD 租用戶中建立應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。 如果您還沒有 Microsoft 帳戶，您可以取得一個[ https://www.live.com/ ](https://www.live.com/)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下頂端功能表中的 [目錄和訂用帳戶篩選]  ，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [應用程式註冊]  。
1. 選取 [新增註冊]  。
1. 輸入應用程式的**名稱**。 例如 *MSAapp1*。
1. 底下**支援的帳戶類型**，選取**中任何組織的目錄和個人 Microsoft 帳戶 (例如 Skype、 Xbox、 Outlook.com) 帳戶**。 此選項會以最寬的 Microsoft 身分識別集合為目標。

   如需有關不同的帳戶類型選項的詳細資訊，請參閱[快速入門：註冊應用程式與 Microsoft 身分識別平台](../active-directory/develop/quickstart-register-app.md)。
1. 底下 **（選擇性） 的 重新導向 URI**，選取**Web** ，然後輸入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`在文字方塊中。 取代`your-tenant-name`以您的 Azure AD B2C 租用戶名稱。
1. 選取**註冊**
1. 資料錄**應用程式 （用戶端） 識別碼**應用程式的 [概觀] 頁面上所示。 您需要這一節中設定身分識別提供者時。
1. 選取**憑證與密碼**
1. 按一下 **新的用戶端祕密**
1. 請輸入**描述**祕密，例如*應用程式密碼 1*，然後按一下**新增**。
1. 記錄中顯示的應用程式密碼**值**資料行。 您需要這一節中設定身分識別提供者時。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>將 Microsoft 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 按一下頂端功能表中的 [目錄和訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]  ，搜尋並選取 [Azure AD B2C]  。
1. 選取 [識別提供者]  ，然後選取 [新增]  。
1. 提供 [名稱]  。 例如，輸入 *MSA*。
1. 選取 [識別提供者類型]  、選取 [Microsoft 帳戶]  ，然後按一下 [確定]  。
1. 選取 **設定此身分識別提供者**並輸入您稍早在錄製的應用程式 （用戶端） 識別碼**用戶端識別碼**文字，然後輸入您記錄在用戶端祕密**用戶端祕密**文字方塊。
1. 依序按一下 [確定]  與 [建立]  ，以儲存您的 Microsoft 帳戶設定。
