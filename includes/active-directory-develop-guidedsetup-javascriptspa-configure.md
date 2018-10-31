---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: b3d46e10facdef26b36c910a5c23b40a415a2894
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988397"
---
## <a name="register-your-application"></a>註冊您的應用程式

有多種方式可以註冊應用程式。 請選取最符合您需求的選項：
* [快速模式 - 使用 SPA 快速入門來設定應用程式](#option-1-register-your-application-express-mode)
* [進階模式 - 手動設定應用程式設定](#option-2-register-your-application-advanced-mode)

### <a name="option-1-register-your-application-express-mode"></a>選項 1：註冊您的應用程式 (快速模式)

1. 登入 [Azure 入口網站應用程式註冊 (預覽)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) 以註冊應用程式。
1. 在 [註冊應用程式] 頁面上，輸入您應用程式的名稱。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
1. 完成時，選取 [註冊]。
1. 依照快速入門指示，只需按一下即可為您下載並自動設定新的應用程式。

### <a name="option-2-register-your-application-advanced-mode"></a>選項 2：註冊您的應用程式 (進階模式)

1. 登入 [Azure 入口網站](https://portal.azure.com/)以註冊應用程式。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取您的帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 在左側導覽窗格中，選取 [Azure Active Directory] 服務，然後選取 [應用程式註冊 (預覽)] > [新增註冊]。
1. [註冊應用程式] 頁面出現時，輸入您應用程式的名稱。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
1. 在 [重新導向 URI] 區段底下，選取 [Web] 平台，然後根據您的 Web 伺服器，將值設定為應用程式的 URL。 如需有關如何設定及取得 Visual Studio 和 Node 中重新導向 URL 的相關指示，請參閱下列各節。
1. 完成時，選取 [註冊]。
1. 在應用程式 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 值。
1. 本快速入門需要啟用[隱含授與流程](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md)。 在所註冊應用程式的左側導覽窗格中，選取 [驗證]。
1. 在 [進階設定] 的 [隱含授與] 底下，啟用 [識別碼權杖] 和 [存取權杖] 核取方塊。 識別碼權杖和存取權杖都是必要權杖，因為此應用程式必須將使用者登入並呼叫 API。
1. 選取 [ **儲存**]。

> #### <a name="setting-the-redirect-url-for-node"></a>設定 Node 的重新導向 URL
> 對於 Node.js，您可以在 server.js 檔案中設定網頁伺服器連接埠。 此教學課程會使用連接埠 30662 作為參考，但您可以使用任何其他可用的連接埠。 請遵循下列指示以在應用程式註冊資訊中設定重新導向 URL：<br/>
> - 切換回 [應用程式註冊]，然後將 `http://localhost:30662/` 設定為 `Redirect URL`，或如果您使用自訂的 TCP 連接埠，請使用 `http://localhost:[port]/` (其中 *[port]* 是自訂的 TCP 連接埠號碼)。

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>取得重新導向 URL 的 Visual Studio 指示
> 請遵循下列步驟以取得重新導向 URL：
> 1. 在 [方案總管] 中，選取專案並查看 [屬性] 視窗。 如果您沒有看到 [屬性] 視窗，請按 **F4**。
> 2. 將此值從 **URL** 複製到剪貼簿：<br/> ![專案屬性](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. 切換回 [應用程式註冊]，然後將值設定為 [重新導向 URL]。

#### <a name="configure-your-javascript-spa"></a>設定您的 JavaScript SPA

1. 在於專案設定期間建立的 `index.html` 檔案中，新增應用程式註冊資訊。 在 `index.html` 檔案主體頂端的 `<script></script>` 標記內，新增下列程式碼：

    ```javascript
    var applicationConfig = {
        clientID: "[Enter the application Id here]",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
以您剛剛註冊的應用程式識別碼取代 <code>Enter the application Id here</code>。
</li>
</ol>
