---
title: 呼叫 web Api 的桌面應用程式（應用程式註冊）-Microsoft 身分識別平臺
description: 瞭解如何建立呼叫 web Api 的傳統型應用程式（應用程式註冊）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 648652ed32a5dea30de665b7fa49190171a7f10a
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268398"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>呼叫 web Api 的桌面應用程式-應用程式註冊

本文包含傳統型應用程式的 [應用程式註冊] specificities。

## <a name="supported-accounts-types"></a>支援的帳戶類型

桌面應用程式中支援的帳戶類型取決於您想要亮起的體驗。 基於此關聯性，支援的帳戶類型取決於您想要使用的流程。

### <a name="audience-for-interactive-token-acquisition"></a>取得互動式權杖的物件

如果您的桌面應用程式使用互動式驗證，則可以從任何[帳戶類型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登入使用者。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面應用程式無訊息流程的物件

- 若要使用整合式 Windows 驗證或使用者名稱/密碼，您的應用程式必須在您自己的租使用者（LOB 開發人員）或 Azure Active directory 組織（ISV 案例）中登入使用者。 Microsoft 個人帳戶不支援這些驗證流程。
- 如果您想要使用裝置程式碼流程，則無法使用他們的 Microsoft 個人帳戶登入使用者。
- 如果您使用社交身分識別來登入使用者，並傳遞 B2C 授權單位和原則，您只能使用互動式和使用者名稱密碼驗證。

## <a name="redirect-uris"></a>重新導向 URI

桌面應用程式中所使用的重新導向 Uri 將取決於您想要使用的流程。

- 如果您使用的是**互動式驗證**或**裝置程式碼流程**，您會想要`https://login.microsoftonline.com/common/oauth2/nativeclient`使用。 在應用程式的 [**驗證**] 區段中按一下對應的 URL，即可達到此設定。
  
  > [!IMPORTANT]
  > 今日 MSAL.NET 預設會在 Windows 上執行的桌面應用程式中，使用`urn:ietf:wg:oauth:2.0:oob`另一個重新導向 URI （）。 在未來，我們會想要變更此預設值，因此建議您使用`https://login.microsoftonline.com/common/oauth2/nativeclient`

- 如果您要為 macOS 建立原生的目標 C 或 Swift 應用程式，您會想要根據應用程式的套件組合識別碼，以下列格式註冊 redirectUri： **msauth。 < 您的 app.config。 id >：//auth** （replace <應用程式套件組合識別碼 > 識別碼）
- 如果您的應用程式只使用整合式 Windows 驗證或使用者名稱/密碼，您就不需要為應用程式註冊重新導向 URI。 這些流程會執行 Microsoft 身分識別平臺 v2.0 端點的來回行程，而您的應用程式將不會在任何特定的 URI 上被回呼。
- 若要區分裝置程式碼流程、整合式 Windows 驗證和使用者名稱/密碼，來自不具重新導向 Uri 的機密用戶端應用程式流程（用於 daemon 應用程式的用戶端認證流程），您需要表達應用程式是公用用戶端應用程式。 若要達到此設定，請移至應用程式的 [**驗證**] 區段。 然後，在 [**高級設定**] 子區段的 [**預設用戶端類型**] 段落中，針對 [將**應用程式視為公用用戶端**] 問題選擇 [**是]** 。

  ![允許公用用戶端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 權限

桌面應用程式會呼叫已登入使用者的 Api。 他們需要要求委派的許可權。 不過，他們無法要求應用程式許可權，而只會在[守護程式應用程式](scenario-daemon-overview.md)中處理。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [桌面應用程式-應用程式設定](scenario-desktop-app-configuration.md)
