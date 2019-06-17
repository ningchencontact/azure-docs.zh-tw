---
title: 傳統型應用程式呼叫 web Api （應用程式註冊）-Microsoft 身分識別平台
description: 了解如何建置傳統型應用程式呼叫 web Api （應用程式註冊）
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ab2701a82da0b8f7bc4e23a3d947be905593e85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057226"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>傳統型應用程式呼叫 web Api-應用程式註冊

這篇文章包含應用程式註冊 specificities，是桌面應用程式。

## <a name="supported-accounts-types"></a>支援的帳戶類型

支援在桌面應用程式的帳戶類型取決於您想要啟動的經驗，並因此流向您想要使用。

### <a name="audience-for-interactive-token-acquisition"></a>互動式權杖取得的對象

如果您的桌面應用程式會使用互動式驗證，您可以登入使用者從任何[帳戶類型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>傳統型應用程式無訊息流程的對象

- 如果您想要使用整合式 Windows 驗證] 或 [使用者名稱/密碼，就必須在自己的租用戶 （LOB 開發人員），或在 Azure Active directory 組織 （ISV 案例） 中的使用者登入您的應用程式。 Microsoft 個人帳戶不支援這些驗證流程
- 如果您想要使用的裝置程式碼流程，您無法登入使用者使用他們的個人 Microsoft 帳戶尚未
- 如果您使用傳遞 B2C 授權單位和原則的社交身分識別登入使用者，您可以只使用互動式和使用者名稱密碼驗證。

## <a name="redirect-uris"></a>重新導向 URI

再次重新導向 Uri 以用於桌面應用程式將取決於您想要使用的流程。

- 如果您使用**互動式驗證**或是**裝置程式碼流程**，您會想要使用`https://login.microsoftonline.com/common/oauth2/nativeclient`。 您會在對應的 URL，即可達成這項組態**驗證**一節以取得您的應用程式
  
  > [!IMPORTANT]
  > MSAL.NET 現在預設會在 Windows 上執行的桌面應用程式使用另一個重新導向 URI (`urn:ietf:wg:oauth:2.0:oob`)。 未來我們會想要變更此預設值，並因此我們建議您改用 `https://login.microsoftonline.com/common/oauth2/nativeclient`

- 如果您的應用程式只使用整合式 Windows 驗證，使用者名稱/密碼，您不需要註冊您的應用程式的重新導向 URI。 事實上，這些流程 Microsoft 身分識別平台 v2.0 端點之間往返進行您的應用程式將不會傳回呼叫任何特定的 uri。 
- 為了區分裝置程式碼流程，整合式 Windows 驗證和使用者名稱/密碼從機密用戶端應用程式流程，它沒有重新導向 Uri 可能是 （用戶端認證流程精靈應用程式中使用），您需要 express您的應用程式是公用的用戶端應用程式。 這項設定之後，即可移至**驗證**您的應用程式，然後在  區段**進階設定**子區段中，選擇 **是**，問題**視為公開用戶端應用程式**(在**預設用戶端類型**段落)

  ![允許公開用戶端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 權限

桌面應用程式代表登入的使用者呼叫 Api。 他們必須要求委派的權限。 它們無法要求應用程式權限 (這只會處理[精靈應用程式](scenario-daemon-overview.md))

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [傳統型應用程式-應用程式設定](scenario-desktop-app-configuration.md)
