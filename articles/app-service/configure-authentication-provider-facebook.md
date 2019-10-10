---
title: 設定 Facebook 驗證 - Azure App Service
description: 瞭解如何為您的 App Service 應用程式設定 Facebook 驗證
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: fb8497f3b9b887e2fd06b350bcc25ac8faaa7b43
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177000"
---
# <a name="configure-your-app-service-app-to-use-facebook-login"></a>將您的 App Service 應用程式設定為使用 Facebook 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文說明如何將 Azure App Service 設定為使用 Facebook 做為驗證提供者。

若要完成本文中的程式，您需要有已驗證電子郵件地址和行動電話號碼的 Facebook 帳戶。 若要建立新的 Facebook 帳戶，請前往 [facebook.com]。

## <a name="register"> </a>向 Facebook 註冊您的應用程式

1. 前往[Facebook 開發人員]網站，並使用您的 Facebook 帳號憑證登入。

   如果您沒有適用于開發人員的 Facebook 帳戶，請選取 [**開始**使用]，並遵循註冊步驟。
1. 選取**我的應用程式** >  [**新增新的應用程式**]。
1. 在 [**顯示名稱**] 欄位中：
   1. 輸入應用程式的唯一名稱。
   1. 提供您的**連絡人電子郵件**。
   1. 選取 [**建立應用程式識別碼**]。
   1. 完成安全性檢查。

   新 Facebook 應用程式的開發人員儀表板隨即開啟。
1. 選取 [**儀表板**]  > **Facebook 登**入  > **設定** > **Web**。
1. 在左側導覽的 [ **Facebook 登**入] 底下，選取 [**設定**]。
1. 在 [**有效的 OAuth 重新導向 uri** ] 欄位中，輸入 `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`。 請記得以您的 Azure App Service 應用程式名稱取代 `<app-name>`。
1. 選取 [Save Changes] \(儲存變更\)。
1. 在左窗格中，選取 [**設定**] [ > ] [**基本**]。 
1. 在 [**應用程式密碼**] 欄位中，選取 [**顯示**]。 複製 [**應用程式識別碼**] 和 [**應用程式密碼**] 的值。 您稍後會使用它們來設定 Azure 中的 App Service 應用程式。

   > [!IMPORTANT]
   > 用戶端密碼是重要的安全性認證。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。
   >

1. 您用來註冊應用程式的 Facebook 帳戶是應用程式的系統管理員。 此時，只有系統管理員可以登入此應用程式。

   若要驗證其他 Facebook 帳戶，請選取 [**應用程式審核**]，並啟用 [ **\<your-應用程式名稱 > 公用**]，以啟用一般公用，以使用 Facebook 驗證來存取應用程式。

## <a name="secrets"> </a>將 Facebook 資訊加入應用程式

1. 登入[Azure 入口網站]，然後流覽至您的 App Service 應用程式。
1. 選取 [**設定**] [ >  個**驗證/授權**]，並確定**App Service 驗證**已**開啟**。
1. 選取 [ **Facebook**]，然後貼上您先前取得的 [應用程式識別碼] 和 [應用程式密碼] 值。 啟用應用程式所需的任何範圍。
1. 選取 [確定]。

   ![行動應用程式 Facebook 設定的螢幕擷取畫面][0]

    根據預設，App Service 會提供驗證，但不會限制對您網站內容和 Api 的已授權存取。 您需要在您的應用程式程式碼中授權使用者。
1. 選擇性若要限制只有透過 Facebook 驗證的使用者才能存取，請將 [**要求未經驗證時所採取的動作**] 設定為 [ **facebook**]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 它也會將所有未經驗證的要求重新導向至 Facebook 以進行驗證。

   > [!CAUTION]
   > 以這種方式限制存取適用于對您應用程式的所有呼叫，這對於具有公開可用首頁的應用程式（如許多單頁應用程式），可能不是理想的做法。 對於這類應用程式，可能會慣用 [**允許匿名要求（無動作）** ]，讓應用程式手動啟動驗證。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]。

您現在已經準備好在應用程式中使用 Facebook 進行驗證。

## <a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 開發人員]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 入口網站]: https://portal.azure.com/
