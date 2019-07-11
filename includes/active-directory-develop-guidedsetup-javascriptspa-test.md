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
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133232"
---
## <a name="test-your-code"></a>測試您的程式碼

藉由使用下列其中一個環境，來測試您的程式碼。

### <a name="test-with-nodejs"></a>以 Node.js 進行測試

如果您不是使用 Visual Studio，請確定您的 web 伺服器已啟動。

1. 將伺服器設定為接聽以您 *index.html* 檔案位置為基礎的 TCP 通訊埠。 對於 Node.js，請從應用程式資料夾在命令提示字元中執行命令，以啟動網頁伺服器來接聽此連接埠：

    ```bash
    npm install
    node server.js
    ```
1. 在您的瀏覽器中，輸入 **http://\<span>\</span>localhost:30662** or **http://\<span>\</span>localhost:{port}** ，其中 port  是您的網頁伺服器正在接聽的連接埠。 您應該會看到 index.html  檔案和 [登入]  按鈕。

### <a name="test-with-visual-studio"></a>使用 Visual Studio 進行測試

如果您使用 Visual Studio，請選取專案解決方案並且選取 F5 來執行專案。 瀏覽器會開啟至 http://<span></span>localhost:{port} 位置，而且您應該會看到 [登入]  按鈕。

## <a name="test-your-application"></a>測試您的應用程式

在瀏覽器載入您的 index.html  檔案之後，請選取 [登入]  。 系統會提示您使用 Microsoft 身分識別平台端點登入：

![JavaScript SPA 帳戶登入視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>同意應用程式存取

您第一次登入應用程式時，系統會提示您提供您設定檔的存取權，並且將您登入：

![[要求的權限] 視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>檢視應用程式結果

登入之後，會在頁面上顯示以 Microsoft Graph API 回應傳回的使用者設定檔資訊。

![Microsoft Graph API 呼叫的結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 user.read  範圍才能讀取使用者的設定檔。 根據預設，在註冊入口網站上註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，Microsoft Graph API 需要 *Calendars.Read* 範圍才能列出使用者的行事曆。

為了在應用程式內容中存取使用者的行事曆，請將 Calendars.Read  委派權限新增至應用程式註冊資訊。 接著，將 Calendars.Read  範圍新增至 `acquireTokenSilent` 呼叫。

>[!NOTE]
>系統可能會在您增加範圍數目時，提示使用者同意其他事項。

如果後端 API 不需要範圍 (不建議)，您可以在呼叫中使用 clientId  作為範圍以取得權杖。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
