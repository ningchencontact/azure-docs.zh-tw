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
ms.openlocfilehash: 0832c1e5f10cdb8e1d7a2edbb88162230ab13401
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233076"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>如何設定 App Service 應用程式以使用 Microsoft 帳戶登入
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定 Azure App Service，以使用 Microsoft 帳戶作為驗證提供者。 

## <a name="register-microsoft-account"> </a>使用 Microsoft 帳戶註冊應用程式
1. 登入[Azure 入口網站], 然後流覽至您的應用程式。 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. 流覽至[**應用程式註冊**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), 並在要求時使用您的 Microsoft 帳戶登入。

1. 按一下 [**新增註冊**], 然後輸入應用程式名稱。

1. 在 [重新**導向 uri**] 中, 選取 [ `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application` **Web**], 然後輸入。 將 *\<應用程式功能變數名稱 >* 取代為應用程式的功能變數名稱。  例如： `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback` 。 

   > [!NOTE]
   > 在 URL 中使用 HTTPS 配置。

1. 選取 [**註冊**]。 

1. 複製**應用程式 (用戶端) 識別碼**。 您稍後將會用到此資訊。 
   
7. 從新應用程式註冊的左側導覽中, 選取 **憑證 & 秘密** >  **新用戶端密碼**。 提供 [描述], 選取 [有效期間], 然後選取 [**新增**]。

1. 複製 [**憑證 & 密碼**] 頁面中顯示的值。 一旦您離開此頁面，就不會再次顯示。

    > [!IMPORTANT]
    > 密碼是重要的安全性認證。 請勿與任何人共用密碼，或在用戶端應用程式內散佈密碼。

## <a name="secrets"> </a>將 Microsoft 帳戶資訊新增至 App Service 應用程式
1. 在 [Azure 入口網站]中，瀏覽到您的應用程式。 從左側導覽中, 按一下 [**驗證/授權**]。

2. 如果 [驗證/授權] 功能未啟用, 請選取 [**開啟**]。

3. 在 [**驗證提供者**] 底下, 選取 [ **Microsoft 帳戶**]。 貼上您稍早取得的應用程式 (用戶端) 識別碼和用戶端密碼, 並選擇性地啟用您的應用程式所需的任何範圍。 然後按一下 [確定]。

    App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。

4. 選擇性若要限制 Microsoft 帳戶使用者的存取權, 請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Microsoft 帳戶登入**]。 這會要求所有的要求都經過驗證，且所有未經驗證的要求會重新導向至 Microsoft 帳戶以進行驗證。

> [!NOTE]
> 以這種方式限制存取適用于應用程式的所有呼叫, 這對於想要公開使用首頁的應用程式 (如許多單頁應用程式), 可能不是理想的做法。 對於這類應用程式, 可能會慣用 [**允許匿名要求 (無動作)** ], 而應用程式會以手動方式啟動登入本身, 如[這裡](overview-authentication-authorization.md#authentication-flow)所述。

5. 按一下 [儲存]。

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="related-content"> </a>相關內容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 入口網站]: https://portal.azure.com/
