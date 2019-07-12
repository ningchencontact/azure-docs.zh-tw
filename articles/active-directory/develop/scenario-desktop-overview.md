---
title: 呼叫 web Api （概觀）-Microsoft 身分識別平台的桌面應用程式
description: 了解如何建置傳統型應用程式呼叫 web Api （概觀）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476703b52813e6b3081dcfb3ab5a2fb4f3a7bfc5
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785626"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>案例：呼叫 Web API 的傳統型應用程式

了解您只需要建置傳統型應用程式呼叫 web Api

## <a name="prerequisites"></a>先決條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>使用者入門

如果您還沒有這麼做，請遵循.NET desktop 快速入門或 UWP 快速入門中建立第一個應用程式：

> [!div class="nextstepaction"]
> [快速入門：取得權杖，然後從 Windows 傳統型應用程式呼叫 Microsoft Graph API](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [快速入門：取得權杖，然後從 UWP 應用程式呼叫 Microsoft Graph API](./quickstart-v2-uwp.md)

## <a name="overview"></a>總覽

撰寫桌面的應用程式，而且想要使用者登入您的應用程式，並呼叫 web Api，例如 Microsoft Graph、 其他 Microsoft Api 或您自己的 web API。 您有幾個可能性：

- 您可以使用互動式權杖取得：

  - 如果桌面應用程式支援圖形化的控制項，例如是否 Windows.Form 應用程式或 WPF 應用程式。
  - 如果它是.NET Core 應用程式，而且您同意已與 Azure AD 的驗證互動發生在系統瀏覽器中

- 對於裝載的 Windows 應用程式，此外，也可以加入 Windows 網域的電腦上執行的應用程式或 AAD 加入以使用整合式 Windows 驗證以無訊息模式取得權杖。
- 最後，雖然不建議，您可以使用使用者名稱/密碼公用用戶端應用程式。 仍需要在某些情況下 （例如 DevOps)，但請注意，使用它將會造成您的應用程式的條件約束。 比方說，它無法登入的使用者需要執行多重要素驗證 （條件式存取） 的使用者。 也您的應用程式不會受益於單一登入 (SSO)。

  它也會針對新式驗證的原則，並僅針對因舊版因素而提供。

  ![桌面應用程式](media/scenarios/desktop-app.svg)

- 如果您要撰寫可移植的命令列工具-可能的.NET Core 應用程式在 Linux 或 Mac 上執行-和您接受委派的驗證，以系統瀏覽器，您將能夠使用互動式驗證。 (.NET core 不會提供尚未[網頁瀏覽器](https://aka.ms/msal-net-uses-web-browser)且因此驗證發生時系統瀏覽器中)，否則會傳回最佳的選項在此情況下是使用裝置程式碼流程。 此流程也可用於沒有瀏覽器中，例如 IoT 應用程式的應用程式

  ![Browserless 應用程式](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>詳細資料

桌面應用程式有數 specificities，主要取決於您的應用程式是否使用互動式驗證。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [傳統型應用程式-應用程式註冊](scenario-desktop-app-registration.md)
