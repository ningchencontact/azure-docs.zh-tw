---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121821"
---
## <a name="register-your-application"></a>註冊您的應用程式

若要註冊應用程式並將應用程式註冊資訊新增到解決方案，您有兩個選項可以選擇：

### <a name="option-1-express-mode"></a>選項 1：快速模式

執行下列動作，即可快速註冊您的應用程式：

1. 移至新的 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)窗格。
1. 輸入您的應用程式名稱，然後按一下 [註冊]。
1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。

### <a name="option-2-advanced-mode"></a>選項 2：進階模式

若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：

1. 前往 Visual Studio，以及：
   1. 在 [方案總管] 中，選取專案並查看 [屬性] 視窗 (如果您沒有看到 [屬性] 視窗，請按 F4)。
   1. 將 [SSL 已啟用] 變更為 `True`。
   1. 在 Visual Studio 中的專案上按一下滑鼠右鍵，然後選擇 [屬性] 和 [Web] 索引標籤。在 [伺服器] 區段中，將 [專案 URL] 變更為 SSL URL。
   1. 複製 SSL URL。 在下一個步驟中，將這個 URL 新增到註冊入口網站的重新導向 URL 清單：<br/><br/>![專案屬性](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. 選取 [新增註冊]。
1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   1. 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `ASPNET-Tutorial`。
   1. 在 [回覆 URL] 中，新增您透過步驟 1 從 Visual Studio 中複製的 SSL URL (例如`https://localhost:44368/`)，然後按一下 [註冊]。
1. 選取 [驗證] 功能表、在 [隱含授與] 下方設定 [識別碼權杖]，然後選取 [儲存]。
1. 在位於根資料夾之 `web.config` 的 `configuration\appSettings` 區段下新增下列內容：

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. 以您剛剛註冊的「應用程式識別碼」取代 `ClientId`。
1. 以您專案的 SSL URL 取代 `redirectUri`。
