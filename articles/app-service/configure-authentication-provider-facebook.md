---
title: 設定 Facebook 驗證 - Azure App Service
description: 了解如何為您的應用程式服務應用程式設定 Facebook 驗證。
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e91d55c29d325301b8ac70ddc63fb408961fbb2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742971"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>如何設定 App Service 應用程式以使用 Facebook 登入
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定 Azure App Service，以使用 Facebook 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。 若要建立新的 Facebook 帳戶，請前往 [facebook.com]。

## <a name="register"> </a>向 Facebook 註冊您的應用程式
1. 瀏覽至 [Facebook 開發人員] 網站，並以您的 Facebook 帳戶認證登入。
3. （選擇性）如果您不是 Facebook 開發人員帳戶，請按一下**開始**並遵循註冊步驟。
4. 按一下 **我的應用程式** > **加入新的應用程式**。
5. 在 [顯示名稱]  中，輸入應用程式的唯一名稱。 另請提供您的 [連絡電子郵件]  ，然後按一下 [建立應用程式識別碼]  並完成安全性檢查。 這會將您帶到開發人員儀表板來設定新的 Facebook 應用程式。
6. 按一下 **儀表板** > **Facebook 登入** > **設定** > **Web**。
1. 在左側導覽中之下**Facebook 登入**，按一下**設定**。
1. 在 **有效的 OAuth 重新導向 Uri**，型別`https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`並取代 *\<應用程式名稱 >* Azure App Service 應用程式的名稱。 按一下 **儲存變更**。
8. 在左側導覽中按一下 [設定]   >  [基本]  。 在 **應用程式祕密**欄位中，按一下**顯示**。 值複製**應用程式識別碼**並**應用程式祕密**。 您使用這些稍後在 Azure 中設定 App Service 應用程式。
   
   > [!IMPORTANT]
   > 用戶端密碼是重要的安全性認證。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。
   > 
   > 
9. 用來註冊應用程式的 Facebook 帳戶是應用程式的系統管理員。 此時，只有系統管理員可以登入此應用程式。 若要驗證其他 Facebook 帳戶，按一下**應用程式審查**並啟用**進行\<您的應用程式名稱 > 公用**使用 Facebook 驗證的一般公用存取。

## <a name="secrets"> </a>將 Facebook 資訊加入應用程式
1. 登入[Azure 入口網站]並瀏覽至您的 App Service 應用程式。 按一下 [設定]   > [驗證/授權]  ，並確定 [App Service 驗證]  為 [開啟]  。
2. 按一下 [Facebook]  ，貼上先前取得的應用程式識別碼與應用程式密碼值，選擇性啟用應用程式需要的任何範圍，然後按一下 [確定]  。
   
    ![][0]
   
    App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。
3. (選擇性) 若要限制只有透過 Facebook 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作]  設為 [Facebook]  。 這會要求所有的要求都經過驗證，且所有未經驗證的要求會重新導向至 Facebook 以進行驗證。
4. 設定驗證完成時，按一下 [儲存]  。

現在，您已可在應用程式中使用 Facebook 進行驗證。

## <a name="related-content"> </a>相關內容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 開發人員]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 入口網站]: https://portal.azure.com/
