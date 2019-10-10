---
title: 設定 Microsoft 帳戶驗證 - Azure App Service
description: 瞭解如何為您的 App Service 應用程式設定 Microsoft 帳戶驗證。
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 70af534e6bcd0039dbc602a5ebc3fc35fb145e79
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176941"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>設定您的 App Service 應用程式以使用 Microsoft 帳戶登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定 Azure App Service，以使用 Microsoft 帳戶作為驗證提供者。 

## <a name="register-microsoft-account"> </a>使用 Microsoft 帳戶註冊應用程式

1. 移至 Azure 入口網站中的[**應用程式註冊**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)。 如有需要，請使用您的 Microsoft 帳戶登入。
1. 選取 [**新增註冊**]，然後輸入應用程式名稱。
1. 在 [重新**導向 uri**] 中，選取 [ **Web**]，然後輸入 `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`。 以您應用程式的功能變數名稱取代 *\<app-功能變數名稱 >* 。  例如，`https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`。 請務必在 URL 中使用 HTTPS 配置。

1. 選取 [註冊]。
1. 複製**應用程式（用戶端）識別碼**。 稍後您將會用到此資訊。
1. 從左側窗格中，選取 [**憑證] & [密碼**]  >  [**新增用戶端密碼**]。 輸入 [描述]，選取 [有效期間]，然後選取 [**新增**]。
1. 複製 [**憑證 & 密碼**] 頁面上顯示的值。 離開頁面之後，將不會再次顯示。

    > [!IMPORTANT]
    > 密碼是重要的安全性認證。 請勿與任何人共用密碼，或在用戶端應用程式內散佈密碼。

## <a name="secrets"> </a>將 Microsoft 帳戶資訊新增至 App Service 應用程式

1. 在[Azure 入口網站]中，移至您的應用程式。
1. 選取 [**設定**] [ >  個**驗證/授權**]，並確定**App Service 驗證**已**開啟**。
1. 在 [**驗證提供者**] 底下，選取 [ **Microsoft 帳戶**]。 貼上您稍早取得的應用程式（用戶端）識別碼和用戶端密碼。 啟用應用程式所需的任何範圍。
1. 選取 [確定]。

   App Service 提供驗證，但不會限制對您網站內容和 Api 的已授權存取。 您必須在應用程式程式碼中授權使用者。

1. 選擇性若要限制 Microsoft 帳戶使用者的存取權，請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Microsoft 帳戶登入**]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 它也會將所有未驗證的要求重新導向至 Microsoft 帳戶進行驗證。

   > [!CAUTION]
   > 以這種方式限制存取適用于對您應用程式的所有呼叫，這對於具有公開可用首頁的應用程式（如許多單頁應用程式），可能不是理想的做法。 對於這類應用程式，可能會慣用 [**允許匿名要求（無動作）** ]，讓應用程式手動啟動驗證。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]。

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 入口網站]: https://portal.azure.com/
