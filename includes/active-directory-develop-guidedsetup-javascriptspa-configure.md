---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133858"
---
## <a name="register-your-application"></a>註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 如果您的帳戶可讓您存取多個租用戶，請在右上方選取帳戶，然後將您的入口網站工作階段設定為想要使用的 Azure AD 租用戶。
1. 移至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. [註冊應用程式]  頁面出現時，輸入您應用程式的名稱。
1. 在 [支援的帳戶類型]  底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]  。
1. 在 [重新導向 URI]  區段底下的下拉式清單中，選取 [Web]  平台，然後根據您的網頁伺服器，將值設定為應用程式的 URL。 

   如需在 Visual Studio 和 Node.js 中設定及取得重新導向 URL 的詳細資訊，請參閱接下來兩個章節。

1. 選取 [註冊]  。
1. 在應用程式 [概觀]  頁面上，記下 [應用程式 (用戶端) 識別碼]  值以供稍後使用。
1. 本快速入門需要啟用[隱含授與流程](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md)。 在所註冊應用程式的左側窗格中，選取 [驗證]  。
1. 在 [進階設定]  的 [隱含授與]  底下，選取 [識別碼權杖]  和 [存取權杖]  核取方塊。 因為此應用程式必須將使用者登入並呼叫 API，所以識別碼權杖和存取權杖都是必要權杖。
1. 選取 [ **儲存**]。

> #### <a name="set-a-redirect-url-for-nodejs"></a>設定 Node.js 的重新導向 URL
> 對於 Node.js，您可以在 server.js  檔案中設定網頁伺服器連接埠。 此教學課程會使用連接埠 30662 作為參考，但您可以使用任何其他可用的連接埠。 
>
> 若要在應用程式註冊資訊中設定重新導向 URL，請切換回 [應用程式註冊]  窗格，並且執行下列其中一項作業：
>
> - 將 `http://localhost:30662/`  設定為 [重新導向 URL]  。
> - 如果您使用自訂 TCP 連接埠，請使用 `http://localhost:<port>/`  (其中 \<port>  是自訂 TCP 連接埠號碼)。
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>設定 Visual Studio 的重新導向 URL
> 若要取得適用於 Visual Studio 的重新導向 URL，請執行下列作業：
> 1. 在 [方案總管]  中，選取專案。
>
>    [屬性]  視窗隨即開啟。 如果未開啟，請按下 **F4**。
>
>    ![[JavaScriptSPA 專案屬性] 視窗](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. 複製 [URL]  值。
 
> 1. 切換回 [應用程式註冊]  窗格，並貼上複製的值作為 [重新導向 URL]  。

#### <a name="configure-your-javascript-spa"></a>設定您的 JavaScript SPA

1. 在於專案設定期間建立的 index.html  檔案中，新增應用程式註冊資訊。 在檔案頂端的 `<script></script>` 標記內，新增下列程式碼：

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    其中：
    - *\<Enter_the_Application_Id_here>* 是您所註冊應用程式的**應用程式 (用戶端) 識別碼**。
    - *\<Enter_the_Tenant_info_here>* 設定為下列其中一個選項：
       - 如果您的應用程式支援 [此組織目錄中的帳戶]  ，請將此值取代為 [租用戶識別碼]  或 [租用戶名稱]  (例如，contoso.microsoft.com  )。
       - 如果您的應用程式支援 [任何組織目錄中的帳戶]  ，請將此值取代為 [組織]  。
       - 如果您的應用程式支援 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]  ，請將此值取代為 [通用]  。 若要將支援範圍限制為 [僅限個人 Microsoft 帳戶]  ，請將此值取代為 [取用者]  。
