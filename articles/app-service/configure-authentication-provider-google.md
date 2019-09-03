---
title: 設定 Google 驗證 - Azure App Service
description: 瞭解如何為您的 App Service 應用程式設定 Google 驗證。
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232143"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>如何設定 App Service 應用程式以使用 Google 登入
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定 Azure App Service，以使用 Google 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。 若要建立新的 Google 帳戶，請前往 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register"> </a>向 Google 註冊您的應用程式
1. 依照 google 登[入伺服器端應用程式](https://developers.google.com/identity/sign-in/web/server-side-flow)的 google 檔, 建立用戶端識別碼和用戶端密碼, 並提供下列資訊 (不需要變更任何程式碼):
    - 針對**授權的 JavaScript 來源**, `https://<app-name>.azurewebsites.net`請在 *\<應用*程式名稱中使用與您的應用程式名稱 >。
    - 針對**授權**的重新導向 URI `https://<app-name>.azurewebsites.net/.auth/login/google/callback`, 請使用。
1. 建立用戶端識別碼和用戶端秘密之後, 請複製其值。

    > [!IMPORTANT]
    > 用戶端密碼是重要的安全性認證。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。


## <a name="secrets"> </a>將 Google 資訊新增至應用程式
1. 在 [Azure 入口網站]中，瀏覽至您的 App Service 應用程式。 從左側功能表中, 選取 [**驗證/授權**]。
2. 如果 [驗證/授權] 功能未啟用，請切換到 [開]。
3. 按一下 [Google]。 貼上您先前取得的 [應用程式識別碼] 和 [應用程式密碼] 值, 並選擇性地啟用應用程式所需的任何範圍。 然後按一下 [確定]。

   App Service 提供驗證, 但不會限制對您網站內容和 Api 的已授權存取。 如需詳細資訊, 請參閱[授權或拒絕使用者](app-service-authentication-how-to.md#authorize-or-deny-users)。
4. (選擇性) 若要限制只有透過 Google 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作] 設為 [Google]。 這會要求所有要求都需經過驗證，且所有未經驗證的要求都會重新導向至 Google 以進行驗證。

    > [!NOTE]
    > 以這種方式限制存取適用于應用程式的所有呼叫, 這對於想要公開使用首頁的應用程式 (如許多單頁應用程式), 可能不是理想的做法。 對於這類應用程式, 可能會慣用 [**允許匿名要求 (無動作)** ], 而應用程式會以手動方式啟動登入本身, 如[這裡](overview-authentication-authorization.md#authentication-flow)所述。
    
5. 按一下 [儲存]。

現在，您已可在應用程式中使用 Google 進行驗證。

## <a name="related-content"> </a>相關內容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 入口網站]: https://portal.azure.com/

