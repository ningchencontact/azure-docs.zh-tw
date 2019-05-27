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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121636"
---
## <a name="register-your-application"></a>註冊您的應用程式

您可以使用兩種方式之一來註冊您的應用程式。

### <a name="option-1-express-mode"></a>選項 1：快速模式

執行下列動作，即可快速註冊您的應用程式：
1. 移至 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)。
1. 輸入應用程式的名稱，並選取 [註冊]。
1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。

### <a name="option-2-advanced-mode"></a>選項 2：進階模式

若要註冊您的應用程式並將應用程式註冊資訊新增到您的解決方案，請執行下列作業：
1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. 選取 [新增註冊]。
   - 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `Win-App-calling-MsGraph`。
   - 在 [支援的帳戶類型] 區段中，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]，例如 Skype、Xbox、Outlook.com。
   - 選取 [註冊] 以建立應用程式。
1. 在應用程式頁面清單中，選取 [驗證]。
   1. 在「重新導向 URI」清單的 [重新導向 URI] 區段中：
   1. 在 [型別] 欄中，選取 [公開用戶端 (行動裝置及桌上型電腦)]。
   1. 在 [重新導向 URI] 欄中輸入 `urn:ietf:wg:oauth:2.0:oob`。
1. 選取 [ **儲存**]。
1. 移至 Visual Studio，開啟 App.xaml.cs 檔案，然後將 `Enter_the_Application_Id_here` 取代為您剛剛註冊及複製的應用程式 ID。

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
