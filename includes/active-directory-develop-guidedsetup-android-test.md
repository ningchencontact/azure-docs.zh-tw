---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: e87a63a0dad5e1f93b1bea62039abee8ded78ab7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988310"
---
## <a name="test-your-app"></a>測試應用程式

1. 針對您的裝置/模擬器執行程式碼。
2. 嘗試使用 Azure Active Directory 帳戶 (公司或學校帳戶) 或 Microsoft account (live.com、outlook.com) 登入。 

    ![測試您的應用程式](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![輸入使用者名稱和密碼](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>同意您的應用程式

使用者第一次登入您的應用程式時，會提示他們同意您的應用程式所需的權限，如下所示： 

![同意應用程式存取](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)

### <a name="success"></a>成功！

當您登入並同意之後，應用程式就會顯示來自 Microsoft Graph API 的回應。 此特定呼叫的目標是 **/me** 端點，而且它會傳回[使用者設定檔](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get)。 如需其他 Microsoft Graph 端點清單，請參閱 [Microsoft Graph API 開發人員文件](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries) \(英文\)。

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>範圍與委派的權限

Microsoft Graph API 需要 *User.Read* 範圍才能讀取使用者的設定檔。 在應用程式註冊入口網站註冊的每個應用程式中，都會自動新增此範圍。 其他 API 將需要額外的範圍。 例如，Microsoft Graph API 需要 *Calendars.Read* 範圍才能列出使用者的行事曆。

若要存取使用者的行事曆，請將 Calendars.Read 委派權限新增到應用程式註冊資訊。 接著，將 Calendars.Read 範圍新增至 `acquireTokenSilent` 呼叫。 

> [!NOTE]
> 若您變更您的應用程式註冊，您的使用者可能會收到提示，要求其提供額外的同意。

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
